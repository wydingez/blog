title: 手写一个Promise
author: wyding
tags:
  - js
  - ''
categories:
  - 面试
cover: >-
  https://wyd-mages.oss-cn-shanghai.aliyuncs.com/43853b2056090306d47874aa3ddece00.jpg
date: 2019-11-14 16:27:00
---
> 手写一个`Promise`😄，[参考文档](https://juejin.im/post/5b2f02cd5188252b937548ab)
 
 <!-- more -->
 ## 1.简单`promise`实现(只能在同步代码中执行)
 ```js
 class Promise {
 	constructor (exector) {
    	this.state = 'pending'
        this.value = undefined
        this.reason = undefined
        
        let resolve = value => {
        	if (this.state === 'pending') {
            	this.state = 'fulfilled'
                this.value = value
            }
        }
        
        let reject = reason => {
        	if (this.state === 'pending') {
            	this.state = 'rejected'
                this.reason = reason
            }
        }
        
        try {
        	exector(resolve, reject)
        } catch(e) {
        	reject(e)
        }
    }
    
    then (onFullfilled, onRejected) {
    	if (this.state === 'fulfilled') {
        	onFullfilled(this.value)
        }
        
        if (this.state === 'rejected') {
        	onRejected(this.reason)
        }
    }
 }
 ```
 
 ## 2.解决异步实现(需要将`callback`存入一个数组，一旦`reject/resolve`就依次调用它们)
 ```js
 class Promise {
 	constructor (exector) {
    	this.state = 'pending'
        this.value = undefined
        this.reason = undefined
        
        this.onResolvedCallbacks = []
        this.onRejectedCallbacks = []
        
        let resolve = value => {
        	if (this.state === 'pending') {
            	this.state = 'fulfilled'
                this.value = value
                
                this.onResolvedCallbacks.forEach(fn => fn())
            }
        }
        
        let reject = reason => {
        	if (this.state === 'pending') {
            	this.state = 'rejected'
                this.reason = reason
                
                this.onRejectedCallbacks.forEach(fn => fn())
            }
        }
        
        try {
        	exector(resolve, reject)
        } catch(e) {
        	reject(e)
        }
    }
    
    then (onResolved, onRejected) {
    	if (this.state === 'fulfilled') {
        	onResolved(this.value)
        }
        
        if (this.state === 'rejected') {
        	onRejected(this.reason)
        }
        
        if (this.state === 'pending') {
        	this.onResolvedCallbacks.push(() => {
            	onResolved(this.value)
            })
            this.onRejectedCallbacks.push(() => {
            	onRejected(this.reason)
            })
        }
    }
 }
 ```
 
 ## 3.完整代码
 ```js
 class Promise{
  constructor(executor){
    this.state = 'pending';
    this.value = undefined;
    this.reason = undefined;
    this.onResolvedCallbacks = [];
    this.onRejectedCallbacks = [];
    let resolve = value => {
      if (this.state === 'pending') {
        this.state = 'fulfilled';
        this.value = value;
        this.onResolvedCallbacks.forEach(fn=>fn());
      }
    };
    let reject = reason => {
      if (this.state === 'pending') {
        this.state = 'rejected';
        this.reason = reason;
        this.onRejectedCallbacks.forEach(fn=>fn());
      }
    };
    try{
      executor(resolve, reject);
    } catch (err) {
      reject(err);
    }
  }
  then(onFulfilled,onRejected) {
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
    onRejected = typeof onRejected === 'function' ? onRejected : err => { throw err };
    let promise2 = new Promise((resolve, reject) => {
      if (this.state === 'fulfilled') {
        setTimeout(() => {
          try {
            let x = onFulfilled(this.value);
            resolvePromise(promise2, x, resolve, reject);
          } catch (e) {
            reject(e);
          }
        }, 0);
      };
      if (this.state === 'rejected') {
        setTimeout(() => {
          try {
            let x = onRejected(this.reason);
            resolvePromise(promise2, x, resolve, reject);
          } catch (e) {
            reject(e);
          }
        }, 0);
      };
      if (this.state === 'pending') {
        this.onResolvedCallbacks.push(() => {
          setTimeout(() => {
            try {
              let x = onFulfilled(this.value);
              resolvePromise(promise2, x, resolve, reject);
            } catch (e) {
              reject(e);
            }
          }, 0);
        });
        this.onRejectedCallbacks.push(() => {
          setTimeout(() => {
            try {
              let x = onRejected(this.reason);
              resolvePromise(promise2, x, resolve, reject);
            } catch (e) {
              reject(e);
            }
          }, 0)
        });
      };
    });
    return promise2;
  }
  catch(fn){
    return this.then(null,fn);
  }
}
function resolvePromise(promise2, x, resolve, reject){
  if(x === promise2){
    return reject(new TypeError('Chaining cycle detected for promise'));
  }
  let called;
  if (x != null && (typeof x === 'object' || typeof x === 'function')) {
    try {
      let then = x.then;
      if (typeof then === 'function') { 
        then.call(x, y => {
          if(called)return;
          called = true;
          resolvePromise(promise2, y, resolve, reject);
        }, err => {
          if(called)return;
          called = true;
          reject(err);
        })
      } else {
        resolve(x);
      }
    } catch (e) {
      if(called)return;
      called = true;
      reject(e); 
    }
  } else {
    resolve(x);
  }
}
//resolve方法
Promise.resolve = function(val){
  return new Promise((resolve,reject)=>{
    resolve(val)
  });
}
//reject方法
Promise.reject = function(val){
  return new Promise((resolve,reject)=>{
    reject(val)
  });
}
//race方法 
Promise.race = function(promises){
  return new Promise((resolve,reject)=>{
    for(let i=0;i<promises.length;i++){
      promises[i].then(resolve,reject)
    };
  })
}
//all方法(获取所有的promise，都执行then，把结果放到数组，一起返回)
Promise.all = function(promises){
  let arr = [];
  let i = 0;
  function processData(index,data){
    arr[index] = data;
    i++;
    if(i == promises.length){
      resolve(arr);
    };
  };
  return new Promise((resolve,reject)=>{
    for(let i=0;i<promises.length;i++){
      promises[i].then(data=>{
        processData(i,data);
      },reject);
    };
  });
}

 ```