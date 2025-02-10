在 TypeScript (TS) 中，`type` 和 `interface` 都可以用于定义对象的结构或数据类型，但它们有一些不同的用途和特性。以下是它们的主要区别：

### 1. **基本用法**

- **`interface`**：主要用于描述对象的形状（即属性和方法的类型）。通常用于定义对象的结构。

  ```typescript
  interface Person {
    name: string;
    age: number;
  }
  ```

- **`type`**：可以定义各种类型，包括基本类型、联合类型、元组类型等。更为通用，除了定义对象类型外，还可以用来定义其他类型。

  ```typescript
  type Person = {
    name: string;
    age: number;
  };
  ```

### 2. **扩展与合并**

- **`interface`**：

  - 可以通过扩展（`extends`）来继承其他接口

  - **可以通过声明合并扩展**，即同名接口会合并它们的属性和方法


  ```typescript
  interface Person {
    name: string;
  }
  
  interface Person {
    age: number;
  }
  
  // 合并后的 Person
  const person: Person = {
    name: "Alice",
    age: 30,
  };
  
  // 接口继承
  interface Employee extends Person {
    salary: number;
  }
  ```

- **`type`**：不能自动合并，但可以通过联合类型（`&`）来扩展其他类型。**不能像接口那样重复声明并合并**。

  ```typescript
  type Person = {
    name: string;
  };
  
  type Employee = Person & {
    salary: number;
  };
  ```

- **`type`和`interface`定义的类型也可以互相复用**

### 3. **功能差异**

- **`type`**：
  - 可以用于定义基本类型、联合类型、交叉类型、元组、以及复杂的类型组合。
  - 可以定义例如联合类型、映射类型等更复杂的类型。
  - 可以声明基本类型别名
  - 可以使用 typeof 获取实例的类型进行赋值
  
  ```typescript
  type StringOrNumber = string | number;
  type Point = [number, number];
  type Name = string;
  
  let div = document.createElement('div');
  type B = typeof div
  ```
  
- **`interface`**：
  
  - 可以声明合并
  - 更专注于描述对象的结构。
  - 通常在需要使用面向对象的概念，如实现和扩展时使用。
  

### 4. **声明合并**

- **`interface`**：支持声明合并，这意味着可以多次声明同一个接口，并且它们的定义会自动合并。这在大型代码库或需要扩展库的类型定义时很有用。

  ```typescript
  interface Person {
    name: string;
  }

  interface Person {
    age: number;
  }

  // Person 现在包含 name 和 age 属性
  const person: Person = {
    name: "Alice",
    age: 30,
  };
  ```

- **`type`**：不支持声明合并。如果两次定义同名的 `type`，会引发错误。

### 5. **使用场景的差异**

- **`interface`** 更适合用于定义对象或类的结构，特别是在需要使用面向对象特性（如继承）时, 比如一些公共的外部 API。

```
interface ClockInterface {
	currentTime: Date;
	setTime(d: Date): void;
}

class Clock implements ClockInterface {
	currentTime: Date = new Date();
	setTime(d: Date): {
		this.currentTime = d;
	};
}
```

- **`type`** 更适合定义更复杂的类型，如联合类型、交叉类型、或需要使用元组时。

### 总结
- 使用 `interface` 定义对象结构或类的接口。
- 使用 `type` 定义更复杂或更灵活的类型，如联合类型或交叉类型。
- 由于同名接口会自动合并，而同名类型别名会冲突，我推荐使用`interface`定义组件属性。这样，使用者可以通过`declare module`语句自由扩展组件属性，增强了代码的灵活性和可扩展性。
  