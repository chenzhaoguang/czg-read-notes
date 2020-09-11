# 脑图👇
![image](https://github.com/chenzhaoguang/czg-read-notes/blob/master/book/img/web前端面试/脑图.jpg)

## 1.手动实现一个浅克隆
浅克隆：只拷贝对象或数组的第一层内容


```
const shallClone = (target) => {
  if (typeof target === 'object' && target !== null) {
    const cloneTarget = Array.isArray(target) ? [] : {};
    for (let prop in target) {
      if (target.hasOwnProperty(prop)) { // 遍历对象自身可枚举属性（不考虑继承属性和原型对象）
        cloneTarget[prop] = target[prop];
    }
    return cloneTarget;
  } else {
    return target;
  }
}
```
## 2.手动实现一个深克隆（简易版）
深克隆：层层拷贝对象或数组的每一层内容

```
function deepClone(target)  {
  if (target === null) return null;
  if (typeof target !== 'object') return target;

  const cloneTarget = Array.isArray(target) ? [] : {};
  for (let prop in target) {
    if (target.hasOwnProperty(prop)) {
      cloneTarget[prop] = deepClone(target[prop]);
    }
  }
  return cloneTarget;
}

```
## 3.手动实现一个深克隆(考虑日期/正则等特殊对象 和 解决循环引用情况)

```
const isObject = (target) => (typeof target === 'object' || typeof target === 'function') && target !== null;

function deepClone (target, map = new Map()) {
  // 先判断该引用类型是否被 拷贝过
  if (map.get(target)) {
    return target;
  }

  // 检测当前对象target是否与 正则、日期格式对象匹配
  if (/^(RegExp|Date)$/i.test(target.constructor.name)){
    new constructor(target);
  }

  if (isObject(target)) {
    map.set(target, true); // 为循环引用的对象做标记
    const cloneTarget = Array.isArray(target) ? [] : {};
    for (let prop in target) {
      if (target.hasOwnProperty(props)) {
        cloneTarget[prop] = deepClone(target[props], map);
      }
    }
    return cloneTarget;
  } else {
    return target;
  }
}

```
## 4.手动实现instanceOf的机制
思路：

​ 步骤1：先取得当前类的原型，当前实例对象的原型链

​ 步骤2：一直循环（执行原型链的查找机制）

- 取得当前实例对象原型链的原型链（==proto = proto.`____proto____`==，沿着原型链一直向上查找）
- 如果 当前实例的原型链 ==`__proto__`== 上找到了当前类的原型prototype，则返回 true
- 如果 一直找到==Object.prototype. `____proto____` == null==，Object的基类(null)上面都没找到，则返回 false

```
function _instanceof (instanceObject, classFunc) {
    let classFunc = classFunc.prototype; // 取得当前类的原型
  let proto = instanceObject.__proto__; // 取得当前实例对象的原型链
  
  while (true) {
    if (proto === null) { // 找到了 Object的基类 Object.prototype.__proto__
      return false;
        };
    if (proto === classFunc) { // 在当前实例对象的原型链上，找到了当前类
      return true;
    }
    proto = proto.__proto__; // 沿着原型链__ptoto__一层一层向上查找
  }
}

```
## 优化版 (处理兼容问题)
> Object.getPrototypeOf ( )：用来获取某个实例对象的原型（内部[[prototype]]属性的值，包含proto属性）



```
function _instanceof (instanceObject, classFunc) {
    let classFunc = classFunc.prototype; // 取得当前类的原型
  let proto = Object.getPrototypeOf(instanceObject); // 取得当前实例对象的原型链上的属性
  
  while (true) {
    if (proto === null) { // 找到了 Object的基类 Object.prototype.__proto__
      return false;
        };
    if (proto === classFunc) { // 在当前实例对象的原型链上，找到了当前类
      return true;
    }
    proto = Object.getPrototypeOf(proto); // 沿着原型链__ptoto__一层一层向上查找
  }
}

```
## 5. 手动实现防抖函数
> 实现函数的防抖（目的是频繁触发中只执行一次）

#### 以最后一次触发为标准


```
/**
 * 实现函数的防抖（目的是频繁触发中只执行一次）
 * @param {*} func 需要执行的函数
 * @param {*} wait 检测防抖的间隔频率
 * @param {*} immediate 是否是立即执行  True：第一次，默认False：最后一次
 * @return {可被调用执行的函数}
 */
function debounce(func, wati = 500, immediate = false) {
  let timer = null
  return function anonymous(... params) {

    clearTimeout(timer)
    timer = setTimeout(_ => {
      // 在下一个500ms 执行func之前，将timer = null
      //（因为clearInterval只能清除定时器，但timer还有值）
      // 为了确保后续每一次执行都和最初结果一样，赋值为null
      // 也可以通过 timer 是否 为 null 是否有定时器
      timer = null
      func.call(this, ...params)
    }, wait)

  }
}

```
#### 以第一次触发为标准


```
/**
 * 实现函数的防抖（目的是频繁触发中只执行一次）
 * @param {*} func 需要执行的函数
 * @param {*} wait 检测防抖的间隔频率
 * @param {*} immediate 是否是立即执行 True：第一次，默认False：最后一次
 * @return {可被调用执行的函数}
 */
function debounce(func, wait = 500, immediate = true) {
  let timer = null
  return function anonymous(... params) {

    // 第一点击 没有设置过任何定时器 timer就要为 null
    let now = immediate && !timer
    clearTimeout(timer)
    timer = setTimeout(_ => {
      // 在下一个500ms 执行func之前，将timer = null
      //（因为clearInterval只能在系统内清除定时器，但timer还有值）
      // 为了确保后续每一次执行都和最初结果一样，赋值为null
      // 也可以通过 timer 是否 为 null 是否有定时器
      timer = null!immediate ? func.call(this, ...params) : null
    }, wait)
    now ? func.call(this, ...params) : null

  }
}

function func() {
  console. log('ok')
}
btn. onclick = debounce(func, 500)

```
## 6.手动实现节流函数
> 实现函数的节流 （目的是频繁触发中缩减频率）
#### 带注释说明版
- 【第一次触发：reamining是负数，previous被赋值为当前时间】
- 【第二次触发：假设时间间隔是500ms，第一次执行完之后，20ms之后，立即触发第二次，则remaining = 500 - ( 新的当前时间 - 上一次触发时间 ) = 500 - 20 = 480 】

```
/**
 * 实现函数的节流 （目的是频繁触发中缩减频率）
 * @param {*} func 需要执行的函数
 * @param {*} wait 检测节流的间隔频率
 * @param {*} immediate 是否是立即执行 True：第一次，默认False：最后一次
 * @return {可被调用执行的函数}
 */
function throttle(func, wait) {
    let timer = null
  let previous = 0  // 记录上一次操作的时间点

  return function anonymous(... params) {
    let now = new Date()  // 当前操作的时间点
    remaining = wait - (now - previous) // 剩下的时间
    if (remaining <= 0) {
      // 两次间隔时间超过频率，把方法执行
      
      clearTimeout(timer); // clearTimeout是从系统中清除定时器，但timer值不会变为null
      timer = null; // 后续可以通过判断 timer是否为null，而判断是否有 定时器
      
      // 此时已经执行func 函数，应该将上次触发函数的时间点 = 现在触发的时间点 new Date()
      previous = new Date(); // 把上一次操作时间修改为当前时间
      func.call(this, ...params);
    } else if(!timer){ 
      
      // 两次间隔的事件没有超过频率，说明还没有达到触发标准，设置定时器等待即可（还差多久等多久）
      // 假设事件间隔为500ms，第一次执行完之后，20ms后再次点击执行，则剩余 480ms，就能等待480ms
      timer = setTimeout( _ => {
        clearTimeout(timer)
        timer = null // 确保每次执行完的时候，timer 都清 0，回到初始状态
        
        //过了remaining时间后，才去执行func，所以previous不能等于初始时的 now
        previous = new Date(); // 把上一次操作时间修改为当前时间
        func.call(this, ...params)；
      }, remaining)
    }
  }
}

function func() {
  console. log('ok')
}
btn. onclick = throttle(func, 500)

```
#### 不带注释版
```
/**
 * 实现函数的节流 （目的是频繁触发中缩减频率）
 * @param {*} func 需要执行的函数
 * @param {*} wait 检测节流的间隔频率
 * @param {*} immediate 是否是立即执行 True：第一次，默认False：最后一次
 * @return {可被调用执行的函数}
 */
function throttle(func, wait) {
    let timer = null;
  let previous = 0;  

  return function anonymous(... params) {
    let now = new Date(); 
    remaining = wait - (now - previous);
    if (remaining <= 0) {
      clearTimeout(timer); 
      timer = null;
      previous = new Date();
      func.call(this, ...params);
    } else if(!timer){ 
      timer = setTimeout( _ => {
        clearTimeout(timer);
        timer = null; 
        previous = new Date();
        func.call(this, ...params)；
      }, remaining)
    }
  }
}

function func() {
  console. log('ok')
}
btn. onclick = throttle(func, 500);

```
## 7.手动实现Object.create
```
Object.create() = function create(prototype) {
  // 排除传入的对象是 null 和 非object的情况
    if (prototype === null || typeof prototype !== 'object') {
    throw new TypeError(`Object prototype may only be an Object: ${prototype}`);
    }
  // 让空对象的 __proto__指向 传进来的 对象(prototype)
  // 目标 {}.__proto__ = prototype
  function Temp() {};
  Temp.prototype = prototype;
  return new Temp;
}

```
## 8.手动实现内置new的原理
#### 简化版
- 步骤1：创建一个Func的实例对象（实例.__proto__ = 类.prototype）
- 步骤2：把Func 当做普通函数执行，并改变this指向
- 步骤3：分析函数的返回值

```
/**
  * Func: 要操作的类（最后要创建这个类的实例）
  * args：存储未来传递给Func类的实参
  */
function _new(Func, ...args) {
  
  // 创建一个Func的实例对象（实例.____proto____ = 类.prototype）
  let obj = {};
  obj.__proto__ = Func.prototype;
  
  // 把Func当做普通函数执行，并改变this指向
  let result = Func.call(obj, ...args);
  
  // 分析函数的返回值
  if (result !== null && /^(object|function)$/.test(typeof result)) {
    return result;
    }
  return obj;
}

```
#### 优化版
##### `__proto__` 在IE浏览器中不支持
```
let x = { name: "lsh" };
Object.create(x);

{}.__proto__ = x;

```

```
function _new(Func, ...args) {
  
  // let obj = {};
  // obj.__proto__ = Func.prototype;
  // 创建一个Func的实例对象（实例.____proto____ = 类.prototype）
  let obj = Object.create(Func.prototype);
  
  // 把Func当做普通函数执行，并改变this指向
  let result = Func.call(obj, ...args);
  
  // 分析函数的返回值
  if (result !== null && /^(object|function)$/.test(typeof result)) {
    return result;
  }
  return obj;
}


```
## 9.手动实现call方法
#### 简易版（不考虑context非对象情况，不考虑Symbol\BigInt 不能 new.constructor( context )情况）


```
/**
 * context: 要改变的函数中的this指向，写谁就是谁
 * args：传递给函数的实参信息
 * this：要处理的函数 fn
 */
Function.prototype.call = function(context, ...args) {
    //  null，undefined，和不传时，context为 window
  context = context == null ? window : context;
  
  let result;
  context['fn'] = this; // 把函数作为对象的某个成员值
  result = context['fn'](...args); // 把函数执行，此时函数中的this就是
    delete context['fn']; // 设置完成员属性后，删除
  return result;
}

```
#### 完善版（context必须对象类型，兼容Symbol等情况）

```
/**
 * context: 要改变的函数中的this指向，写谁就是谁
 * args：传递给函数的实参信息
 * this：要处理的函数 fn
 */
Function.prototype.call = function(context, ...args) {
    //  null，undefined，和不传时，context为 window
  context = context == null ? window : context;
  
  // 必须保证 context 是一个对象类型
  let contextType = typeof context;
  if (!/^(object|function)$/i.test(contextType)) {
    // context = new context.constructor(context); // 不适用于 Symbol/BigInt
    context = Object(context);
    }
  
  let result;
  context['fn'] = this; // 把函数作为对象的某个成员值
  result = context['fn'](...args); // 把函数执行，此时函数中的this就是
    delete context['fn']; // 设置完成员属性后，删除
  return result;
}


```
## 10.手动实现apply方法


```
/**
 * context: 要改变的函数中的this指向，写谁就是谁
 * args：传递给函数的实参信息
 * this：要处理的函数 fn
 */
Function.prototype.apply = function(context, args) {

  context = context == null ? window : context;
  
  let contextType = typeof context;
  if (!/^(object|function)$/i.test(contextType)) {
    context = Object(context);
    }
  
  let result;
  context['fn'] = this; 
  result = context['fn'](...args); 
    delete context['fn'];
  return result;
}

```
## 11.手动实现bind方法


```
/**
 * this: 要处理的函数 func
 * context: 要改变的函数中的this指向 obj
 * params：要处理的函数传递的实参 [10, 20]
 */
Function.prototype._bind = function(context, ...params) {
  
  let _this = this; // this: 要处理的函数
  return function anonymous (...args) {
    // args： 可能传递的事件对象等信息 [MouseEvent]
    // this：匿名函数中的this是由当初绑定的位置 触发决定的 （总之不是要处理的函数func）
    // 所以需要_bind函数 刚进来时，保存要处理的函数 _this = this
    _this.call(context, ...params.concat(args));
  }
}

```
## 12.ES5实现数组扁平化flat方法

思路：

- 循环数组里的每一个元素
- 判断该元素是否为数组
    - 是数组的话，继续循环遍历这个元素——数组
    - 不是数组的话，把元素添加到新的数组中
```
let arr = [
    [1, 2, 2],
    [3, 4, 5, 5],
    [6, 7, 8, 9, [11, 12, [12, 13, [14]]]], 10
];

function myFlat() {
  _this = this; // 保存 this：arr
  let newArr = [];
  // 循环arr中的每一项，把不是数组的元素存储到 newArr中
  let cycleArray = (arr) => {
    for (let i=0; i< arr.length; i++) {
      let item = arr[i];
      if (Array.isArray(item)) { // 元素是数组的话，继续循环遍历该数组
        cycleArray(item);
        continue;
      } else{
        newArr.push(item); // 不是数组的话，直接添加到新数组中
      }
    }
  }
  cycleArray(_this); // 循环数组里的每个元素
  return newArr; // 返回新的数组对象
}

Array.prototype.myFlat = myFlat;

arr = arr.myFlat(); // [1, 2, 2, 3, 4, 5, 5, 6, 7, 8, 9, 11, 12, 12, 13, 14, 10]

```
## 13.ES6实现数组扁平化flat方法

```
const myFlat = (arr) => {
  let newArr = [];
  let cycleArray = (arr) => {
    for(let i = 0; i < arr.length; i++) {
      let item = arr[i];
      if (Array.isArray(item)) {
        cycleArray(item);
        continue;
      } else {
        newArr.push(item);
      }
    }
  }
  cycleArray(arr);
  return newArr;
}

myFlat(arr); // [1, 2, 2, 3, 4, 5, 5, 6, 7, 8, 9, 11, 12, 12, 13, 14, 10]

```
## 14.使用reduce手动实现数组扁平化flat方法
根据 `Array.isArray` 逐个判断数组里的每一项是否为数组元素
```
const myFlat = arr => {
  return arr.reduce((pre, cur) => {
    return pre.concat(Array.isArray(cur) ? myFlat(cur) : cur);
  }, []);
};
console.log(myFlat(arr)); 
// [12, 23, 34, 56, 78, 90, 100, 110, 120, 130, 140]

```
## 15.用不同的三种思想实现数组去重？
#### 思想一：数组最后一项元素替换掉当前项元素，并删除最后一项元素
```
let arr = [12, 23, 12, 15, 25, 23, 16, 25, 16];

for(let i = 0; i < arr.length - 1; i++) {
  let item = arr[i]; // 取得当前数组中的每一项
  let remainArgs = arr.slice(i+1); // 从 i+1项开始截取数组中剩余元素，包括i+1位置的元素
  if (remainArgs.indexOf(item) > -1) { // 数组的后面元素 包含当前项
    arr[i] = arr[arr.length - 1]; // 用数组最后一项替换当前项
    arr.length--; // 删除数组最后一项
    i--; // 仍从当前项开始比较
  }
}

console.log(arr); // [ 16, 23, 12, 15, 25 ]

```
#### 思想二：新容器存储思想——对象键值对
> 思想：

> ​ 把数组元素作为对象属性

> ​ 通过遍历数组，判断数组元素是否已经是对象的属性，如果对象属性定义过，则证明是重复元素，进而删除重复元素

```
let obj = {};
for (let i=0; i < arr.length; i++) {
  let item = arr[i]; // 取得当前项
  if (typeof obj[item] !== 'undefined') {
    // obj 中存在当前属性，则证明当前项 之前已经是 obj属性了
    // 删除当前项
    arr[i] = arr[arr.length-1];
    arr.length--;
    i--;
  }
  obj[item] = item; // obj {10: 10, 16: 16, 25: 25 ...}
}
obj = null; // 垃圾回收
console.log(arr); // [ 16, 23, 12, 15, 25 ]

```
## 思想三：相邻项的处理方案思想——基于正则

```
let arr = [12, 23, 12, 15, 25, 23, 16, 25, 16];

arr.sort((a,b) => a-b);
arrStr = arr.join('@') + '@';
let reg = /(\d+@)\1*/g,
    newArr = [];
arrStr.replace(reg, (val, group1) => {
 // newArr.push(Number(group1.slice(0, group1.length-1)));
 newArr.push(parseFloat(group1));
})
console.log(newArr); // [ 12, 15, 16, 23, 25 ]

```
## 16.基于Generator函数实现async/await原理
> 核心：传递给我一个Generator函数，把函数中的内容基于Iterator迭代器的特点一步步的执行

```
function readFile(file) {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve(file);
    }, 1000);
    })
};

function asyncFunc(generator) {
    const iterator = generator(); // 接下来要执行next
  // data为第一次执行之后的返回结果，用于传给第二次执行
  const next = (data) => {
        let { value, done } = iterator.next(data); // 第二次执行，并接收第一次的请求结果 data
    
    if (done) return; // 执行完毕(到第三次)直接返回
    // 第一次执行next时，yield返回的 promise实例 赋值给了 value
    value.then(data => {
      next(data); // 当第一次value 执行完毕且成功时，执行下一步(并把第一次的结果传递下一步)
    });
  }
  next();
};

asyncFunc(function* () {
    // 生成器函数：控制代码一步步执行 
  let data = yield readFile('a.js'); // 等这一步骤执行执行成功之后，再往下走，没执行完的时候，直接返回
  data = yield readFile(data + 'b.js');
  return data;
})

```
## 17.基于Promise封装Ajax
思路：

- 返回一个新的Promise实例

- 创建HMLHttpRequest异步对象

- 调用open方法，打开url，与服务器建立链接（发送前的一些处理）

- 监听Ajax状态信息

- 如果xhr.readyState == 4（表示服务器响应完成，可以获取使用服务器的响应了）
    - xhr.status == 200，返回resolve状态
    - xhr.status == 404，返回reject状态
- xhr.readyState !== 4，把请求主体的信息基于send发送给服务器

```
function ajax(url, method) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest()
    xhr.open(url, method, true)
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        if (xhr.status === 200) {
          resolve(xhr.responseText)
        } else if (xhr.status === 404) {
          reject(new Error('404'))
        }
      } else {
        reject('请求数据失败')
      }
    }
    xhr.send(null)
  })
}

```
## 18.手动实现JSONP跨域
思路：

- 创建script标签
- 设置script标签的src属性，以问号传递参数，设置好回调函数callback名称
- 插入到html文本中
- 调用回调函数，res参数就是获取的数据

```
let script = document.createElement('script');

script.src = 'http://www.baidu.cn/login?username=JasonShu&callback=callback';

document.body.appendChild(script);

function callback (res) {
    console.log(res);
}

```
## 19.手动实现sleep
某个时间过后，就去执行某个函数，基于`Promise`封装异步任务
```
/**
 * 
 * @param {*} fn 要执行的函数
 * @param {*} wait 等待的时间
 */
function sleep(fn, wait) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(fn);
    })
  })
}

let sayHello = (name) => console.log(`hello ${name}`);

async function autoRun() {
  let demo1 = await sleep(sayHello(('时光屋小豪'), 1000));
  let demo2 = await sleep(sayHello(('掘友们'), 1000));
  let demo3 = await sleep(sayHello(('公众号的朋友们'), 1000));
};

autoRun();

```
## 20.ES5手动实现数组reduce
特点：
- 初始值不传时的特殊处理：会默认使用数组中的第一个元素
    - 函数的返回结果会作为下一次循环的prev
    - 回调函数一共接受四个参数（`arr.reduce(prev, next, currentIndex, array))`）
`prev`：上一次调用回调时返回的值
        - 正在处理的元素
        - 正在处理的元素的索引
        - 正在遍历的集合对象

```
Array.prototype.myReduce = function(fn, prev) {
  for (let i = 0; i < this.length; i++) {
    if (typeof prev === 'undefined') {
      prev = fn(this[i], this[i+1], i+1, this);
      ++i;
    } else {
      prev = fn(prev, this[i], i, this);
    }
  }
  return prev
}

```
测试用例

```
let sum = [1, 2, 3].myReduce((prev, next) => {
  return prev + next
});

console.log(sum); // 6
```
## 21.手动实现通用柯理化函数
柯理化函数含义：是给函数分步传递参数，每次传递部分参数，并返回一个更具体的函数接收剩下的参数，这中间可嵌套多层这样的接收部分参数的函数，直至返回最后结果。
```
// add的参数不固定，看有几个数字累计相加
function add (a,b,c,d) {
  return a+b+c+d
}

function currying (fn, ...args) {
  // fn.length 回调函数的参数的总和
  // args.length currying函数 后面的参数总和 
  // 如：add (a,b,c,d)  currying(add,1,2,3,4)
  if (fn.length === args.length) {  
    return fn(...args)
  } else {
    // 继续分步传递参数 newArgs 新一次传递的参数
    return function anonymous(...newArgs) {
      // 将先传递的参数和后传递的参数 结合在一起
      let allArgs = [...args, ...newArgs]
      return currying(fn, ...allArgs)
    }
  }
}

let fn1 = currying(add, 1, 2) // 3
let fn2 = fn1(3)  // 6
let fn3 = fn2(4)  // 10

```
## 23.ES5实现一个继承
> 寄生组合继承（ES5继承的最佳方式）

所谓寄生组合式继承，即通过借用构造函数来继承属性，通过原型链的形式来继承方法。

只调用了一次父类构造函数，效率更高。避免在子类.prototype上面创建不必要的、多余的属性，与其同时，原型链还能保持不变。

```
function Parent(name) {
  this.name = name;
  this.colors = ['red', 'blue', 'green'];
}
Parent.prototype.getName = function () {
  return this.name;
}

function Child(name, age) {
  Parent.call(this, name); // 调用父类的构造函数，将父类构造函数内的this指向子类的实例
  this.age = age;
}

//寄生组合式继承
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;

Child.prototype.getAge = function () {
    return this.age;
}

let girl = new Child('Lisa', 18);
girl.getName();

```
## 24.手动实现发布订阅
> 发布订阅的核心:： 每次event. emit（发布），就会触发一次event. on（注册）

```
class EventEmitter {
  constructor() {
    // 事件对象，存放订阅的名字和事件
    this.events = {};
  }
  // 订阅事件的方法
  on(eventName,callback) {
    if (!this.events[eventName]) {
      // 注意数据，一个名字可以订阅多个事件函数
      this.events[eventName] = [callback];
    } else  {
      // 存在则push到指定数组的尾部保存
      this.events[eventName].push(callback)
    }
  }
  // 触发事件的方法
  emit(eventName) {
    // 遍历执行所有订阅的事件
    this.events[eventName] && this.events[eventName].forEach(cb => cb());
  }
}

```
测试用例

```
let em = new EventEmitter();

function workDay() {
  console.log("每天工作");
}
function makeMoney() {
    console.log("赚100万");
}
function sayLove() {
  console.log("向喜欢的人示爱");
}
em.on("money",makeMoney);
em.on("love",sayLove);
em.on("work", workDay);

em.emit("money");
em.emit("love");  
em.emit("work");  

```

## 26.手动实现观察者模式
> 观察者模式（基于发布订阅模式） 有观察者，也有被观察者

==观察者需要放到被观察者中==，==被观察者的状态变化需要通知观察者== 我变化了 内部也是基于发布订阅模式，收集观察者，状态变化后要主动通知观察者

```
class Subject { // 被观察者 学生
  constructor(name) {
    this.state = '开心的'
    this.observers = []; // 存储所有的观察者
  }
  // 收集所有的观察者
  attach(o){ // Subject. prototype. attch
    this.observers.push(o)
  }
  // 更新被观察者 状态的方法
  setState(newState) {
    this.state = newState; // 更新状态
    // this 指被观察者 学生
    this.observers.forEach(o => o.update(this)) // 通知观察者 更新它们的状态
  }
}

class Observer{ // 观察者 父母和老师
  constructor(name) {
    this.name = name
  }
  update(student) {
    console.log('当前' + this.name + '被通知了', '当前学生的状态是' + student.state)
  }
}

let student = new Subject('学生'); 

let parent = new Observer('父母'); 
let teacher = new Observer('老师'); 

// 被观察者存储观察者的前提，需要先接纳观察者
student. attach(parent); 
student. attach(teacher); 
student. setState('被欺负了');

```
## 27.手动实现Object.freeze
`Object.freeze`冻结一个对象，让其不能再添加/删除属性，也不能修改该对象已有属性的==可枚举性、可配置可写==性，也不能修改已有属性的值和它的原型属性，最后返回一个和传入参数相同的对象。

```
function myFreeze(obj){
  // 判断参数是否为Object类型，如果是就封闭对象，循环遍历对象。去掉原型属性，将其writable特性设置为false
  if(obj instanceof Object){
    Object.seal(obj);  // 封闭对象
    for(let key in obj){
      if(obj.hasOwnProperty(key)){
        Object.defineProperty(obj,key,{
          writable:false   // 设置只读
        })
        // 如果属性值依然为对象，要通过递归来进行进一步的冻结
        myFreeze(obj[key]);  
      }
    }
  }
}

```
## 28.手动实现Promise.all
`Promise.all`：有一个promise任务失败就全部失败

`Promise.all` 方法返回的是一个`promise`

```
function isPromise (val) {
  return typeof val.then === 'function'; // (123).then => undefined
}

Promise.all = function(promises) {
  return new Promise((resolve, reject) => {
    let arr = []; // 存放 promise执行后的结果
    let index = 0; // 计数器，用来累计promise的已执行次数
    const processData = (key, data) => {
      arr[key] = data; // 不能使用数组的长度来计算
      /*
        if (arr.length == promises.length) {
          resolve(arr);  // [null, null , 1, 2] 由于Promise异步比较慢，所以还未返回
        }
      */
     if (++index === promises.length) {
      // 必须保证数组里的每一个
       resolve(arr);
     }
    }
    // 遍历数组依次拿到执行结果
    for (let i = 0; i < promises.length; i++) {
      let result = promises[i];
      if(isPromise(result)) {
        // 让里面的promise执行，取得成功后的结果
        // data promise执行后的返回结果
        result.then((data) => {
          // 处理数据，按照原数组的顺序依次输出
          processData(i ,data)
        }, reject)  // reject本事就是个函数 所以简写了
      } else {
        // 1 , 2
        processData(i ,result)
      }
    }
  })
}
测试用例


```

```
let fs = require('fs').promises;

let getName = fs.readFile('./name.txt', 'utf8');
let getAge = fs.readFile('./age.txt', 'utf8');

Promise.all([1, getName, getAge, 2]).then(data => {
    console.log(data); // [ 1, 'name', '11', 2 ]
})


```
## 29.手动实现Promise.allSettled
> MDN: Promise.allSettled()方法返回一个在所有给定的promise都已经fulfilled或rejected后的promise，并带有一个对象数组，每个对象表示对应的promise结果。

> 当您有多个彼此不依赖的异步任务成功完成时，或者您总是想知道每个promise的结果时，通常使用它。

【译】Promise.allSettled跟Promise.all类似, 其参数接受一个Promise的数组, 返回一个新的Promise, 唯一的不同在于, 其不会进行短路, 也就是说当Promise全部处理完成后我们可以拿到每个Promise的状态, 而不管其是否处理成功。

##### 用法 | 测试用例


```
let fs = require('fs').promises;

let getName = fs.readFile('./name.txt', 'utf8'); // 读取文件成功
let getAge = fs.readFile('./age.txt', 'utf8');

Promise.allSettled([1, getName, getAge, 2]).then(data => {
    console.log(data);
});
// 输出结果
/*
    [
    { status: 'fulfilled', value: 1 },
    { status: 'fulfilled', value: 'zf' },
    { status: 'fulfilled', value: '11' },
    { status: 'fulfilled', value: 2 }
    ]
*/

let getName = fs.readFile('./name123.txt', 'utf8'); // 读取文件失败
let getAge = fs.readFile('./age.txt', 'utf8');
// 输出结果
/*
    [
    { status: 'fulfilled', value: 1 },
    {
      status: 'rejected',
      value: [Error: ENOENT: no such file or directory, open './name123.txt'] {
        errno: -2,
        code: 'ENOENT',
        syscall: 'open',
        path: './name123.txt'
      }
    },
    { status: 'fulfilled', value: '11' },
    { status: 'fulfilled', value: 2 }
  ]
*/

```
##### 实现

```
function isPromise (val) {
  return typeof val.then === 'function'; // (123).then => undefined
}

Promise.allSettled = function(promises) {
  return new Promise((resolve, reject) => {
    let arr = [];
    let times = 0;
    const setData = (index, data) => {
      arr[index] = data;
      if (++times === promises.length) {
        resolve(arr);
      }
      console.log('times', times)
    }

    for (let i = 0; i < promises.length; i++) {
      let current = promises[i];
      if (isPromise(current)) {
        current.then((data) => {
          setData(i, { status: 'fulfilled', value: data });
        }, err => {
          setData(i, { status: 'rejected', value: err })
        })
      } else {
        setData(i, { status: 'fulfilled', value: current })
      }
    }
  })
}

```
## 30.手动实现Promise.prototype.finally
前面的promise不管成功还是失败，都会走到finally中，并且finally之后，还可以继续then（说明它还是一个then方法是关键），并且会将初始的promise值原封不动的传递给后面的then.

##### Promise.prototype.finally最大的作用

1. finally里的函数，无论如何都会执行，并会把前面的值原封不动传递给下一个then方法中（相当于起了一个中间过渡的作用）——对应情况1，2，3
2. 如果finally函数中有promise等异步任务，会等它们全部执行完毕，再结合之前的成功与否状态，返回值

#### Promise.prototype.finally六大情况用法

```
// 情况1
Promise.resolve(123).finally((data) => { // 这里传入的函数，无论如何都会执行
  console.log(data); // undefined
})

// 情况2 (这里，finally方法相当于做了中间处理，起一个过渡的作用)
Promise.resolve(123).finally((data) => {
  console.log(data); // undefined
}).then(data => {
  console.log(data); // 123
})

// 情况3 (这里只要reject，都会走到下一个then的err中)
Promise.reject(123).finally((data) => {
  console.log(data); // undefined
}).then(data => {
  console.log(data);
}, err => {
  console.log(err, 'err'); // 123 err
})

// 情况4 (一开始就成功之后，会等待finally里的promise执行完毕后，再把前面的data传递到下一个then中)
Promise.resolve(123).finally((data) => {
  console.log(data); // undefined
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('ok');
    }, 3000)
  })
}).then(data => {
  console.log(data, 'success'); // 123 success
}, err => {
  console.log(err, 'err');
})

// 情况5 (虽然一开始成功，但是只要finally函数中的promise失败了，就会把其失败的值传递到下一个then的err中)
Promise.resolve(123).finally((data) => {
  console.log(data); // undefined
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject('rejected');
    }, 3000)
  })
}).then(data => {
  console.log(data, 'success');
}, err => {
  console.log(err, 'err'); // rejected err
})

// 情况6 (虽然一开始失败，但是也要等finally中的promise执行完，才能把一开始的err传递到err的回调中)
Promise.reject(123).finally((data) => {
  console.log(data); // undefined
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('resolve');
    }, 3000)
  })
}).then(data => {
  console.log(data, 'success');
}, err => {
  console.log(err, 'err'); // 123 err
})

```
#### 源码实现
```
Promise.prototype.finally = function (callback) {
  return this.then((data) => {
    // 让函数执行 内部会调用方法，如果方法是promise，需要等待它完成
    // 如果当前promise执行时失败了，会把err传递到，err的回调函数中
    return Promise.resolve(callback()).then(() => data); // data 上一个promise的成功态
  }, err => {
    return Promise.resolve(callback()).then(() => {
      throw err; // 把之前的失败的err，抛出去
    });
  })
}

```
