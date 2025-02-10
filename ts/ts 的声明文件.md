## 声明文件  .d.ts

后缀名为`.d.ts`，文件中只包含与类型相关的代码，它们的作用只在于为**开发者提供类型信息，所以它们只在开发阶段起作用**。

总结起来有三个特点:

1. 不包含具体实现, 只包含类型声明代码
2. 开启 tsconfig.json 中的 `declaration: true`, 可以根据 ts 自动生成 .d.ts 文件
3. .d.ts 只用于 typescript 编译器进行类型检查, 不会包含在打包产物中.

### **你一定知道的 lib.d.ts**

当我们安装 typescript 的时候， 会顺带安装一个 lib.d.ts。 这个文件包含 javascript 运行时以及 DOM 中存在的各种属性声明， 方便我们在开发时获得类型提示。

### **我们为什么要了解声明文件？**

1. **知道如何编写一个带有类型的库.** 
   
   当我们开发自己的第三方库时， 使用 tsc 编译后， `.ts` 文件会变成可执行的 `.js` ，其它人使用你的 package 会执行 `.js` 文件，类型提示需要通过 `.d.ts` 文件来获得类型信息
2. **知道为如何全局方法添加类型**
   
   通过 script 标签引入的第三方 sdk，挂载了一些全局的方法，如果在 TS 中直接使用的话，会报 TS 类型错误，这时候就需要我们对这些全局的方法进行 TS 声明。
3. **知道如何解决第三方库没有类型** 
   
   比如一些古早的 npm library，比如 lodash， 可以通过 `@types/xxx` 下载对应的 types 库（如果有人写了的话），如果没有人提供的话你可以在自己的项目中新建一个 `.d.ts` 然后在里面编写 `declare module`



### **声明文件是如何被找到**

1. 比如有个第三方包名字叫  foo，那么 TypeScript 会在 `node_modules/foo` 中根据其  package.json 的 types 字段查找声明文件查找到的声明文件被作为该模块的声明文件. 

2. TypeScript 也会在`node_modules/@types/foo` 目录中查找声明文件，如果能找到就被作为 foo 模块的声明文件；

3. TypeScript 还会在我们的项目中根据 tsconfig.ts 的 including 查找 .d.ts 文件，如果遇到`declare module 'foo'`语句，则该声明被用作 foo 模块的声明。

   

### **项目中的 global.d.ts**

需要配合修改 tsconfig.json

```
// CSS modules
type CSSModuleClasses = Record<readonly string, string>;

declare module '*.module.css' {
  const classes: CSSModuleClasses;
  export default classes;
}
declare module '*.module.scss' {
  const classes: CSSModuleClasses;
  export default classes;
}

// CSS
declare module '*.css' {
  const css: string;
  export default css;
}
declare module '*.scss' {
  const css: string;
  export default css;
}

/** File type modules */
declare module '*.avif' {
  const src: string;
  export default src;
}

declare module '*.bmp' {
  const src: string;
  export default src;
}

declare module '*.gif' {
  const src: string;
  export default src;
}

declare module '*.svg' {
  const src: string;
  export default src;
}

declare module '*.jpg' {
  const src: string;
  export default src;
}

declare module '*.jpeg' {
  const src: string;
  export default src;
}

declare module '*.png' {
  const src: string;
  export default src;
}

declare module '*.webp' {
  const src: string;
  export default src;
}

declare module '*.vue' {
  import Vue from 'vue';

  export default Vue;
}

declare module 'vue-cookie' {
  import type { CookieOptions } from 'tiny-cookie';
  import type { default as VueType } from 'vue';

  const defaultExport: {
    install: (Vue: typeof VueType) => void;
    get: (key: string) => string | null;
    set: (key: string, value: string, options?: CookieOptions) => void;
    delete: (key: string, options?: CookieOptions) => void;
  };

  export default defaultExport;
}

declare module '@byted-growth/byted-passport-util' {
  export function encrypt(val: string): string;

  export function encryptParams<T extends object, U extends keyof T>(
    obj: T,
    props: U[],
  ): {
    [key in keyof Omit<T, U>]: T[key];
  } & {
    [key in keyof Pick<T, U>]: string;
  };
}

```

```
// argument.d.ts
import 'element-ui/types/message';
import type { ElDatePicker } from 'element-ui/types/date-picker';

/**
 * Add all type augmentation declarations in this file
 *
 * Note: Cannot add content in this file to global.d.ts, cause a 'import' statement will
 * break global type and module declarations
 */

declare module 'element-ui/lib/message-box' {
  import type { ElMessageBox } from 'element-ui/types/message-box';

  const MessageBox: ElMessageBox;
  export default MessageBox;
}
declare module 'vue' {
  import { ElButton as OriginClass } from 'element-ui/types/message-box';
  import type { ElementUIComponentSize, ButtonType } from 'element-ui/types/message-box';

  export declare class ElButton extends OriginClass {
    /** Button size */
    size: ElementUIComponentSize | 'xl';

    /** Button type */
    type: ButtonType | 'okee';
  }
  export interface GlobalComponents {
    ElButton: ElButton;
  }
}

declare module 'element-ui/lib/message' {
  import type { ElMessage } from 'element-ui/types/message';

  const Message: ElMessage;
  export default Message;
}
declare module 'element-ui/types/message' {
  interface ElMessage {
    closeAll: () => void;
  }
}

declare module 'element-ui/types/notification' {
  interface ElNotification {
    closeAll: () => void;
  }
}

declare module 'vue' {
  import type { DefineComponent } from 'vue';

  type ElDatePickerValue = Date | string | Date[] | string[] | [number, number];

  interface GlobalComponents {
    /**
     * Compatible with `value-format="timestamp"`
     */
    ElDatePicker: DefineComponent<
      Partial<
        Omit<ElDatePicker, 'focus' | 'value'> & {
          value: ElDatePickerValue;
          onChange: (value: ElDatePickerValue) => void;
          onInput: (value: ElDatePickerValue) => void;
          onFocus: (e: Event) => void;
          onBlur: (e: Event) => void;
        }
      >,
      object,
      object,
      object,
      Pick<ElDatePicker, 'focus'>
    >;
  }
}

```

