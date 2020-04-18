# Promise处理异步任务

`ES6`引入了`promise`用于更简单的处理异步任务。

`promise`对象是对我们现在尚未得到但将来会得到值的占位符；它是对我们最终能够得知异步计算结果的一种保证。如果我们兑现了承诺，那结果会得到一个值。如果发生了问题，结果则是一个错误，一个为什么不能交付的接口。

创建一个简单的`promise`:

~~~javascript
const ninjaPromise = new Promise((resolve, reject) => {
    resolve("Hattori");
    //reject("An error resolving a promise!");
});

ninjaPromise.then(ninja => {
    assert(ninja === "Hattori", "We were promised Hattori!");
}, err => {
    fail("There shouldn't be an error");
});
~~~

`promise`构造函数需要传入一个执行函数，它包含两个参数`resolve`和`reject`。当把两个内置函数`resolve`和`reject`作为参数传入`promise`构造函数后，执行函数会立刻调用。通过调用传入的`resolve`函数，一个`promise`将被成功兑现；通过调用`reject`则`promise`被拒绝。

在一个`promise`对象上使用`then`方法后可以传入两个回调函数，`promise`成功兑现后会调用第一个回调函数，出现错误时则调用第二个回调函数。

## 1 简单回调函数所带来的问题

**第一个问题：错误处理困难。**执行长时间任务时，为了避免应用程序阻塞，通常提供一个回调函数，当任务结束后会调用该回调函数。问题在于当回调函数发生错误时，无法用内置语言结构来处理。导致这个问题的原因在于，当长时间任务开始运行，调用回调函数的代码一般不会和开始任务中的这段代码位于事件循环的同一步骤。导致的结果就是，错误经常会丢失。

**第二个问题：执行连续步骤非常棘手。**当执行一个长时间运行的任务后，我们经常希望用获取的数据来做些什么。这会导致开始另一项长期运行的任务，该任务最后又会触发另一个长期运行的任务，如此一来导致了互相依赖的一系列异步回调任务。结果可能就是写了一堆嵌套的回调函数用来表明需要执行的一系列步骤。还会意识到这样的代码难以理解，向其中再插入几步简直是一种痛苦，增加错误处理也会大大增加代码的复杂度。

~~~javascript
getJSON('data/ninjas.json', function(err, ninjas) {
    getJSON(ninjas[0].location, function(err, locationInfo) {
        sendOrder(locationInfo, function(err, status) {
            ....
        })
    })
})
~~~



**第三个问题：执行很多并行任务很棘手。**有时候得到最终结果的这些步骤并不相互依赖，所以我们不必让它们按顺序执行，可以并行地执行这些任务。每个任务结束后，都检查看看是否是最后一段缺失的数据。最后，当所有的数据都获取到了，就能立刻开始执行计划了。我们依然不得不书写很多样板代码仅仅用于并行执行多个行动。

~~~javascript
var ninjas, mapInfo, plan;
$.get("data/ninjas.json", function(err, data) {
    if(err) { processError(err); return; }
    ninjas = data;
    actionItemArrived();
})
$.get("data/mapInfo.json", function(err, data) {
    if(err) { processError(err); return; }
    mapInfo = data;
    actionItemArrived();
})
$.get("data/plan.json", function(err, data) {
    if(err) { processError(err); return; }
    plan = data;
    actionItemArrived();
})
function actionItemArrived() {
    if(ninjas != null && mapInfo != null && plan != null) {
        console.log("....");
    }
}
~~~

## 2 深入研究`Promise`

一个`promise`对象从等待(`pending`)状态开始，此时我们对承诺的值一无所知。因此一个等待状态的`promise`对象也称为未实现(`unresovled`)的`promise`。在程序执行的过程中，如果`promise`的`resolve`函数被调用，`promise`就会进入完成(`fulfilled`)状态，在该状态下我们能够成功获取到承诺的值。如果`promise`的`reject`函数被调用，或者如果一个未处理的异常在`promise`调用的过程中发生了，`promise`就会进入到拒绝状态，尽管在该状态下我们无法获取承诺的值，但我们至少知道了原因。

一旦某个`promise`进入到完成态或者拒绝态，它的状态都不能再切换了。

`promise`的执行顺序：

~~~javascript
report("At code start");

var ninjaDelayedPromise = new Promise((resolve, reject) => {
    report("ninjaDelayedPromise executor");
    setTimeout(() => {
        report("Resolving ninjaDelayedPromise");
        resolve("Hattori");
    }, 500)
});

