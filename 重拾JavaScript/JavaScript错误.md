### 1. Error对象
当JavaScript引擎执行JavaScript代码时，可能会发生各种错误。当错误发生时，JavaScript引擎通常会停止，并生成一个错误消息。描述这种情况的技术术语是：JavaScript将抛出一个错误对象（`Error`实例）。

`Error`实例上一般有以下信息：
![Error 实例](https://img.imgdb.cn/item/5ff84a713ffa7d37b33bacb3.jpg)

1. message： 错误提示信息
2. name： 错误名称
3. stack： 错误的堆栈

JavaScript基于`Error`类定义了以下几种子类错误类型：
- SyntaxError
- ReferenceError
- TypeError
- RangeError

#### 1.1 SyntaxError
代码中出现语法错误：
```js
cont a;
// ⓧ Uncaught SyntaxError: Unexpected identifier

1a+[\]]]]
// ⓧ Uncaught SyntaxError: Invalid or unexpected token

null = 2
// ⓧ Uncaught SyntaxError: Invalid left-hand side in assignment

console.log 'hello');
// ⓧ Uncaught SyntaxError: Unexpected string
```

以上均是在语法解析阶段发生的语法错误。

#### 1.2 ReferenceError
引用错误：
```js
const { name } = obj
// ⓧ Uncaught ReferenceError: obj is not defined

b = a + 1
// ⓧ Uncaught ReferenceError: a is not defined
```

访问或操作一个不存在的变量时发生引用错误。

#### 1.3 TypeError
类型错误：
```js
const a = 1
a()
// ⓧ Uncaught TypeError: a is not a function

const b = 2
b = 3
// ⓧ Uncaught TypeError: Assignment to constant variable.
```

变量或参数不是预期类型时发生类型错误。

#### 1.4 RangeError
```js
new Array(-1)
// ⓧ Uncaught RangeError: Invalid array length

function foo() {
  foo()
}
foo()
// ⓧ Uncaught RangeError: Maximum call stack size exceeded
```

值超出有效范围或者堆栈溢出时产生`RangeError`。

这里只总结了四种常见类型的错误，其他类型的错误就不再一一列举了。

### 2. 异常
你可能会认为，错误和异常是一回事——实际上，**一个错误对象只有在被抛出时才成为异常**。

#### 2.1 抛出异常
一般情况下，正如上文给出的例子，JavaScript代码执行出错时会**主动**抛出异常，而我们也可以通过`throw`**手动**抛出异常：
```js
throw Error('手动抛出错误')
throw SyntaxError('手动抛出语法错误')

// 也可以抛出非错误对象
throw(3)
throw('非错误对象')
```

对于JavaScript引擎来说，遇到异常，程序就中止了：
```js
// 开发手动抛异常
throw(3)
console.log(3) // 不会被执行

// 主动抛异常
consol.log(a.b)
console.log(3) // 不会被执行
```

#### 2.2 try...catch 捕获异常
当业务代码执行到某一步出错终止时，视图以及用户的交互逻辑很可能会出现超出预期的异常，因此我们需要对可能执行出错的代码进行特殊的处理。

在JavaScript中，我们可以通过`try ...catch`进行异常的捕获：
```js
try {
  f();
} catch(e) {
  // 处理错误
} finally {
  // 总是执行
}
console.log(222) // 仍然可以执行
```

上面代码中，如果函数`f`执行出错，会被`catch`捕获，程序会继续向下执行。

值得注意的是，`try...catch`无法捕获语法错误：
![try catch无法捕获语法错误](https://img.imgdb.cn/item/600bb54c3ffa7d37b397d55b.jpg)

好在，语法错误在开发阶段就可以被发现，所以我们一般也不需要对语法错误使用`try...catch`。

#### 2.3 异步异常
除了语法错误，异步任务中抛出的异常也无法被`try...catch`捕获：
![异步异常](https://img.imgdb.cn/item/600bb4ee3ffa7d37b397ae3a.jpg)

知道事件循环、栈的概念的话，这个就很好理解——定时器注册的回调执行时`try...catch`上下文已经被销毁了。

Promise异常：
![Promise异常](https://img.imgdb.cn/item/600d1c103ffa7d37b33cf99e.jpg)

**`try catch`无法捕获Promise内部抛出的异常**，Promise内部异常需要通过`promise.catch`进行捕获：
![Promise异常捕获](https://img.imgdb.cn/item/600d20fb3ffa7d37b33ef095.jpg)

所以，对于异步任务，Promise内的异常使用`Promise.catch`进行异常，而异步回调比如定时器则可以将回调的逻辑通过`try...catch`包裹：
```js
try {
  setTimeout(function () {
    a()
  }, 1000)
} catch (err) {
  console.log('这里不会执行')
}
// 改写为
setTimeout(function () {
  try {
    a()
  } catch (err) {
    console.log('这里可以捕获到' err)
  }
}, 1000)
```

### 总结
这里大概总结了JavaScript的`Error`对象和几种基于Error的子类，同步异常和异步异常的捕获策略。

### 参考
1. [错误处理机制](https://javascript.ruanyifeng.com/grammar/error.html)
