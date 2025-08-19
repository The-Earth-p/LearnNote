# TypeScript

就是JavaScript的超集，是它的进化版，支持静态类型检查了，也就是运行前就能知道报错，包括逻辑漏洞，拼写错误等等

## TS编译成JS

捞的方法：安装typescript，命令行编译：tsc index，会生成一个ts对应的js文件。

高级方法：自动化编译，直接tsc --init命令，会生成一个config文件，写清楚ts应该怎么转换为js，然后写tsc --watch，就能够实时监控ts文件的更改，ts改了js也一起改，可以在config里面更改noEmitOnError这个选项，让它不要注释掉，设置值为True，即可保证在ts有错的时候不马上同步到js文件中。

上述的编译是自己写ts文件用的，如果用vue、react这种官方脚手架，框架会自动编译的，直接运行就行了。

## 类型声明

变量的定义，函数的形参，都要指定数据类型（形如let a : string)，如果在定义的时候直接指定值，就是字面量类型，不能改变。定义变量指明数据类型时，大小写不同，也会有不同：小写是基元，大写是包装对象。一般写小写，大写用于底层自动装箱（字面量想要用length方法）

Ts中新增了6个数据类型和2个自定义数据类型

## 常用数据类型

any：这个变量存啥都行，这个变量可以赋值给任意变量，一般不要用这个，太容易破坏别人了

unknown：目前不知道的数据类型，同样可以存任何类型的数据，和any很像，但是unknown不能赋值给别人，不会破坏别人。但是如果是同样数据类型，那可以通过typeof进行if判断，或者通过断言进行赋值。实际类型的一些对应的方法，也是和这个同类型赋值一样，要么if，要么断言（as 数据类型）

never：这个变量什么值都不能存，不能有值，如果是函数则不能返回值，不能结束。一般不用来限制任何变量，这个一般是主动推断出来的

void：通常用于函数返回值声明，被它定义就是“空”，不能拿这种值来做文章。可以接收的空是undefined定义的空，undefined是函数的隐式返回值，但是void和undefined是不一样的，void不能被用来做文章，而undefined可以。

object：实际使用的较少，定义为这个类型就能够存储非原始类型，比如对象列表啥的。但是如果开头大写：Object，那么不仅非原始类型，原始类型也可以存，除了null和undefined存不了。存的范围太大了，所以一般不用。

tuple：元组，特殊的数组类型，但是固定其中元素的个数和元素的数据类型，？表示可有可无，...string[]表示可以有无限个string数据

enum：枚举，定义一组命名常量。下面这个其实就是一个对象，并且其中的元素还会反向映射一下，比如有direction[0]=up和direction.up=0，但是仅限数字，字符串没有反向映射。这个数据类型中的数据都是只读，不可更改

```typescript
//加个const生成的js文件中就不复杂，编译时内联
const enum Direction{
up="up",
down=15,//下面两个自动变成16和17
left,
right
}
```

type：为任意类型创建别名。一般用来做一个类型的联合，表示既可以是number，也可以是string

```typescript
//类型的联合，表示或
type Status=number|string
let a:Status
a=66
a='hello'
//这里的元素的分隔和声明对象是一样的
type Area={
    height:number
    width:number
}
type Address={
    num:number
    room:string
}
//类型的与联合，之后若定义为House，则需要总共4个元素赋值
type House=Area&Address
```



## 声明对象类型

```typescript
//声明对象时，其内含有的参数可以通过"，""；"或者换行来分隔，但是实例化的时候必须用","
let person:{
name:string
age?:number//问号表示可以有
[key:string]:any//可以在创建实体的时候额外添加任意数量的键值对，即索引签名
}
```

## 声明函数类型

```typescript
//声明函数的参数类型以及返回数据类型，这里的“=>”作用就是指明哪个是返回的数据类型，是ts中的箭头，不是js中的代替function的箭头函数
let count:(a:numbere)=>number
//实现函数的具体定义
count=function(a){
    return a
}
//也可以写成
count=(a)=>{
    return a
}
//也可以直接和function融合起来，不用先单独声明一下count
function count(a:number):number{
    return a
}
```

## 类

1.需要指明构造函数，可以直接在构造器参数里面指明本类具有的属性，形式为：public weight:number

2.继承类之后要调用一下父类的构造器，像python一样通过super调用即可

3.子类中的继承下来的属性不需要再指明public这种属性值

## 接口

1.接口类，和后端springboot中的东西一样的，可以定义接口类

2.可以定义接口对象：这个接口可以当作一个类型来用，其中可以指定有哪些属性、方法等等，不用implements

3.接口之间可以相互继承，extends实现

```typescript
interface IPerson{
name:string,
readonly gender:string,
id?:number,
run:(n:number)=>void
}
interface IStudent extends IPerson{
age:number
}
```