assert(ninjaDelayedPromise !== null, "After creating ninjaDelayedPromise");

ninjaDelayedPromise.then(ninja => {
    assert(ninja === "Hattori", "ninjaDelayedPromise resolve handled with Hattori");
});

const ninjaImmediatePromise = new Promise((resolve, reject) => {
    report("ninjaImmediatePromise executor. Immediate resolve.");
    resolve("Yoshi");
});

ninjaImmediatePromise.then(ninja => {
    assert(ninja === "Yoshi", "ninjaImmediatePromise resolve handled with Yoshi");
});

report("At code end");
~~~

执行结果：

~~~javascript
At code start
ninjaDelayedPromise executor
After creating ninjaDelayedPromise
ninjaImmediatePromise executor. Immediate resolve.
At code end
ninjaImmediatePromise resolve handled with Yoshi
Resolving ninjaDelayedPromise
ninjaDelayedPromise resolve handled with Hattori
~~~

`Promise`是设计用来处理异步任务的，所以`JavaScript`引擎经常会凭借异步处理使`promise`的行为得以预见。`JavaScript`通过在本次事件循环中的所有代码都执行完毕后，调用then回调函数来处理`promise`。因此首先是日志`At code end`,然后才是`ninjaImmediatePromise resolve handled with Yoshi`。

## 3 拒绝`promise`

拒绝一个`promise`有两种方式：显示拒绝，即在一个`promise`的执行函数中调用传入的`reject`方法；隐式拒绝，正处理一个`promise`的过程中抛出一个异常。

**显示拒绝`promise`：**

~~~javascript
const promise = new Promise((resolve, reject) => {
    reject("Explicitly reject a promise!");
});
promise.then(() => fail("Happy path, won't be called"),
            error => pass("A promise was explicitly rejected."));
~~~

**链式调用catch方法：**

~~~javascript
var promise = new Promise((resolve, reject) => {
    reject("Explicitly reject a promise!");
});
promise.then(() => fail("Happy path, won't be called"))
    .catch(() => pass("Promise was also rejected"));
~~~

通过在`then`方法后链式调用`catch`方法，我们同样可以在`promise`进入被拒绝状态时为其提供错误回调函数。

**异常隐式拒绝一个`promise`：**

~~~javascript
const promise = new Promise((resolve, reject) => {
    undeclaredVariable++; // 该变量未定义，将抛出异常
});
promise.then(() => fail("Happy path, won't be called"))
    .catch(error => pass("Third promise was also rejected"));
~~~

无论`promise`是被如何拒绝的，显示调用`reject`方法还是隐式调用，只要发生了异常，所有错误和拒绝原因都会在拒绝回调函数中被定位。

## 4 链式调用`promise`

调用`promise`的`then`方法后还会再返回一个新的`promise`对象。

~~~javascript
getJSON("data/ninjas.json")
.then(ninjas => getJSON(ninjas[0].missionsUrl))
.then(missions => getJSON(missions[0].detailsUrl))
.then(mission => assert(mission !== null, "Ninja mission obtained"))
.catch(error => fail("An error has occurred"));
~~~

如果错误在前面的任何一个`promise`中产生，`catch`方法就会捕捉到它。如果没发生任何错误，则程序流程只会无障碍地继续通过。

## 5 等待多个`promise`

~~~javascript
Promise.all([getJSON("data/ninjas.json"),
           getJSON("data/mapInfo.json"),
           getJSON("data/plan.json")]).then(results => {
    const ninjas = results[0], mapInfo = results[1], plan = results[2];
    assert(ninjas !== undefined && mapInfo !== undefined && plan !== undefined, 
          "The plan is ready to be set in motion!");
}).catch(error => {
    fail("A problem in carrying out our plan");
})
~~~

通过使用内置方法`Promise.all`可以等待多个`promise`。这个方法将一个`promise`数组作为参数，然后创建一个新的`promise`对象，一旦数组中的`promise`全部被解决，这个返回的`promise`就会被解决，而一旦其中有一个`promise`失败了，那么整个`promise`对象也会被拒绝。后续的回调函数接受成功值组成的数组，数组中的每一项都对应`promise`数组中的对应项。

`Promise.all`方法等待列表中的所有`promise`。`Promise.race`只关心第一个成功或失败的`promise`。