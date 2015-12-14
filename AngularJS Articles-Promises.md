这是我在翻阅资料的时候，在国外的thinkster上找到的一篇关于关于angular中promise的一篇资料教程，写的非常好，收益良多，我现在翻译一下，供大家学习交流，如果大家想深入学习，欢迎到thinkster注册，并且付费。<br>
下面是正文：<br>
angular的promise是由$q提供和构件的，$q提供了一个通过注册一个promise项目来异步执行的方法。Promise作为ES6的一部分规范形成了原生javascript。angular的$q服务提供了一个新的接口，方便与以后移植到ES6。<br>
code
```
<html>
<head>
           <tittle>Promise fun</tittle>
</head>
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.0-beta.5/angular.min.js"></script> 
<script src="app.js"></script> 
<body ng-app="app">
 </body>
</html>
```
```
function getData($timeout, $q) {
  return function() {
    var defer = $q.defer()
    // simulated async function
    $timeout(function() {
    }, 2000)
    return defer.promise
  }
}
angular.module('app', [])
.factory('getData', getData)//把getData方法进入factory进行实例化，方便进去run方法进行配置
.run(function(getData) {//run方法用于快速配置，注入完成后启动（只能是实例化的服务或者参数可以配置）
  var promise = getData()
})
```
为了简单起见，我们使用angular的$timeout服务模拟异步函数，尤其是讲解AJAX使用angular的$HTTP服务，是最常用使用promise的地方

##异步对象
延迟对象是一个简单的对象当为了解决这个promise对象暴露出一个promise对象，它是使用$q.defer()方法来构建，并且暴露出三个主要的方法，resolve() reject()和notify();这个相关联的promise对象能够通过promise对象属性来访问.
```
function getData(){
  return function(){
      var defer=$q.defer();
      $timeout(function(){
            defer.resolve("data received");
       },2000);
      return defer.promise;
  }
}
```
这里创建了一个promise对象，然后返回promise对象的属性，执行异步函数当函数完成后，我们解析这个延迟对象，这个resoleve()函数的参数会被传递到回调函数中。

##Promise对象
现在我们获得了一个promise对象（defer.promise），让我们注册一个回调函数，当异步函数完成后执行。使用then()方法，附加一个回调函数，用来返回promise对象用来打印返回的string对象。
```
.run(function(getData){
    var promise=getData()
    .then(function(){
        console.log(string);
    })
})
```
##拒绝一个promise对象
我们已经见过如何成功解析一个promise对象，但是当异步函数失败的时候会发生什么，相比于使用Resolve()方法，我们称reject()方法如果当失败的时候使用。
使用Math.random()来模拟失败然后返回这个promise对象，一半的机会。
```
function getData($timeout,$q){
    var defer=$q.defer();
    $timeout(function(){
         if(Math.round(Math.rondom())){
            defer.solve('data received!')
          }else{
            defer.reject('oh no an error! try again');  
           }
    },2000)
    return defer;
}
```
then()方法的第二个参数是一个可选的发生错误时候的回调函数，当这个promise对象失败的时候调用。
给then()传入第二个作为执行发生错误时的回调函数。
```
.run(function(getData){
      var promise=getData()
      .then(function(string){
          console.log(string)
      },function(error){
          console.log(error);
      })
})
```
##使用$q构造函数
使用$q服务本身能够快速变换一个基于异步函数的回调函数，形成一种快速解决方案。
重写这个模拟的异步函数用来返回一个promise对象使用$q函数。
```
function($timeout,$q){
    return function(){
        return $q(function(resolve,reject){
            $timeout(function(){
                if(Math.ronund(Math.random())){
                    resolve('data received!')
                 }else{
                    reject('oh no an error! try again')
                  }
             },2000)
        })
    }
}
```
这个方法完成了同样的事情，手动的创建了一个延迟对象，
##finally()方法
promise对象只能成功执行函数或者失败回调函数能够被执行，但不是全部。但是当你需要确保这个方法执行忽略promise的结果。你可以通过使用finally()来注册这个方法，当对于一个已知状态的时候，很有用。
使用这个finally()方法来打印相应时间间隔，当异步函数完成时。
```
.run(function(getData) {
  var promise = getData()
    .then(function(string) {
      console.log(string)
    }, function(error) {
      console.error(error)
    })
    .finally(function() {
      console.log('Finished at:', new Date())
    })
})
```
现在你可以看到现在时间被打印出来，忽略了promise执行的状态。
##promise链
最有魅力的promise特性就是能够使用使用链链接起来。它允许数据通过这个链流动，并且每一步进行操作和突变。
简单的示例：
现在修改我们的异步函数，用来传递一个0-9之间的随机数，到resolve()函数
```
function getData($timeout, $q) {
  return function() {
    // simulated async function
    return $q(function(resolve, reject) {
      $timeout(function() {
        resolve(Math.floor(Math.random() * 10))
      }, 2000)
    })
  }
}
```
当页面刷新，可以看到一个随机的interger数字在0-9之间跳动。
为了开始链式调用，我们必须修改这个回调函数返回一个value。
修改这个promise调用的回调函数用来返回一个随机数乘以2。
```
.run(function(getData) {
  var promise = getData()
    .then(function(num) {
      console.log(num)
      return num * 2
    })
})
```
现在我们能够链式调用另外一个回调函数对象使用then()方法，能够在第一个回调函数被执行后返回，这个值传入第二个回调函数被乘以2。
```
.run(function(getData) {
  var promise = getData()
    .then(function(num) {
      console.log(num)
      return num * 2
    })
    .then(function(num) {
      console.log(num) // = random number * 2
    })
})
```
这个例子虽然简单，但是说明了一个强大的概念，此外，不仅仅是从promise回调返回一个简单的值，能够返回新的promise对象。这个作用域链会暂停，知道返回这个回调成功。这样允许把多个异步回调串在一起。

下面是原文的连接，如果大家喜欢，可以到原网站学习。[think]https://thinkster.io/a-better-way-to-learn-angularjs/promises

本文可以随意转载，但需注明出处。[here]http://www.jianshu.com/p/df257dfbc8fe






























