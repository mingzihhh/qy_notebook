## js中的if（）判断和a==b

主要讨论js中if语句和a==b中的隐形类型转换问题。

### 一、if()判断

> if (condition) {
>
>  }

在js的if语句中，condition可以是任意表达式；而且对这个表达式求值的结果不一定是布尔值。js会自动调用Boolean()转换函数将这个表达式的结果转换为一个**布尔值**。

具体转换如表格所示：

| 类型      | 结果                                   |
| --------- | -------------------------------------- |
| Undefined | false                                  |
| Null      | false                                  |
| Boolean   | 直接判断                               |
| Number    | +0, −0, 或者 NaN 为 false, 其他为 true |
| String    | 空字符串为 false,其他都为 true         |
| Object    | true                                   |

举几个例子：

>if  ("")  {                              //空字符串，判断为false
>console.log('empty')
>}

> if  (" ")  {                             //空白字符串，中间有空白字符，判断为true
> console.log('empty')
> }

> if  ([0])  {                               //对象，判断为true
> console.log('array')
> }

### 二、a == b

- 对于"=="比较，一般情况下是先向**数值**转换再进行比较。

| type      | Result                                                       |
| --------- | :----------------------------------------------------------- |
| Undefined | NaN                                                          |
| Null      | 0                                                            |
| Boolean   | ture -> 1, false -> 0                                        |
| String    | “abc” -> NaN, “123"  -> 123, ""->0                           |
| Object    | 先调用 .valueOf 方法获取结果。 如果没定义，再尝试调用 .toString方法 |



- **具体规律如下**：

1. 如果存在NaN，一律返回false。
2. 如果a或b是布尔值，就将布尔先转换为数值。（false是0，true是1）
3. 如果a或b是字符串, 有三种情况：

> 1、对方是对象，对象使用toString()或者valueOf()进行转换；
>
> 2、对方是数值，字符串转数值；
>
> 3、对方是字符串，直接比较；
>
> 4、其他返回false

4. 如果a或b是数值，对方是对象，对象取valueOf()或者toString()进行比较, 其他一律返回false
5. null, undefined不会进行类型转换, 但它们俩相等     



举几个例子：

>"     " == 0           //true
>
>{  } == true         //false
>
>"hello" == true   //fasle



参考资料：

http://book.jirengu.com/fe/

https://www.haorooms.com/post/js_yinxingleixing