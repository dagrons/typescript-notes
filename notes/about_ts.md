> 所谓的程序, 不过是人为规定的执行路径
> if else 可以实现执行路径的分流
> while for 可以实现执行路径的迂回

> 在没有结构化编程的时候, 用jz <pos>代替分流, 用jz <neg>代替迂回

# about ts
1. 可能有人会好奇, js中不是有类型吗？then why ts?

是的, js有number, boolean, string, object, function, undefined六种类型, 乍一看貌似没有问题, 但是仔细想想, js缺了啥, 
答案是缺乏扩充类型和检查类型的能力, 在其他语言中,  class定义可以实现扩充类型的功能, 并且间接实现了类型检查, 但在js中, 所有class的instance都是object类(python中是有类型的哦!), 这样的信息本身就没有任何意义, 我们根本无法判断它的shape, 即我们无法从一个object的类型中判断出它应该具有哪些属性和方法, 对于类, 我们可以用instanceof判定class类型, 但在js中, 我们最好不要使用类, 那如何定义类型呢, ts提供的类型注解工具允许我们定义struct in C类似的类型, 并支持为函数参数和返回值定义类型

## strictness

在默认情况下, tsc不检查null/undefined的类型, 即null/undefined类型的值可以fit/pass所有类型检查,  可以通过--strict选项对null/undefined进行检查, 

## noImplicitAny & strictNullChecks

正常情况下, 当ts无法infei一个变量的类型时, 会将其默认注解为any, noImplicitAny会阻止这种行(对显示声明的any没有影响)

strictNullChecks会对null/undefined变量进行类型检查

## ts支持的类型注解

之前我们说到在js中, 只有number, boolean, string, object, function, undefined七种类型, 下面我们看看ts为我们提供的扩充注解类型

0. typealias: 类似于C语言中的typedef, 用于为类型命名
```typescript
type Point = {
    x: number;
    y: number;
};
```
1. string, number, boolean 
2. Arrays: string[] or Array<string>
3. any: 即对类型不做检查
4. union type: 一般配合typeof使用 // eg: id: string|number
5. functioin type annotation
```typescript
fn: (a: string) => void
fn: (a: int, b: int) => int
```
6. interface type: 
```typescript
interface Point { // 类似于C语言中的结构体, 支持继承
    x: number,
    y: number
}

Interface 3DPoint extends Point {
    z: number
}
``` 
7. type assertions: 可以对未知类型的值进行类型断言
```typescript
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement; // or
const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");
```

## Less Common Primitives
1. bigint: es2020以后开始支持
```typescript
const onHundred: bigint = BigInt(100);
const anotherHundred: bigint = 100n;
```
2. symbol: global unique reference 
```typescript
const firstName = Symbol("name");
const secondName = Symbol("name");

firstName === secondName // false
```

## Narrowing
> 静态分析可以分析所有可能的执行路径, 并根据*type guard*确定当前执行路径下的变量类型
所谓narrowing, 就是指ts在静态分析时, 遇到特殊语句例如: if (typeof xxx === yyy) 或者 if (instanceof xxx === yyy)（（被称为type guard) 时, 会将当前类型特殊化, 即ts具有这样的能力, 解析这些语句并产生预期的效果, 常见的type guard包括:
1. typeof guards
2. truthiness narrowing: 常用于null和undefined类型判定
3. instanceof: 对class类型判定
4. ===: x===y判断x和y为相同类型的type, 常用于多态方法确保两个参数为相同类型, 虽然更推荐用type parameter
5. control flow analysis
6. type predicate: user-defined type guard, 实现对type guard的扩充
```typescript
function isFish(pet: Fish|Bird): pet is Fish {
    return (pet as Fish).swim !== undefined;
}

let pet = getSmallPet();

if (isFish(pet)) {
    pet.swim();
}
else {
    pet.fly();
}
```
7. never: 不可能的type, 经过之前的执行路径到这, 已经没有可选type
   

**NOTE: typescript具有极强的类型推断能力, 在C/C++中, 任何变量都是单一类型, 所以不需要narrowing这个过程, 而在ts中允许变量是多种类型, 但在代码逻辑中, 我们往往需要根据类型划分执行路径, ts提供了narrowing的机制, 可以智能判断我们当前执行路径下的变量类型, 从而实现narrowing**

**NOTE: type guard可以理解为当前执行路径下的一个门卫, 每遇到一个guard, 都会从你变量的可能类型中拿走部分类型, 使得你在下面的执行路径中携带更少的可能类型**

## More on Functions

1. 基本语法:
```typescript
fn: (a: string) => void // 如果参数没有指定类型, 默认为any
```
2. call signatures: define a callable object with properties
```typescript
type DescribableFunction = {
    description: string;
    (someArg: number): boolean;
};

// 注意和下面例子的区别
type nn = {
    description: string;
    fn: (someArgs: number) => boolean;
} // this is a not a callble object!
```
