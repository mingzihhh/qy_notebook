## 表单与v-model

#### v-model

VUE提供了v-­model指令， 用于在表单类元素上双向绑定事件

- **input和textarea**

  所显示的值只依赖于所绑定的数据，不再关心初始化时的插入的value

-  **单选框**  <input type='radio'>

  1. 单个单选按钮，直接用v­-bind绑定一个布尔值，用v­-model是不可以的

  2. 如果是组合使用，就需要v­-model来配合value使用，绑定选中的单选框的value值，此处所绑定的初始值可以随意给

    ![1551343068209](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1551343068209.png)

-  **复选框** <input type='checkbox'>

  1. 单个复选框，直接用定一个布尔值，可以用v­-model可以用v-­bind
  2. 多个复选框– 如果是组合使用，就需要v-­model来配合value使用。v-­model绑定一个数组；如果绑定的是字符串，则会转化为true或false，与所有绑定的复选框的checked属性相对应，全部选中或全部不选

-  **下拉框** select option

  1. 如果是单选，所绑定的value值初始化可以为数组，也可以为字符串，有value直接优先匹配一个value值，没有value就匹配一个text值

  2. 如果是多选，就需要v-­model来配合value使用，v­-model绑定一个数组，与复选框类似，加multiple关键字

    ![1551343699586](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1551343699586.png)

  3. v­-model一定是绑定在select标签上

  ##### 总结

  如果是单选，初始化最好给定字符串，因为v­model此时绑定的是静态字符串或者布尔值；
  如果是多选，初始化最好给定一个数组



#### 绑定值

- 单选按钮

  只需要用v­-bind给单个单选框绑定一个value值，此时，v­-model绑定的也是他的value值

- 复选框

  用true-value和false-value控制选中和不选中时的value不一样

- 下拉框
  在select标签上绑定value值对option并没有影响



#### 修饰符

**.lazy**

在默认情况下，`v-model` 在每次 `input` 事件触发后将输入框的值与数据进行同步 。你可以添加 `lazy` 修饰符，从而转变为使用 `change`事件进行同步,可以使其在失去焦点或者敲回车键之后更新、

**.number**

自动将用户的输入值转为数值类型

**.trim**

自动过滤用户输入的首尾空白字符