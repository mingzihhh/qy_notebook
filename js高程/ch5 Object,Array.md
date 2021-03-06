## ch5 Object,Array

###  一、Object类型

#### 创建

1. new+构造函数Object()

2. 对象字面量

   - 空花括号代表定义只包含默认属性和方法的对象
   - 不会调用Object()

   - 建议只在考虑对象属性名可读性时使用

   - 可以当函数参数，用来向函数传递大量参数

     建议对必需值使用命名参数，使用对象字面量封装多个可选参数

#### 访问属性

1. 点表示法，一般建议
2. 方括号表示法
   - 属性必须以字符串形式放在括号中
   - 可以通过变量访问属性
   - 属性名包括导致语法错误的字符，或使用关键字或保留字

#### 检查声明

1. 检查一个全局变量是否被声明

   ```if ('a' in window) {
   if ('a' in window) {
   // 变量 a 声明过
   } else {
   // 变量 a 未声明
   }
   ```

2. 查看一个对象本身的所有属性，可以使用`Object.keys`方法。

#### 删除属性

1. `delete`命令用于删除对象的属性，删除成功后返回`true`。
2. 注意，删除一个不存在的属性，`delete`不报错，而且返回`true`。因此，不能根据`delete`命令的结果，认定某个属性是存在的，只能保证读取这个属性肯定得到`undefined`。
3. `delete`命令不能删除`var`命令声明的变量，只能用来删除属性。

### 二、Array类型

#### 特点

1. 数组每一项可以保存任何类型属性
2. 大小可以动态调整

#### 创建

1. 使用构造函数Array()，可以省略new
   - 传递数值，为length
   - 传递其他类型参数，为数组内容
2. 数组字面量 []

#### 读取设置

1. []+基于0的数字索引
2. 通过length属性从数组末尾移除项或向数组添加新项

#### 检测

1. 一个网页或者一个全局作用域中，instanceof操作符
   - 确定数组对象，所以适用于只能有一个Array构造函数的情况
2. ES5，IE9+，Array.isArray()
   - 确定某个值是不是数组

#### 转换

1. valueOf()，默认逗号分隔
2. toString()，数组的每一项调用各自的toLocalString()方法，默认逗号分隔
3. toLocalString()，数组的每一项调用各自的toLocalString()，默认逗号分隔
4. join()，设定不同分隔符，返回字符串

#### 栈方法

让数组模拟栈，操作数组末尾，即栈顶部

1. push()，返回修改后的数组长度
2. pop()，返回移除项

#### 队列方法

让数组模拟队列

1. push()

2. shift()，移除第一个项并返回该项
3. unshift()，在数组前端添加若干个项，返回新数组长度；配合pop()可模拟反向序列

#### 重排序方法

1. reverse()，反向排列，返回排序后的数组
2. sort()，返回排序后的数组
   - 默认升序排列
   - 实际上调用每项的toString()，然后比较字符串
   - 可以接收比较函数

#### 操作方法

1. concat()，

   - 基于当前数组所有的项创建新数组，原数组保持不变，返回新数组
   - 参数可以是值，也可以是数组

2. slice()

   - 基于当前数组中的一或多个项创建新数组，原数组保持不变

   - 一或两个参数，返回项的起始或结束位置，[ )

   - 参数有负数，则数组长度加该数来确定相应位置；

     结束位置小于开始位置，返回空数组

3. splice()

   - 向数组中部插入项，返回一个数组，包含原数组中删除的项
   - 2个参数，删除：删除的第一项的位置，要删除的项数，[ )
   - 3个参数，插入：起始位置，0，要插入的任意数量的项
   - 3个参数，替换：起始位置，要删除的项数，要插入的任意数量的项，插入项和删除项不必相等
   - splice的第一个参数是绝对索引值，而不是相对于数组索引，第二个参数并不是删除元素的个数，而是删除动作执行多少次，并不是按数组实际索引移动，而是连续移动。同时调整后面元素索引，前面索引不理会

#### 位置方法

2个参数：要查找的项和查找起点，返回找到的位置或-1，===，IE9+

1. indexOf()，从数组开头向后查找，
2. lastIndexOf()，从数组末尾向前查找

#### 迭代方法

遍历数组，原数组不变；

参数为一个回调函数，回调函数有三个参数：

 function(item,index,array)

当前元素，当前元素索引值，整个数组   

1. every()

   **所有**函数的每个回调函数都返回true的时候才会返回true，当遇到false的时候终止执行，返回false

2. some()

   **存在**有一个回调函数返回true的时候终止执行并返回true，否则返回false

3. forEach()

   无返回值

4. map()

   回调函数返回值组成一个新数组返回，新数组索引结构和原数组一致

5. filter()

   返回数组的一个子集，回调函数用于逻辑判断是否返回，返回true则把当前元素加入到返回数组中，false则不加；适用于查询符合某些条件的所有数组项

#### 归并方法

2个参数：每一项上调用的函数，（可选）作为归并基础的初始值

传入函数有4个参数：function(prev,cur,index,array)，这个函数返回的任何值都会作为第一个参数自动传给第二项

第一次迭代发生在第二项，所以prev为第一项，cur为第二项

1. reduce()，从数组的第一项开始，逐个遍历到最后
2. reduceRight()，从数组的最后一项，遍历到第一项



