---
title: typescript的简单使用
tags: typescript
categories: 
- typescript
---

#### 什么是typescript

TypeScript是JavaScript的超集，它可编译为纯JavaScript，是一种给 JavaScript 添加特性的语言扩展

#### 基础类型

- string

- number

  双精度 64 位浮点值。它可以用来表示整数和分数，**注意：**TypeScript 和 JavaScript 没有整数类型

- boolean

- void

  用于标识方法返回值的类型，表示该方法没有返回值

- any

  声明为 any 的变量可以赋予任意类型的值

  ```typescript
  let foo: any = 'xx'
  
  foo = 2
  ```

- enum

  枚举类型用于定义数值集合

  ```typescript
  enum Color {Red, Green, Blue};
  let c: Color = Color.Blue;
  console.log(c);    // 输出 2
  ```

- null

  表示对象值缺少,表示空对象的引用，typeof返回的是object

- undefined

  用于初始化变量为一个未定义的值，typeof返回的是undefined

- never

  never是其他类型的子类型，代表从不会出现的值，在函数中它通常表现为抛出异常或无法执行到终止点（例如无限循环）

  ```typescript
  // 返回值为 never 的函数可以是抛出异常的情况
  function error(message: string): never {
      throw new Error(message);
  }
  
  // 返回值为 never 的函数可以是无法被执行到的终止点的情况
  function loop(): never {
      while (true) {}
  }
  ```

  

#### 类型推论

```typescript
let a = 'xxx' // 此时表示a已经为string类型
a = 2 //错误
```

#### 数组

```typescript
let names: string[]

names = ['tom']
```

任意类型也可以用于数组

```typescript
let list: any[] = [1, 'abc']
```

#### 多种类型

```typescript
let multiType: string | number

multiType = 'abc'

multiType = 123
```

#### 函数中使用

- 作为形参
- 作为返回值

```typescript
function greeting(person: string): string {
	return 'hello' + person
}

function warnUser(msg: string): void {
	alert(msg)
}
```

```typescript
// 此处name和age是必填参数
// 如果要变为可选参数，加上？
// 可选参数在必选参数后面
function sayHello (name: string, age?: number): string {
	return name + age
}
sayHello('tom', 12)
sayHello('tom')
```

#### 重载

通过参数或者返回值的类型或个数不同区别同名函数，注意：先声明，再实现

```typescript
// 声明1
function info(a: object):string
// 声明2
function info(a: string): object
// 实现
function info(a: any): any {
    if(typeof a == 'object') {
        return a.name
    } else {
		return { name: a }
	}
}
console.log(info({name: 'tom'}))
console.log(info('tom'))
```

#### 类中使用

#### 类的原理

class是语法糖，它指向的就是构造函数

```js
class Person{ // 类指向构造函数
    constructor(name, age){ // constructor是默认方法，new实例时自动调用
        this.name = name; // 属性会声明在实例上，因为this指向实例
        this.age = age;
    }
    say(){ // 方法会声明在原型上
        return "我的名字叫" + this.name + "今年" + this.age + "岁了";
    }
}
console.log(typeof Person); // function
console.log(Person === Person.prototype.constructor); // true
// 等效于
function Person(name,age) {
    this.name = name;
    this.age = age;
}
Person.prototype.say = function(){
	return "我的名字叫" + this.name+"今年"+this.age+"岁了";
}
```



##### typescript带来的特性

- 访问修饰符private, public, protected, readonly

- 构造函数参数加上修饰符能够定义并初始化一个成员属性
- 存取器：存取数据时可以添加额外的逻辑，在vue里面可以做计算属性

