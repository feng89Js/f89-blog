---
title: typescript 使用笔记
date: 2020.03.01
categories:
  - 开发
tags:
  - typescript
---

# typescript使用技巧

小结 [typescript lib.es5.d.ts](https://github.com/microsoft/TypeScript/blob/master/lib/lib.es5.d.ts#L1437-L1512)

## Partial

使泛型所有属性都可选

```ts
type Partial<T> = {
    [P in keyof T]?: T[P];
};

interface User {
  id: number;
  age: number;
  name: string;
};
// type user = { id?: number; age?: number; name?: string }
type user = Partial<User>
```
<!-- more -->

## Required

使泛型所有参数都必选， 与`Partial`相反

```ts
type Required<T> = {
    [P in keyof T]-?: T[P];
};

interface User {
  id?: number;
  age?: number;
  name?: string;
};
// type user = { id: number; age: number; name: string }
type user = Required<User>
```

## Pick

从T中，选出K对应的属性值

```ts
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};

interface User {
  id: number;
  age: number;
  name: string;
};
// type user = { id: number; name: string }
type user = Pick<User, 'id' | 'name'>
```

## Record

创建一组键为K，类型为T的数据类型

```ts
type Record<K extends keyof any, T> = {
    [P in K]: T;
};

interface User {
  id: number;
  age: number;
  name: string;
};
// type user = { vip1: User; vip2: User }
type user = Record<'vip1' | 'vip2', User>
```

## Exclude

从T中排除U的值，

```ts
type Exclude<T, U> = T extends U ? never : T;

interface User {
  id: number;
  age: number;
  name: string;
};
// type user = "id" | "age"
type user = Exclude<keyof User, 'name'>
```

## Extract

与`Exclude`相反，从T中提取可分配给U的类型

```ts
type Extract<T, U> = T extends U ? T : never;

interface User {
  id: number;
  age: number;
  name: string;
};
// type user = "name"
type user = Extract<keyof User, 'name'>
```

## Omit

创建一个除了K以外的类型，

```ts
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;

interface User {
  id: number;
  age: number;
  name: string;
};
// type user = { id: number; age: number; }
type user = Omit<User, 'name'>
```

## NonNullable

从T排除null和undefined

```ts
type NonNullable<T> = T extends null | undefined ? never : T;

interface User {
  id?: number | null;
  age: number;
  name: string;
};

// type id = number
type id = NonNullable<User['id']>
```

## Parameters

```ts
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never;

function foo(x: number): Array<number> {
  return [x];
}

// type P = [number]
type P = Parameters<typeof foo>;
```

## ConstructorParameters

```ts
type ConstructorParameters<T extends new (...args: any) => any> = T extends new (...args: infer P) => any ? P : never;

class Person {
  private firstName: string;
  private lastName: string;

  constructor(firstName: string, lastName: string) {
      this.firstName = firstName;
      this.lastName = lastName;
  }
}

// type P = [string, string]
type P = ConstructorParameters<typeof Person>;
```

## ReturnType

```ts
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;

function foo(x: number): Array<number> {
  return [x];
}

// type P = number[]
type P = ReturnType<typeof foo>;
```

## InstanceType

```ts
type InstanceType<T extends new (...args: any) => any> = T extends new (...args: any) => infer R ? R : any;

class C {
    x = 0;
    y = 0;
}

type T20 = InstanceType<typeof C>;  // C
type T21 = InstanceType<any>;  // any
type T22 = InstanceType<never>;  // any
type T23 = InstanceType<string>;  // Error
type T24 = InstanceType<Function>;  // Error\
```

### extends

`extends` 可以用来继承一个类，也可以用来继承一个 interface，但还可以用来判断有条件类型：
```ts
T extends U ? X : Y;
```

上面的类型意思是，若 T 能够赋值给 U，那么类型是 X，否则为 Y。
原理是令 `T` 和` U`分别为 T 和 U 的实例，并将所有类型参数替换为 any，如果 T能赋值给 U，则将有条件的类型解析成 X，否则为Y。
上面的官方解释有点绕，下面举个栗子：

```ts
type Words = 'a'|'b'|"c";

type W<T> = T extends Words ? true : false;

type WA = W<'a'>; // -> true
type WD = W<'d'>; // -> false
```
a 可以赋值给 Words 类型，所以 WA 为 true，而 d 不能赋值给 Words 类型，所以 WD 为 false。

### typeof
在 JS 中 typeof 可以判断一个变量的基础数据类型，在 TS 中，它还有一个作用，就是获取一个变量的声明类型，如果不存在，则获取该类型的推论类型。

```ts
interface Person {
  name: string;
  age: number;
  location?: string;
}

const jack: Person = { name: 'jack', age: 100 };
type Jack = typeof jack; // -> Person

function foo(x: number): Array<number> {
  return [x];
}
type F = typeof foo; // -> (x: number) => number[]
```


`Jack`这个类型别名实际上就是 jack 的类型 Person，而 F 的类型就是 TS 自己推导出来的 foo 的类型 `(x: number) => number[]`。


### keyof
keyof 可以用来取得一个对象接口的所有 key 值：

```ts
interface Person {
    name: string;
    age: number;
    location?: string;
}

type K1 = keyof Person; // "name" | "age" | "location"
type K2 = keyof Person[];  // "length" | "push" | "pop" | "concat" | ...
type K3 = keyof { [x: string]: Person };  // string | number
```

### in

in 可以遍历枚举类型：
```
type Keys = "a" | "b"
type Obj =  {
  [p in Keys]: any
} // -> { a: any, b: any }
```
上面 in 遍历 Keys，并为每个值赋予 any 类型。

### infer
在条件类型语句中, 可以用 infer 声明一个类型变量并且对它进行使用，
我们可以用它获取函数的返回类型， 源码如下：
```ts
type ReturnType<T> = T extends (
  ...args: any[]
) => infer R
  ? R
  : any;
```
其实这里的 infer R 就是声明一个变量来承载传入函数签名的返回值类型, 简单说就是用它取到函数返回值的类型方便之后使用。

### Mutable

将 T 的所有属性的 readonly 移除：

```ts
type Mutable<T> = {
  -readonly [P in keyof T]: T[P]
}
```

### PowerPartial
内置的 Partial 有个局限性，就是只支持处理第一层的属性，如果是嵌套多层的就没有效果了，不过可以如下自定义：

```ts
type PowerPartial<T> = {
    // 如果是 object，则递归类型
    [U in keyof T]?: T[U] extends object
      ? PowerPartial<T[U]>
      : T[U]
};
```
### Deferred
相同的属性名称，但使值是一个 Promise，而不是一个具体的值：
```ts
type Deferred<T> = {
    [P in keyof T]: Promise<T[P]>;
};
```

### Proxify
为 T 的属性添加代理
```ts
type Proxify<T> = {
    [P in keyof T]: { get(): T[P]; set(v: T[P]): void }
};
```