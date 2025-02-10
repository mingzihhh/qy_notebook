#### RTL

**客户侧：**

1. Webpack 配合 [postcss-inline-rtl](https://www.npmjs.com/package/postcss-inline-rtl) 插件，自动转换你写的 left, right 等关键词，自动解决 90% 的场景
2. 各种细节处手动适配：比如 Element Checkbox 不认 RTL、 el-tabs 的 active bar 替换……
3. Vue | Sass RTL mixins

- html： 新建 html 一定要设置 **dir** 和 **lang** 属性
- CSS：
  - 遇到没有在 RTL 下自动翻转的图标或元素使用 **flip-when-rtl**  (多用于一些方向类 icon)
    - 因为内部使用了 `transform: scaleX(-1)` 来实现反转内容，所以如果元素自己本身带了些 transform 规则，可以这么写：`@include flip-when-rtl(translateY(-50%))`
  - RTL 下特殊适配的时候使用  **when-rtl** 
  - 需要在 RTL 下保持原来方向的时候使用  **keep-ltr-when-rtl** 
  - Flex-box 的顺序需要颠倒的时候使用  **row-reverse-when-rtl** 

```
/* RTL mixins
 -------------------------- */
@mixin when-ltr() {
  @at-root [dir='ltr'] & {
    @content;
  }
}

@mixin when-rtl() {
  @at-root [dir='rtl'] & {
    @content;
  }
}

@mixin flip-when-rtl($other-transform-rules: '') {
  transform: $other-transform-rules;

  @at-root [dir='rtl'] & {
    transform: scaleX(-1) #{$other-transform-rules};
  }
}

@mixin keep-ltr-when-rtl() {
  @at-root [dir='rtl'] & {
    direction: ltr;
  }
}

@mixin row-reverse-when-rtl() {
  @at-root [dir='rtl'] & {
    flex-direction: row-reverse;
  }
}
```

- JavaScript

  - 检查新增的依赖方 SDK 是否支持 RTL，例如 **feelgood**、**@byted/common-login-i18n-sdk** (这些已跟进适配）
  - 以后[新增语言](https://bytedance.feishu.cn/wiki/wikcnHyYjhOUtZS1it8fqo7lrmd)时，需要指定是否是 RTL 语言
  - `@/locale/utils` 内还提供了 isRTL 方法用来检查当前 document 的 direction

  ```
  export function isRTL() {
    return document.documentElement.dir.toLowerCase() === 'rtl';
  }
  ```

  - 像数字范围 18-24/24-35/35+ 在 RTL 需要适配用 numberRangeStrToRTL

  ```
  /**
   * Make number-range string to RTL format
   * @param {string} str eg. 18-24, 25-35, 35+
   * @returns {string}
   */
  export function numberRangeStrToRTL(str) {
    if (typeof str !== 'string' || !isRTL()) {
      return str;
    }
  
    // eg. 18-24 -> 24-18
    if (str.includes('-')) {
      return str.split('-').reverse()
        .join('-');
    }
    // eg. 35+ -> +35
    // note that under dir=rtl, 35+ will be rendered as +35, vice versa
    if (str.endsWith('+')) {
      str = '+' + str.slice(0, -1);
    }
  
    return str;
  }
  ```

- Vue/Element UI

  - `<el-input />` 和 `<el-input type="textarea" />` 在展示 word limit 时**不能**使用 element 原生的 show-word-limit 及 max-len 属性（不适配 rtl），需要自己实现
  - 提供判断是否是 rtl 的 mixin

  ```
  import { defineComponent } from 'vue';
  import { detectUserPreferredLanguage, getLangMetasByCode, isRTL } from '@/locale/utils';
  import { useEventBus } from '@/utils/bus';
  
  export default defineComponent({
    data() {
      return {
        isRTL: isRTL(),
      };
    },
  
    setup() {
      const eventBus = useEventBus();
      return { eventBus };
    },
  
    created() {
      const lang = detectUserPreferredLanguage();
      const isRTLLanguage = getLangMetasByCode(lang).rtl;
      this.onLanguageChange(isRTLLanguage);
      this.eventBus.on('language-change', () => {
        this.onLanguageChange();
      });
    },
    methods: {
      onLanguageChange() {
        this.isRTL = isRTL();
      },
    },
  });
  ```



**达人侧:**

- 组件库默认支持 rtl，根据语言在最外层的 provider 中注入是否是 rtl
- 需要调用下 useSetRTL，为 html 设置正确的 dir 属性

```
const useSetRTL = (): void => {
  useEffect(() => {
    const rootEl = document.documentElement;
    rootEl.dir = isArab ? 'rtl' : 'ltr';
  }, []);
};
```

- 对于自己书写的 CSS， 构建时有 [postcss-rtl](https://www.npmjs.com/package/postcss-rtl) 进行转化