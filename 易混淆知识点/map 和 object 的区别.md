在 JavaScript 中，`Map` 和普通对象（`Object`）有一些关键的区别，主要在于它们的用途、特性和行为。以下是详细的对比：

### 1. Key 类型
- **Map**: 
  - `Map` 可以使用任意类型的值作为键，包括对象、函数、基本类型（如字符串、数字、布尔值等）。
  - 例如：
    ```javascript
    let map = new Map();
    map.set('key1', 'value1');
    map.set(123, 'value2');
    map.set(true, 'value3');
    map.set({a: 1}, 'value4');
    ```

- **Object**:
  - 对象的键必须是字符串或者 Symbol。即使使用其他类型作为键，JavaScript 会将其强制转换为字符串。
  - 例如：
    ```javascript
    let obj = {};
    obj['key1'] = 'value1';
    obj[123] = 'value2'; // 实际上被转换为字符串 '123'
    obj[true] = 'value3'; // 实际上被转换为字符串 'true'
    obj[{a: 1}] = 'value4'; // 实际上被转换为字符串 '[object Object]'
    ```

### 2. 保持键的顺序
- **Map**:
  - `Map` 以插入顺序保存键值对。这意味着你迭代 `Map` 时，**键值对的顺序是按照它们被插入的顺序**。
  
- **Object**:
  - 对象属性的顺序不一定是按插入顺序，尤其是在数值键的情况下。ES6 规范规定，对象属性的顺序在特定情况下会有所不同。

### 3. 键值对的大小
- **Map**:
  - 可以通过 `size` 属性直接获取 `Map` 中键值对的数量。
  - 例如：
    ```javascript
    let map = new Map();
    map.set('key1', 'value1');
    console.log(map.size); // 1
    ```

- **Object**:
  - 对象没有直接的属性来获取键值对的数量。需要使用 `Object.keys()` 或其他方法手动计算。
  - 例如：
    ```javascript
    let obj = { key1: 'value1' };
    console.log(Object.keys(obj).length); // 1
    ```

### 4. 性能
- **Map**:
  - 在频繁添加和删除键值对的场景下，`Map` 通常表现得比对象更好，因为它是为这种用途设计的。
  - 在 JavaScript 引擎中通常实现为哈希表（hash table），哈希表在处理键的查找、插入和删除操作时具有平均时间复杂度为 O(1)的特点。这意味着这些操作的时间复杂度与数据的数量无关，能够快速处理大量键值对。
  
- **Object**:
  - 虽然对象在大多数情况下也能胜任，但在需要频繁修改键值对时，在性能可能不如 `Map`。
  - 对象在 JavaScript 中的实现更接近于树形结构或链表结构，这使得在处理键的查找、插入和删除操作时，时间复杂度可能会接近 O(n)，尤其是在键的数量较多时。虽然现代 JavaScript 引擎对对象的性能进行了大量优化，但对象的设计初衷并不是为了频繁修改的高效键值对存储。

### 5. 使用场景
- **Map**:
  - 更适合需要频繁增删键值对、且需要保证键的类型多样化和顺序的场景。

- **Object**:
  - 适合简单的数据结构存储和访问，特别是当键是已知的字符串时。

### 例子对比

```javascript
// 使用 Map
let map = new Map();
map.set('key1', 'value1');
map.set(123, 'value2');
map.set(true, 'value3');

console.log(map.get('key1')); // 'value1'
console.log(map.get(123)); // 'value2'
console.log(map.get(true)); // 'value3'

// 使用 Object
let obj = {};
obj['key1'] = 'value1';
obj[123] = 'value2'; // 实际上键是字符串 '123'
obj[true] = 'value3'; // 实际上键是字符串 'true'

console.log(obj['key1']); // 'value1'
console.log(obj['123']); // 'value2'
console.log(obj['true']); // 'value3'
```

总结来说，`Map` 提供了更强大的键值对存储功能，适用于更复杂和频繁操作的场景；而对象则适用于简单的键值对存储和基本操作。