```typescript
class MyComp {
    // 访问修饰符private, public, protected
    private _foo: string // 私有属性， 不能在类外部使用
    protected bar: string //保护属性，还可以派生类中使用
    public p: string // 可以被外部使用
    readonly mua = 'mua' //只读属性必须在声明时初始化，构造函数里声明时也需初始化

    // 构造函数：初始化成员变量
    // 参数加上修饰符能够定义并初始化一个成员属性
    constructor (private tua = 'tua') {
        this._foo = 'foo'
        this.bar = 'bar'
        this.p = 'p'
    }

    // 方法也可以有修饰符
    private someMethod(){}
    
    // 存取器
    get foo() {
        return this._foo
    }

    set foo(val) {
        this._foo = val
    }
}
```

#### 自定义类型约束数据结构

```typescript
// 定义一个特性类，拥有更多属性
class Feature {
    constructor(public id: number, public name: string) {}
}

// 可以对获取的数据类型做约束
@Component
export default class HelloWorld extends Vue {
    private features: Feature[];
    constructor() {
        super();
        this.features = [
            { id: 1, name: "类型注解" },
            { id: 2, name: "编译型语言" }
        ];
    }
    get count() {
    	return this.features.length;
    }
}
```

##### 接口: interface

仅定义结构，不需要实现

```typescript
// 定义接口类
interface Person {
    firstName: string
    lastName: string
    sayHello(): string // 要求实现方法
}
// 实现接口
class Greeter implements Person {
    constructor(public firstName = '', public lastName = '') {
    }
    sayHello() {
        return 'Hello' + this.firstName + this.lastName
    }
}

// 面向接口编程
function greeting2(person: Person) {
    return person.sayHello()
}
const user = new Greeter('Jane', 'User')
console.log(greeting2(user))
```

##### 泛型

Generics是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性

```typescript
// 定义泛型接口
interface Result<T> {
    ok: 0 | 1;
    data: T[];
}
// 定义泛型函数
function getData<T>(): Result<T> {
    const data: any[] = [
        { id: 1, name: "类型注解", version: "2.0" },
        { id: 2, name: "编译型语言", version: "1.0" }
    ];
    return { ok: 1, data };
}
// 使用泛型
this.features = getData<Feature>().data;
```

返回Promise

```typescript
function getData<T>(): Promise<Result<T>> {
    const data: any[] = [
        { id: 1, name: "类型注解", version: "2.0" },
        { id: 2, name: "编译型语言", version: "1.0" }
    ];
    return Promise.resolve<Result<T>>({ ok: 1, data });
}

// 使用
async created() {
    const result = await getData<Feature>();
    this.features = result.data;
}
```

#### 装饰器

装饰器实际上是工厂函数，传入一个对象，输出处理后的新对象, 装饰器实际上是一个函数，通过定义劫持，能够对类及其方法、属性提供额外的扩展功能。

```typescript
// 类装饰器
function isFoo(target) {
    // target是构造函数Foo
    console.log(target === Foo); // true
    target.isFoo = true;    
    return target;
}
// 属性装饰器
function mua(target, name) {
    // target是原型，name是属性名
    console.log(target === Foo.prototype);
    target[name] = 'mua~~~'
}
// 方法装饰器
function dong(target, name, descriptor) {
    // target是原型，name是方法名，descriptor是描述符，
    console.log(target === Foo.prototype);    
    // 方法的定义方式：Object.defineProperty(target, name, descriptor)
    console.log(target[name] === descriptor.value);
    // 这里通过修改descriptor.value扩展了bar方法
    // Object.defineProperty(target, name, {
    //     get(){},
    //     set(){},
    //     configurable:true,
    //     enumerable:true,
    //     writable:true,
    //     value:function(){}
    // })
    const bar = descriptor.value;
    descriptor.value = function() {
        console.log('dong~');
        bar();
    }
    return descriptor
}
@isFoo
class Foo {
    @mua aaa!: string;    
    constructor(){
        console.log('Foo构造函数');
    }
    @dong 
    bar() {
        console.log('bar~');
    }
}

console.log(Foo.isFoo) // true
const f = new Foo();
console.log(f.aaa); // mua~~
f.bar()
```