# 浏览器工作原理和 V8 引擎

#### JavaScript 是一门高级编程语言

- **编程语言发展历史**

  - 机器语言    ⬆ 接近机器思维

  - 汇编语言    

  - 高级语言    ⬇ 接近人类思维

    - 编译型语言：C、C++、Java

      > 代码先转化为可执行文件

    - 解释型语言：JavaScript、Python

#### 浏览器的工作原理

<img src="_media/assets/javascript/engine/浏览器的工作原理.PNG" alt="浏览器的工作原理" />

> 1. 输入 URL 进行搜索
> 2. DNS 将 URL 解析为服务器 IP 地址并进行访问
> 3. 下载 HTML 及所需 CSS / JS 文件，由浏览器内核（渲染引擎）进行解析渲染

#### 浏览器的渲染过程

<img src="_media/assets/javascript/engine/浏览器渲染过程.PNG" alt="浏览器的渲染过程" />

> 如果在 HTML 解析过程中遇到 JavaScript 代码，会**停止解析 HTML**，而去加载和执行 JavaScript 代码，因为这有可能改变当前 DOM Tree 的结构

> 💡 JavaScript 代码由谁来执行？

#### JavaScript 引擎

1. **作用**

   通过 **JavaScript 引擎**将 **JavaScript 代码**转为 **CPU 指令**（二进制机器指令）来执行

2. **与浏览器内核的关系**
   
   <img src="_media/assets/javascript/engine/JS引擎与浏览器内核关系.PNG" alt="JS引擎与浏览器内核关系" />
   
   > 🎯 Chrome
   >
   > - 浏览器内核：**Blink**
   > - JS 引擎：**V8**
   
3. **V8 引擎**

   - 原理

     - 由 C++ 编写的开源高性能 JavaScript 和 WebAssembly 引擎，用于 Chrome 和 Node.js 等
     - 能在多平台上运行
     - 可以独立运行，也可以嵌入到任何 C++ 应用程序中

     <img src="_media/assets/javascript/engine/V8 引擎原理.PNG" alt="V8 引擎原理" style="zoom:80%;" />

     > **Parser**：将 JS 代码（由词法分析转化为 tokens）解析为 AST 抽象语法树
     >
     > **PreParser**：**延迟解析**。将不必要的函数进行预解析（一开始未被执行），而在函数被调用时才进行全量解析。
     >
     > **Ignition**：**解释器**（V8 引擎中的一个模块），负责将 **AST 转化成字节码**，逐句解释成机器码并执行，同时会收集优化所需信息
     >
     > - ❓ 原因：不同操作系统 CPU 架构差异，导致可运行 CPU 指令的差异，而字节码存在**跨平台特性**，不针对特定的 CPU 架构。
     >
     > **TurboFan**：**编译器**（V8 引擎中的一个模块），在解释执行的过程中将 **字节码编译为优化的机器码**或将**已优化的机器码去优化为字节码**
     >
     > - ❓ 原因：
     >   - 优化：部分代码重复执行，就会被标记为热点代码，转化成效率更高的机器码，再次运行时直接执行该部分机器码，不再逐句解释执行字节码，提高代码执行性能。
     >   - 去优化：代码在多次执行过程中类型可能发生变化，原机器码不能正确处理运算，就会逆向转化为字节码。
     
   - 执行过程

     - **初始化全局对象**

       > JS 引擎会在**执行代码之前（Parse 过程中）**，在**堆内存中创建一个全局对象：Global  Object（GO）**

       - 该对象所有作用域（scope）都可以访问
       - 里面会包含 Date、Array、String、Number、setTimeout、setInterval 等等
       - 其中还有一个 window 属性指向自己

       <img src="_media/assets/javascript/engine/Global Object.PNG" alt="Global Object" />

     - **执行上下文栈（ECS，代码执行调用栈）**

       - 全局的代码块为了执行会创建一个 Global Execution Context（GEC），GEC 会被放入到 ECS 中执行，其中包含两部分内容：

         - 第一部分：在**代码执行前（Parse 过程中）**，会将**全局定义的变量、函数**等加入到 **Global Object** 中，但是**并不会赋值**

           > 📝 **变量**初始化为 **undefined**，**函数**则存储所对应函数对象（包含父级作用域与函数执行体）的**内存地址**
           >
           > 这个过程也称为**变量的作用域提升**

         - 第二部分：在**代码执行中**，对变量**赋值**，或执行其他函数

         <img src="_media/assets/javascript/engine/ECS GEC.PNG" alt="ECS GEC" />

       - 在执行的过程中执行到一个函数时，会根据函数体创建一个 Functional Execution Context（FEC），并且压入到 ECS 中执行，其中包含三部分内容：

         - 第一部分：在**代码执行前（Parse 过程中）**，会创建一个 Activation Object（AO）

           > AO 中包含形参、arguments、函数定义和指向函数对象、定义的变量

         - 第二部分：作用域链

           > 📝 由 VO（指向 AO 对象）和父级 VO 组成，查找时**沿作用域链层层向上查找**
           >
           > **函数作用域**与**函数定义位置**有关，而与函数调用位置无关

         - 第三部分：this 绑定值

         <img src="_media/assets/javascript/engine/ECS FEC.PNG" alt="ECS FEC" />

       - 变量环境和记录

         > 早期 ECMAScript 版本规范：
         >
         > **每一个执行上下文**会被关联到一个**变量对象（Variable Object，VO）**，在源代码中的变量和函数声明会被作为**属性**添加到 VO 中。对于函数来说，参数也会被添加到 VO 中。
         >
         > 最新 ECMAScript 版本规范：
         >
         > **每一个执行上下文**会关联到一个**变量环境（Variable Environment）**中，在执行代码中变量和函数的声明会作为**环境记录（Environment Record）**添加到变量环境中。对于函数来说，参数也会被作为环境记录添加到变量环境中。

     - 📍 **作用域提升面试题**

       <img src="_media/assets/javascript/engine/作用域提升面试题.PNG" alt="作用域提升面试题" />