```
// vue-i18n.d.ts
import type { Path, Values, Locale } from 'vue-i18n/types';
import type VueI18n from 'vue-i18n/types';

/**
 * Overloads VueI18n interface to avoid needing to cast return value to string.
 * @see https://github.com/kazupon/vue-i18n/issues/410
 */
declare module 'vue-i18n/types' {
  export default class VueI18n {
    t(key: Path, locale: Locale, values?: Values): string;
    t(key: Path, values?: Values): string;
  }
}

declare module 'vue/types/vue' {
  interface Vue {
    $t: (key: Path, values?: Values) => string;
    date: any;
  }
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  interface VueConstructor<V extends Vue = Vue> {
    i18n: typeof VueI18n.prototype;
    prototype: Vue;
  }
}

export default VueI18n;

```



## Declare 的应用

.d.ts 文件中的顶级声明必须以 "declare" 或 "export" 修饰符开头，否则会报错 xxx must start with either 'declare' or 'export' modifier

在没有 export 的情况下, 通过 declare 声明的类型或者变量或者模块，在 include 包含的文件范围内，都可以直接引用而不用去 import 或者import type 相应的变量或者类型。

declare 就是告诉 TS 编译器你担保这些变量和模块存在， 并声明了相应的类型， 编译的时候不要报错

### **你可以用 declare 声明**

- variable (*const*, *let*, *var*).
- type / interface
- class
- enum
- function
- module
- namespace

### **声明全局模块**

```
declare module '*.css' {
	const css: string;
	export default css;
}

declare module '*.jpg' {
	const src: string;
	export default src;
}

declare module '*.ttf' {
	const src: string;
	export default src;
}
```

否则在 import 这种类型的文件时会报错 “cannot find module or its corresponding type declaretions”

### **声明全局方法**

console.log(window.whiteScreen) 

如果不申明：类型“Window & typeof globalThis”上不存在属性“whiteScreen”。

```TypeScript
  <script
    src="https://sf-static.tiktokcdn.com/obj/goofy-sg/ttcm/ttcm_white_screen/dist/index~0.0.7.js"
    crossorigin="anonymous"
  ></script>
  <script>
    if (window.whiteScreen) {
      window.whiteScreen.init({
        fallback: '/fallback',
        redirectURI: '/',
        threshold: 2,
        bid: 'star_fe_i18n',
        env: '<%= htmlWebpackPlugin.options.buildType %>' === 'online' ? 'production' : 'test',
        release: '<%= htmlWebpackPlugin.options.appVersion %>',
        logOnly: '<%= htmlWebpackPlugin.options.NODE_ENV %>' === 'production' ? false : true,
        dom: '#app',
      });
    }
  </script>


// x.d.ts
declare interface Window {
  whiteScreen: {
    init: (options: WhiteScreenOptions) => void;
  };
}
```



### **模块扩展**

如果是需要扩展原有模块的话，需要在类型声明文件中先引用原有模块，再使用 `declare module` 扩展原有模块

```TypeScript
// types/moment-plugin/index.d.ts
import * as moment from 'moment';
declare module 'moment' {
    export function foo(): moment.CalendarKey;
}
// src/index.ts
import * as moment from 'moment';
import 'moment-plugin';

moment.foo();
```



### Declare global

```
// env.d.ts
export {};

declare global {
  namespace NodeJS {
    interface ProcessEnv {
      NODE_ENV: 'development' | 'production' | 'test';

      /**
       * 静态资源是否上传 CDN
       */
      VUE_APP_CDN: 'true' | 'false';

      /**
       * SCM 构建版本号
       */
      VUE_APP_VERSION: string;

      /**
       * SCM 构建类型
       *  - online: 线上
       *  - test: 测试
       *  - offline: 线下
       */
      BUILD_TYPE: 'online' | 'test' | 'offline';
    }
  }
}

```



说到 declare global 不得不先提到 typescript 中的模块；

在 TypeScript  中任何包含顶级 import 或者 export 的文件都被当成一个模块。

相反地，如果一个文件不带有顶级的 import 或者 export 声明，那么它的内容被视为全局可见的

```TypeScript
// foo.ts
const a = 1 

// bar.ts
const a = 2 //Cannot redeclare block-scoped variable 'a'.ts(2451)
```

上面两个文件中, a 变量都是全局可见的,所以报错了

```TypeScript
// a.ts
export {}
const a = 1

// b.ts
const a = 2 // ☑️
```

所以如下代码中， app.ts 无法访问到 Person 类型

```TypeScript
// index.d.ts
export {} // 已形成模块

declare interface Person {
  name: string;
}

// app.ts
const person: Person // Cannot find name 'Person'.ts
```

这个时候使用 declare global 就可以将 declare 类型的作用域提升到全局

```TypeScript
export {}

declare global {
 interface Person {
   name: string;
 }
}
```