# This

#### 作用

> ✍ 方便代码编写

#### 指向

##### 全局作用域

- **浏览器：**window（globalObject）
- **Node：**{}

##### 函数作用域

1. 函数在调用时，JS 会默认给 this 绑定一个值；
2. this 的绑定和定义的位置无关；
3. this 的绑定和**调用方式以及调用的位置**有关；
4. this 是在**运行时被绑定**的；

#### 绑定规则

- **默认绑定**

  **独立函数调用：**函数没有被绑定到某个对象上进行调用。此时 this 均为全局对象。

  ```javascript
  // eg:
  function foo() {
      console.log(this);
  }
  
  foo(); // window
  ```

- **隐式绑定**

  **通过某个对象发起函数调用**

  ```javascript
  // eg:
  const obj = {
      name: 'curry',
      foo: function() {
          console.log(this);
      },
  };
  
  obj.foo(); // obj
  ```

- **显示绑定**

  **调用函数时明确指定所绑定 this：**call / apply / bind

  > 🤔 思考三个函数之间的区别

  ```javascript
  // eg:
  function foo(num1, num2) {
      console.log(this);
  }
  
  foo.call('str', 1, 2); // String {'str'}
  foo.apply('str', [1, 2]); // String {'str'}
  const _foo = foo.bind('str');
  ```

- **new 绑定**

  使用 new 关键字来调用函数（构造器），此时 this 绑定为调用函数所创建的对象

  ```javascript
  // eg:
  function Person(name, age) {
   this.name = name;
   this.age = age;
  }
  
  const curry = new Person('curry', 30); // 此时 this 为 curry 对象
  ```

> 🤔 内置函数绑定思考：setTimeout，元素事件绑定函数，forEach......

#### 规则优先级

**new 绑定 > 显示绑定 > 隐式绑定 > 默认绑定**

#### 特殊绑定

- **忽略显示绑定**

  apply / call / bind：当传入 null 或 undefined 时，自动将 this 绑定成全局对象

- **间接函数引用**

  ```javascript
  const obj1 = {
      name: 'obj1',
      foo: function() {
          console.log(this);
      },
  };
  
  const obj2 = {
      name: 'obj2',
  };
  
  (obj2.bar = obj1.foo)(); // window
  ```

- **箭头函数获取**

  不绑定 this，从上层作用域获取

#### 📍 作用域提升面试题

1️⃣

```javascript
var name = "window";
var person = {
    name: "person",
    sayName: function() {
        console.log(this.name);
    },
};

function sayName() {
    var sss = person.sayName;
    sss(); // window
    person.sayName(); // person
    (person.sayName)(); // person
    (b = person.sayName)(); // window
}

sayName();
```

2️⃣

```javascript
var name = "window";

var person1 = {
    name: "person1",
    foo1: function() {
    	console.log(this.name);
    },
    foo2: () => console.log(this.name),
    foo3: function() {
        return function() {
            console.log(this.name);
        },
    },
    foo4: function() {
        return () => {
            console.log(this.name);
        },
    },
};
    
var person2 = {
    name: "person2",
};

person1.foo1(); // person1
person1.foo1.call(person2); // person2

person1.foo2(); // window
person1.foo2.call(person2); // window

person1.foo3()(); // window
person1.foo3.call(person2)(); // window
person1.foo3().call(person2); // person2

person1.foo4()(); // person1
person1.foo4.call(person2)(); // person2
person1.foo4().call(person2); // person1
```

3️⃣

```javascript
var name = "window";

function Person(name) {
	this.name = name;
    this.foo1 = function() {
        console.log(this.name);
    },
    this.foo2 = () => console.log(this.name),
    this.foo3 = function() {
    	return function() {
            console.log(this.name);
        };  
    },
    this.foo4 = function() {
        return () => {
            console.log(this.name);
        };
    },
}
    
var person1 = new Person('person1');
var person2 = new Person('person2');
    
person1.foo1(); // person1
person1.foo1.call(person2); // person2
    
person1.foo2(); // person1
person1.foo2.call(person2); // person1
    
person1.foo3()(); // window
person1.foo3.call(person2)(); // window
person1.foo3().call(person2); //person2
    
person1.foo4()(); // person1
person1.foo4.call(person2)(); // person2 
person1.foo4().call(person2); // person1
```

4️⃣

```javascript
var name = "window";

function Person(name) {
    this.name = name;
    this.obj = {
    	name: 'obj',
        foo1: function() {
            return function() {
            	console.log(this.name);  
            };
        },
        foo2: function() {
            return () => {
            	console.log(this.name);  
            };
        },
    };
}

var person1 = new Person('person1');
var person2 = new Person('person2');

person1.obj.foo1()(); // window
person1.obj.foo1.call(person2)(); // window
person1.obj.foo1().call(person2); // person2

person1.obj.foo2()(); // obj
person1.obj.foo2.call(person2)(); // person2
person1.obj.foo2().call(person2); // obj
```

