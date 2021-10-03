# 内存管理和内存泄露

#### 内存管理

##### 定义

不论何种编程语言，在**代码的执行过程中都需要给它分配内存**，不同的是某些编程语言（如 C、C++）需要**手动管理内存**，某些编程语言（如 Java、JavaScript、Python）会**自动管理内存**。

> **硬盘**中的**程序指令**加载至**内存**中，由 **CPU** 执行程序指令

##### 生命周期

> 第一步：**申请**分配内存
>
> 第二步：**使用**分配的内存
>
> 第三步：**释放**内存

#### JS 的内存管理

> JavaScript 会在**定义变量时**分配内存

- 对于**基本数据类型**的内存，会直接在**栈空间**进行分配
- 对于**复杂数据类型**的内存，会在**堆内存**中开辟一块空间，并且将这块空间的**指针**返回值变量**引用**

#### JS 的垃圾回收

> 因为**内存大小有限**，所以当**内存不再需要**时，需要**对其进行释放**，以便腾出**更多的内存空间**

**垃圾回收机制（Garbage Collection）**

- **垃圾**：不再使用的对象
- **语言运行环境内置垃圾回收器**

> ❓ GC 如何知道哪些对象不再使用

- **GC 算法**

  - **引用计数**

    - 当一个对象有一个引用指向它时，那么这个对象的引用就 +1，当一个对象的引用为 0 时，这个对象就可以被销毁

    - 弊端：循环引用

      <img src="_media/assets/javascript/memory_management/循环引用.PNG" alt="循环引用" />

  - **标记清除**

    - 设置一个根对象，垃圾回收器会定期从这个根开始，找所有从根开始有引用到的对象，对于没有引用到的对象则进行回收

    - 解决**循环引用**问题

      <img src="_media/assets/javascript/memory_management/标记清除.PNG" alt="标记清除" />
