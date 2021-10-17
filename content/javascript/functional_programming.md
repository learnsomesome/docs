# 函数式编程

#### 📌 JS 实现 call / apply / bind

```javascript
// call
Function.prototype._call = function(_this, ..._params) {
    // 判断传入 this 参数类型
    _this = _this === undefined || _this === null ? window : Object(_this);
    
    // this 即为当前所调用函数
    _this.fn = this;
    const res = _this.fn(..._params);
    delete _this.fn;
    
    return res;
};

// apply
Function.prototype._apply = function(_this, _params = []) {
    _this = _this === undefined || _this === null ? window : Object(_this);
    _this.fn = this;
    const res = _this.fn(..._params);
    delete _this.fn;
    
    return res;
};

// bind
Function.prototype._bind = function(_this, ..._params) {
    return (...params) => {
        _this = _this === undefined || _this === null ? window : Object(_this);
        _this.fn = this;
        const res = _this.fn(..._params, ...params);
        delete _this.fn;

        return res;
    };
};
```

####  Arguments

##### 定义

对应于**传递给函数的参数**的**类数组（array-like）对象**

> - 存储在当前函数 AO 对象中
>
> - 箭头函数内部不绑定 arguments 对象，会往上层作用域查找
>
> - ES6 后通过剩余参数获取入参

##### 作用

- 获取传参个数 `arguments.length`
- 获取具体传参值 `arguments[0]`
- 获取当前函数 `arguments.callee` 

**转化为数组**

- for 循环遍历塞入新数组
- `Array.prototype.slice.call(arguments)` 或 `[].slice.call(arguments)`
- `Array.from(arguments)`
- `[...arguments]`

#### 纯函数

##### 定义

函数符合以下条件

- 确定的输入，一定会产生确定的输出

- 函数在执行过程中，不能产生副作用

  > **副作用（side effect）：**
  >
  > 在执行函数时，除了返回函数值之外，还对调用函数产生了附加的影响，如修改了全局变量，修改参数或改变外部存储等

##### 优势

- 编写：保证函数纯度，单纯实现自己的业务逻辑，不需要关心传入内容或依赖其他外部变量
- 使用：确定输入内容不会被任意篡改，确定输入即有确定输出

> 🔰 所有 React 组件都必须像纯函数一样保护它们的 props 不被更改

#### 柯里化

##### 定义

只传递给函数一部分参数来调用它，让它返回一个函数去处理剩余参数，这个转换过程为柯里化

> **单一职责原则：**一个函数处理的问题尽可能单一

##### 应用

- 逻辑复用

  ```javascript
  // 原函数
  function log(date, type, message) {
      console.log(`[${date}]: (${type})${message}`);
  }
  
  // 柯里化函数
  const log = date => type => message => console.log(`[${date}]: (${type})${message}`);
  
  const nowLog = log(Date.now());
  newLog('DEBUG')('xxx');
  
  const nowDebugLog = log(Date.now())('DEBUG');
  nowDebugLog('xxx');
  ```

##### 实现

```javascript
function currying(fn) {
    function curried(...args) {
        // 判断当前接收参数个数，是否与函数本身需接受参数个数一致
        if (args.length >= fn.length) {
            return fn.apply(this, args);
        } else {
            function _curried(..._args) {
                return curried.apply(this, [...args, ..._args]);
            }
            
            return _curried;
        }
    }
    
    return curried;
}
```

#### 组合函数

##### 定义

多个函数依次调用，上一个函数返回结果作为下一个函数的入参，将其转化为一个函数的执行过程

##### 实现

```javascript
function compose(...fns) {
    const length = fns.length;

    if (fns.some((fn) => typeof fn !== 'function')) {
        throw new TypeError("Expected arguments are functions");
    }
    
    function _compose(...args) {
        let index = 0;
        let result = length ? fns[index].apply(this, args) : args;
        while(++index < length) {
            result = fns[index].call(this, result);
        }
        
        return result;
    }
    
    return _compose;
}
```

