

tsc
ts-node
  

// Union
let score = number | null


// Alias (псевдоним; с большой буквы)
type MyType = string | boolean
  
  
// Arrays - это List(произвольный размер, один тип)
или Tuple(конкретный размер, могут разных типов)
  

// List
const numbers1: number[] = [1, 2, 3]
или
const numbers2: Array<number> = [4, 5, 6]
const list: (number | null)[] = [1, null], но лучше этот тип вынести в alias
  

// Tuple
const tuple1: [string, number] = ['Pavel', 20]


// Object
const obj_1: {a: number; b: string} = {a: 1, b: 'hello'}

это эквивалентно
type MyObj = {a: number, b: string};
const obj_1: MyObj = {a: 1, b: 'hello'}

или

interface IMyObj {a: number, b: string}
const obj_1: IMyObj = {a: 1, b: 'hello'}

  

// Interface (свои имена с буквы I)
можно ?
можно readonly

  
можно в конце сказать, что будет ещё неопределенное количество ключей:
interface {a: string, [key: string]: any}

методы:
print(a: number): void - не сделать опционально
print: (a:number) => void - можно опционально

если переобъявить интерфейс, то он примкнёт к ранее созданному (type в таком случае выдаст ошибку)


можно по-другому "объединить" интерфейсы:
interface IProgrammer extends IEmployee, IHuman {...}
аналог для типов: type1 = type2 & type3

  
  

// Function
const myFn: `(num: number) => void` = (num) => {...}
или
const myFn = (num: number):void => {...} (для FD/FE типы указ. после скобок)

  

Если параметр функции обозначен через интерфейс, то это объявление "программы минимум" для него.

Например:

interface IObj {a: number}

const fun = function(obj: IObj): void {}

const testObj = {a: 1, b: true}

fun(testObj) // без ошибок

fun({a: 1, b: true}) // ошибка

  

Перегрузка:

const someFn = (param: number | {flag: boolean}): num | string;

но лучше

const someFn = (param: number): num;

const someFn = (param = {flag: boolean}): string;

const someFn = (param): any {

if (typeof x ...) ...

...

}

  
  

// Never

Указываем как возвращаемый тип функции, если та не заканчивается или кидает ошибку

  

// As

const test_1 = {} as IObj

или

