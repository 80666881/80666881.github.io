---
title: the usage of Promise
date: 2017-11-23
description: promise用法的简单介绍
categories:
 - promise
 - es6
photos: http://oxpvyb4d5.bkt.clouddn.com/promise.png
tags: es6 Promise 异步编程
---

>本文是本人学习时遇到Promise后，在网上查询资料及总结后的学习笔记。


### 什么是Promise？
看看MDN的定义：
[原文请点击](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)
>Promise 对象用于一个异步操作的最终完成（或失败）及其结果值的表示。(简单点说就是处理异步请求。。我们经常会做些承诺，如果我赢了你就嫁给我，如果输了我就嫁给你之类的诺言。这就是promise的中文含义。一个诺言，一个成功，一个失败。)

**我们再看看在控制台上Promise长什么样？**

![image.png](http://upload-images.jianshu.io/upload_images/593513-704d715fa9811dd9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*我们简单总结一下：*
1.Promise是一个构造函数，可以异步操作。
2.可以根据异步处理结果进行不同处理（success/error）
3.它包含了一些方法,(catch,then,reject,resolve...)

既然提及了根据不同异步结果，会有不同处理方法，那么我们如何知道呢？
就是根据状态，没错，Promise就是根据状态来识别该如何处理的。

**一个 Promise有以下几种状态:**
 - pending: 初始状态，不是成功或失败状态。
 - fulfilled: 意味着操作成功完成。
 - rejected: 意味着操作失败。

![image.png](http://upload-images.jianshu.io/upload_images/593513-bb71a3134e957336.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>而且Promise不能被重置，只能设置一次。
Once settled, a promise can not be resettled. Calling resolve() or reject() again will have no effect. The immutability of a settled promise is an important feature.

### Promise怎么用
#### resolve用法
```js
var myFirstPromise = new Promise(function(resolve, reject){
    //当异步代码执行成功时，我们才会调用resolve(...), 当异步代码失败时就会调用reject(...)
    //在本例中，我们使用setTimeout(...)来模拟异步代码，实际编码时可能是XHR请求或是HTML5的一些API方法.
    setTimeout(function(){
        resolve("成功!"); //代码正常执行！
    }, 250);
});

myFirstPromise.then(function(successMessage){
    //successMessage的值是上面调用resolve(...)方法传入的值.
    //successMessage参数不一定非要是字符串类型，这里只是举个例子
    console.log("Yay! " + successMessage);
});
```
 - 逻辑处理完毕并且没有错误时，resolve这个回调会将值传递到一个特殊的地方。这个特殊的地方在哪呢？就是下面代码中的then，我们使用then中的回调函数来处理resolve后的结果。比如上面的代码中，我们将值简单的输出到控制台。如果有错误，则reject到then的第二个回调函数中，对错误进行处理。

 - 注意！我只是new了一个对象，并没有调用它，我们传进去的函数就已经执行了，这是需要注意的一个细节。所以我们用Promise的时候一般是包在一个函数中，在需要的时候去运行这个函数，如：

```js
function p() {
    var myFirstPromise = new Promise(function (resolve, reject) {    
        setTimeout(function () {
            resolve("成功!"); //代码正常执行！
        }, 250);
    });
    return myFirstPromise
}
p().then(function (successMessage) {
    console.log("Yay! " + successMessage);
});
```

那我们返回的这个myFirstPromise有什么用呢？就是可以接着用then(),catch()等方法，这是Promise的精髓。
这和我们以前使用回调函数是相同的，有什么特殊呢?

```js
function runAsync(callback){
    setTimeout(function(){
        console.log('执行完成');
        callback('随便什么数据');
    }, 2000);
}

runAsync(function(data){
    console.log(data);
});
```

那么问题来了，有多层回调该怎么办？如果callback也是一个异步操作，而且执行完后也需要有相应的回调函数，该怎么办呢？总不能再定义一个callback2，然后给callback传进去吧。而Promise的优势在于，可以在then方法中继续写Promise对象并返回，然后继续调用then来进行回调操作。

用Promise我们可以这么写。

```js
function runAsync1(){
    var p = new Promise(function(resolve, reject){
        //做一些异步操作
        setTimeout(function(){
            console.log('异步任务1执行完成');
            resolve('随便什么数据1');
        }, 1000);
    });
    return p;            
}
function runAsync2(){
    var p = new Promise(function(resolve, reject){
        //做一些异步操作
        setTimeout(function(){
            console.log('异步任务2执行完成');
            resolve('随便什么数据2');
        }, 2000);
    });
    return p;            
}
function runAsync3(){
    var p = new Promise(function(resolve, reject){
        //做一些异步操作
        setTimeout(function(){
            console.log('异步任务3执行完成');
            resolve('随便什么数据3');
        }, 2000);
    });
    return p;            
}


runAsync1()//return a Promise object
.then(function(data){
    console.log(data);
    return runAsync2();
})
.then(function(data){
    console.log(data);
    return runAsync3();
})
.then(function(data){
    console.log(data);
});
```

结果如下：

![image.png](http://upload-images.jianshu.io/upload_images/593513-445948826c0c6e00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当然，直接返回数据也可以类似在函数里写入resolve('')，传给下面的回调函数。
```
          runAsync1()
            .then(function (data) {
                console.log(data);
                return '直接传数据';
            })
            .then(function (data) {
                console.log(data);
                return runAsync3();
            })
            .then(function (data) {
                console.log(data);
            });
```

结果：
![image.png](http://upload-images.jianshu.io/upload_images/593513-2f3a74436fb8704a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####reject用法

接下来说说reject用法.
reject的作用就是把Promise的状态置为rejected，这样我们在then中就能捕捉到，然后执行‘失败’函数（此时我们调用的时then()传入的第二个函数）。所以这个状态是我们标记的。

```js
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <script>
        function getNumber() {
            var p = new Promise(function (resolve, reject) {
                //做一些异步操作
                setTimeout(function () {
                    var num = Math.ceil(Math.random() * 10); //生成1-10的随机数
                    if (num <= 5) {
                        resolve(num);
                    } else {
                        reject('数字是'+num+',太大了');
                    }
                }, 2000);
            });
            return p;
        }

        getNumber()
            .then(
                function (data) {
                    console.log('resolved');
                    console.log(data);
                },
                function (reason, data) {
                    console.log('rejected');
                    console.log(reason);
                }
            );
    </script>
</body>

</html>
```

结果

![image.png](http://upload-images.jianshu.io/upload_images/593513-62222107937589a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### catch用法

我们知道Promise对象除了then方法，还有一个catch方法，它是做什么用的呢？其实它和then的第二个参数一样，用来指定reject的回调，用法是这样：

```
getNumber()
.then(function(data){
    console.log('resolved');
    console.log(data);
})
.catch(function(reason){
    console.log('rejected');
    console.log(reason);
});
```

这和我们上面在then里传入第二个函数效果是一样的。
不过它还有另外一个作用：在执行resolve的回调（也就是上面then中的第一个参数）时，如果抛出异常了（代码出错了），那么并不会报错卡死js，而是会进到这个catch方法中。请看下面的代码：

```js
getNumber()
.then(function(data){
    console.log('resolved');
    console.log(data);
    console.log(somedata); //此处的somedata未定义
})
.catch(function(reason){
    console.log('rejected');
    console.log(reason);
}).then(function(){
    console.log('试着能不能继续运行')
});

```

结果

![image.png](http://upload-images.jianshu.io/upload_images/593513-0ce7ea5511a02fa1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们看到第一个回调运行时产生错误以后，会把错误抛给catch()，然后处理后后面的程序会继续运行，而不会停止。这是catch的好处.
否则会直接在控制台报错，后面也停止了。

![image.png](http://upload-images.jianshu.io/upload_images/593513-ca6c9d007ddc864e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### all的用法

Promise的all方法提供了并行执行异步操作的能力，并且在所有异步操作执行完后才执行回调。我们仍旧使用上面定义好的runAsync1、runAsync2、runAsync3这三个函数，看下面的例子：

```js
Promise
.all([runAsync1(), runAsync2(), runAsync3()])
.then(function(results){
    console.log(results);
});

```
结果

![image.png](http://upload-images.jianshu.io/upload_images/593513-13aaa0756dd36e46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，我们把三个函数并行执行时，把各自返回的结果存在一个数组里，再把这个数组作为参数，传递给then。

以上~~~



