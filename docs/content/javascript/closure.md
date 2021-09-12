# 闭包

> **JS 中函数是一等公民**
>
> 函数可以作为另一个函数的**参数**，也可作为另一个函数的**返回值**来使用

> **高阶函数**
>
> - **filter**：过滤
> - **map**：映射
> - **reduce**：累计
> - **find/findIndex**：查找
> - **forEach**：迭代 

#### 定义

> MDN：**一个函数**和**对其周围状态（词法环境）的引用**捆绑在一起，这样的**组合**就是闭包

**一个函数如果它有访问外层作用域的自由变量，那么这个函数与其可捕获到的外层自由变量的组合就是闭包**

#### 闭包的执行过程

> 🤔 思考以下代码在内存中的执行过程

```javascript
function bar() {
    var name = "curry";
    function foo() {
        console.log(name);
    }
    
    return foo;
}

var fn = bar();
fn(); // curry
```

<img src="_media/assets/javascript/closure/闭包执行过程.PNG" alt="闭包执行过程" />

> ❓ 此时闭包为

**此时 name 变量和 foo 函数的组合就是闭包**

> ❓ 为什么 bar 函数执行完被销毁后，仍能读取到 name 变量

```javascript
var fn = bar(); 
/* 
** 此时 fn 变量存着 foo 函数的内存地址，引用指向 foo 函数对象
** 而 foo 函数对象内的父级作用域存着 bar 函数 AO 对象的内存地址，引用指向 bar 函数 AO 对象
** 所以 bar 函数的 AO 对象因沿根对象仍在引用路径，故未被 GC 销毁，仍可读取到其中所存储的 name 变量
*/
```

#### 闭包的内存泄漏

> ❓ 为什么会造成内存泄漏

**fn 函数执行完后，自身函数对象与所引用的父级作用域 AO 对象未被销毁，仍占用内存** 

> ❓ 如何解决

```javascript
fn = null;
// fn 引用指向为空
```