const test_2 = <IObj>{} (old version, doesn't work)

  
  

// Modify type

type MyType = {a: number, b: boolean}

  

type ModifType = Exclude<keyof MyType, 'a'>

или

type ModifType = Pick<MyType, 'b'>

  
  

// Generics

E.g.:

  

// ES5 syntax

function getter<T>( data: T): T {

return data;

}

// ES6 syntax

const getter = <T>(data: T): T => data;

  

const mergeObjects = <T extends object, R extends object>(a: T, b: R) => Object.assign({}, a, b)

  

const get0bjectValue = <T extends object, R extends keyof T>(obj: T, key: R) => obj [key]

  

Partial<myObj> - тип myObj с возможным остутствием некоторых полей

  

Readonly<myArr> - объект типа myArr только для чтения

  
  

// Class

  

E.g.

class User {

public name: string;

public nickName: string;

public age: number;

public pass: number;

constructor (name: string, age: number, nickName: string, pass: number) {

this.name = name;

this.age = age;

this.nickName = nickName;

this.pass = pass;

}

  

Этот вариант можно написать проще:

  

class User {

constructor (

public name: string,

public age: number,

public nickName: string,

public pass: number

) {}

}

  

Пример сеттеров:

  

class User {

private age: number = 20;

  

constructor (public name: string) {}

  

setAge(age: number) {

this.age = age;

}

  

set myAge(age: number) {

this.age = age;

}

}

  

const Yauhen = new User ('Yauhen');

yauhen.setAge(30)

yauhen.myAge = 31;

  
  

// Namespace

  

Создание глобальных переменных - не лучшая практика.

  

namespace Utils {

export const SECRET: string = '123321';

const PI: number = 3.14;

export const getPass (name: string, age: number): string => ${name}${age}*;

export const isEmpty<T>(data: T): boolean => !data;

};

  

/// <reference path="Utils.ts" />

  

// Calling exported from namespace methods

const myPass = Utils.getPass ('Yauhen' 31); // "Yauhen31"

// Constant with the same name outside namespace

const PI = 3; // No Errors

  

Метод создания пространства имён через namespace устарел. Используем ES6 модули.

  

// File "Utils.ts"

export const SECRET: string = '123321';

export const getPass = (name: string, age: number): string => ${name}$ {age}*;

  

// File "Customers.ts"

import { getPass, SECRET Lyd from "./Utils";

const myPass = getPass (SECRET, 31); 11 "Yauhen31"

  
  
  

// Decorators

  

// Class Decorator (если декоратор содержит return, то им он заменяет объявление класса с помощью предоставленного констурктора)

const logClass = (constructor: Function) => {

console.log(constructor); // Result of call: class User {}

};

  

@logClass //<--- Apply decorator for class

class User {

constructor (public name: string, public age: number) {}

public getPass (): string {

return `${this.name}${this.age}`;

}

}

  
  

// Property Decorator (декоратор возвращает null или дескриптор свойства)

const logProperty = (target: Object, propertyKey: string | symbol) => {

console.log(propertyKey); // Result of call: "secret"

};

  

class User {

@logProperty //<--- Apply decorator for property

secret: number;

  

constructor (public name: string, public age: number, secret: number) {

this.secret = secret;

}

  

public getPass(): string {

return `${this.name}${this.age}`;

}

}

  
  

/ / Method Decorator (декоратор возвращает null или дескриптор свойства)

const logMethod = (target: Object, propertyKey: string | symbol, descriptor: PropertyDescriptor) => {

console.log(propertyKey); // Result of call: "getPass"

};

  

class User {

constructor(public name: string, public age: number) {}

  

@logMethod // <--- Apply decorator for method

public getPass(): string {

return `${this.name}${this.age}`;

}

}

  
  

// get/set Decorator (декоратор возвращает null или дескриптор свойства)

const logSet = (target: Object, propertyKey: string | symbol, descriptor: PropertyDescriptor) => {

console.log(propertyKey); //Result of call: "myAge"

};

  

class User {

constructor(public name: string, public age: number) {}

@logSet // <--- Apply decorator for set

set myAge(age: number) {

this. age = age;

}

  

// Factory Decorator

function factory(value: any) { // Factory

return function (target: any) { // Decorator

console.log(target); // Decorator logic

}

}

  

// Applying Factory Decorator

const enumerable = (value: boolean) => {

return (target: any, propertyKey: string | symbol, descriptor: PropertyDescriptor) => {

descriptor.enumerable = value;

};

}

  

class User {

constructor (public name: string, public age: number) {}

@enumerable(false) // <--- Call decorator factory with argument

public getPass(): string {

return ${this.name}${this.age} ;

}

}

  
  

// Decorator composition syntax

// Apply decorators (one line)

@f @g x

  

// Apply decorators (multiple lines)

@f

@g

x

  

// Example of two factory decorators

const first = () => {

    console.log('first() completing');

    return (target: any, propertyKey: string | symbol, descriptor: PropertyDescriptor) => {

        console.log('first() called');

    };

}

  

const second = () => {

    console.log('second() completing');

    return (target: any, propertyKey: string | symbol, descriptor: PropertyDescriptor) => {

        console.log('second() called');

    };

}

  

// Apply and call two factory decorators

class User {

  

    constructor(public name: string, public age: number) {}

    @first()

    @second()

    public getPass(): string {

        return `${this.name}${this.age}`;

    }

  

}

  

// Call results:

"first() completing" // Factory 1

"second() completing" // Factory 2

"second() called" // Decorator 2

"first() called" // Decorator 1

  
  

// Utils

  

// Readonly<T>

interface User {

name: string;

}

  

const user: Readonly<User> = {

name: 'Yauhen',

};

  

user.name = 'Max'; // Error: cannot reassign a readonly property

  

// Required<T>

interface Props {

a?: number;

b?: string;

};

  

const obj: Props = { a: 5 }; // OK

  

const obj2: Required<Props> = { a: 5 }; // Error: property 'b' missing

  

// Record<K, T>

interface PageInfo {

title: string;

}

  

type Page = 'home' | 'about' | 'contact';

  

const x: Record<Page, PageInfo> = {

about: { title: 'about' },

contact: { title: 'contact' },

home: { title: 'home' },

};

  

// Compiled code

"use strict";

const x = {

    about: { title: 'about' },

    contact: { title: 'contact' },

    home: { title: 'home' },

};

  

// Pick<T, K>

interface Todo {

title: string;

description: string;

completed: boolean;

}

  

type TodoPreview = Pick<Todo, 'title' | 'completed'>;

  

const todo: TodoPreview = {

title: 'Clean room',

completed: false,

};

  

// Omit<T, K>

interface Todo {

title: string;

description: string;

completed: boolean;

}

  

type TodoPreview = Omit<Todo, 'description'>;

  

const todo: TodoPreview = {

title: 'Clean room',

completed: false,

};

  

// Exclude<T, U>

type T0 = Exclude<"a" | "b" | "c", "a">; // "b" | "c"

type T1 = Exclude<"a" | "b" | "c", "a" | "b">; // "c"

type T2 = Exclude<string | number | (() => void), Function>; // string | number

  

// Extract<T, U>

type T0 = Extract<"a" | "b" | "c", "a" | "f">; // "a"

type T1 = Extract<string | number | (() => void), Function>; // () => void

  

// NonNullable<T>

type T0 = NonNullable<string | number | undefined>; // string | number

type T1 = NonNullable<string[] | null | undefined>; // string[]

  

// ReturnType<T>

declare function f1(): { a: number, b: string };

  

type T0 = ReturnType<() => string>; // string

type T1 = ReturnType<(s: string) => void>; // void

type T2 = ReturnType<(<T>() => T)>; // {}

type T3 = ReturnType<(<T extends X, X extends number[]>() => T)>; // number[]

type T4 = ReturnType<typeof f1>; // { a: number, b: string }

type T5 = ReturnType<any>; // any

type T6 = ReturnType<never>; // never

type T7 = ReturnType<string>; // Error

type T8 = ReturnType<Function>; // Error

  

// InstanceType<T>

class C {

x = 0;

y = 0;

}

  

type T0 = InstanceType<typeof C>; // C

type T1 = InstanceType<any>; // any

type T2 = InstanceType<never>; // never

type T3 = InstanceType<string>; // Error

type T4 = InstanceType<Function>; // Error

  

*/