## Vue3 代码规范

### 目录规范

### 模块命名

采用 kebab-case 命名方式，多个 noun 单词用短横线"-"分割，一般不超过 2 个单词，命名采用模块-子模块方式，这样 module 会按模块级别归类排序。

如 vue 的编译器 compiler 分 core、dom、ssr、sfc 模块，因此命名为 compiler-core，将根模块放在第一个单词。

```javascript
//BAD
compilerCore;
compilerDom;
compilerSSR;

// GOOD
vue;
vue - compat;

compiler - core;
compiler - dom;
compiler - ssr;
compiler - sfc;

runtime - core;
runtime - dom;
runtime - test;
```

### 文件夹命名

文件夹命名需要特别注意，windows 系统对大小写不敏感，如 components 和 Components 在 windows 系统下会认为是同一个，因此把名字由 Components 改为 components，git 是识别不到差异。由于文件夹大小写重命名导致在 linux 环境编译报错的问题屡见不鲜。

和 module 类似，采用 kebab-case 规则，以 noun 单词命名。如果为同类功能的文件夹，则一般以复数形式结尾，常用的有 components、utils 等等。

```javascript
layouts;
components;
directives;
hooks;
utils;
patches;
scripts;
transforms;
services;
```

### 组件文件夹命名

一个复杂组件通常会拆分为多个文件夹，每个文件夹以 kebab-case 方式命名。

```javascript
dropdown;
dropdown - item;
dropdown - menu;

checkbox;
checkbox - button;
checkbox - group;
```

### assets 目录

assets 存放静态资源，images, styles, icons、svgs 等静态目录以复数形式结尾，静态资源文件以 kebab-case 形式命名。

```
- assets
  - images
  - icons
  - styles
  - svgs
    - ant-design-vue.svg
```

### 组件规范

### 组件结构化

组件编写大部分事件聚焦逻辑，因此将 script 放在顶层，文件结构可按 sript、template、style 顺序布局。

```javascript

<script setup lang="ts">
...
</script>
<template>
...
</template>
<style lang="less" scoped>
...
</style>
```

script 可以按 props、emits、ref、 computed、watch、methods、events 顺序排列代码。为你而是每个文件顺序统一，可以使用 vscode 定义代码片段，按以上顺序添加注释，开发组件直接在对应注释下添加对应代码。

```javascript

<script lang="ts" setup>
/** imports  */
import { computed, ref, useSlots } from 'vue'
import { ElIcon } from '@element-plus/components/icon'
import { TypeComponents, TypeComponentsMap } from '@element-plus/utils'
import { useNamespace } from '@element-plus/hooks'
import { alertEmits, alertProps } from './alert'

/** props  */
const props = defineProps(alertProps)

/** emits  */
const emit = defineEmits(alertEmits)

/** refs  */
const visible = ref(true)

/** computed  */
const iconComponent = computed(() => TypeComponentsMap[props.type])
const iconClass = computed(() => [
  ns.e('icon'),
  { [ns.is('big')]: !!props.description || !!slots.default },
])
const withDescription = computed(() => {
  return { 'with-description': props.description || slots.default }
})

/** methods  */
const close = (evt: MouseEvent) => {
  visible.value = false
  emit('close', evt)
}
</script>
```

### 组件中单引号、双引号

html 中、vue 的 template 中标签属性使用双引号

```html
<component
  :is="tag"
  ref="_ref"
  v-bind="_props"
  :class="buttonKls"
  :style="buttonStyle"
  @click="handleClick"
></component>
```

所有 js 中的字符串使用单引号

```javascript
ns.is('disabled', _disabled.value),
  ns.is('loading', props.loading),
  ns.is('plain', props.plain),
  ns.is('round', props.round),
```

所有 js 中的代码行换行，要么统一使用分号";"，要么统一不使用分号，不能混着用。

```javascript
// BAD
import { buttonProps } from "./button";
import type { ExtractPropTypes } from "vue";

// GOOD
import { buttonProps } from "./button";
import type { ExtractPropTypes } from "vue";
```

### 组件名命名规范

采用 kebab-case，一个项目必须保持统一，组件名一般不超过 2 到 3 个单词。

```javascript
checkbox - button.vue;
checkbox - group.vue;
```

### 组件以高优单词开头

组件命名以高优先单词开头，以描述性单词结尾，重要单词放前面可实现有序排列。例如查询组件，使用 Search 前缀，输入组件命名为 SearchInputXXX，而按钮组件使用 SearchButtonXXX。

```
components/
|- search-button.vue
|- search-button-clear.vue
|- search-input.vue
|- search-input-query.vue
|- settings-checkbox.vue
|- settings-checkbox-terms.vue
```

### 父、子组件命名

和父组件紧密相关的子组件应该以父组件名作为前缀命名，例如：

```

components
|- todo-list.vue
|- todo-list-item.vue
|- todo-list-item-button.vue
```

### 组件名应使用完整单词

组件命名不能使用缩写，而应该使用完整单词组成的名称。因为时间一长，或者换个人，可能完全看不出 SdSettings 组件为何意。由于文件引入一般都有智能提示，因此也不容易出现拼写类错误。

```

BAD
components/
|- SdSettings.vue
|- UProfOpts.vue

GOOD
components/
|- student-dashboard-settings.vue
|- user-profile-options.vue
```

### 组件 Props 命名

组件 props 定义使用 lowerCamelCase 命名，在 template 中使用组件时，使用 kebab-case 规则。例如定义 greetingText 属性，模板使用方式为 greeting-text=""。

```javasript
/ BAD
// component
const props = defineProps({
  'greeting-text': String
})
// for in-DOM templates
<welcome-message greetingText="hi"></welcome-message>

// GOOD
component
const props = defineProps({
  greetingText: String
})
<WelcomeMessage greeting-text="hi"/>
```

### 组件事件命名规则

事件命名需要注意定义和 template 使用。

事件定义：通常使用一个 verb 定义的事件名居多，例如 open、close、click、change、focus、blur、select。对于状态类事件定义一般采用 noun + "-" + verb 形式，例如 state-change、active-change。生命周期类通常采用 prep + verb 形式表示事件，例如 before-enter、after-enter。或者是标识动作的事件，采用 verb + "-" + noun。

```javascript
/ verb
defineEmits(['open'])
defineEmits(['close'])
defineEmits(['focus'])

// noun + "-" + verb
defineEmits(['state-change'])

// verb + "-" + noun
defineEmits(['open-menu'])

// 生命周期
'before-enter'
'before-leave'
'after-enter'
'after-leave'
```

事件定义推荐使用对象的形式，而不是简写。对象形式能够直观看到每一个事件入参和返回值类型。

```javascript
export const cascaderEmits = {
  focus: (evt: FocusEvent) => evt instanceof FocusEvent,
  blur: (evt: FocusEvent) => evt instanceof FocusEvent,
  clear: () => true,
  visibleChange: (val: boolean) => isBoolean(val),
  expandChange: (val: CascaderValue) => !!val,
  removeTag: (val: CascaderNode["valueByOption"]) => !!val,
};

const emit = defineEmits(cascaderEmits);
```

template 中使用组件时，注册事件使用 kebab-case 形式。

```html
<el-pagination
  @size-change="handleSizeChange"
  @current-change="handleCurrentChange"
/>
```

### template 中组件属性设置单独占用一行

在使用属性时，每个属性独占一行可提升代码的可读性。

```

// BAD
<my-component foo="a" bar="b" baz="c"/>

// GOOD
<my-component
  foo="a"
  bar="b"
  baz="c"
/>
```

### 组件模板应仅包含简单表达式，复杂的应提取到 computed 中

在 template 中避免使用复杂的表达式，包含计算逻辑的值应提取到 computed。

```javascript

// BAD
 <div :style="{
      height: '30rem',
      width: '100%',
      transition: '.3s ease-out all',
      transform: `rotateX(${parallax.roll}deg) rotateY(${parallax.tilt}deg)`,
    }">
 </div>

// GOOD
<div :style="cardStyle">

const cardStyle = computed(() => ({
  height: '30rem',
  width: '100%',
  transition: '.3s ease-out all',
  transform: `rotateX(${parallax.roll}deg) rotateY(${parallax.tilt}deg)`,
}))
```

### 复杂的 computed 应提取为多个简单计算

包含多个值计算的 computed 应当通过拆分，简化逻辑。

```javascript
// BAD
const price = computed(() => {
  const basePrice = manufactureCost.value / (1 - profitMargin.value);
  return basePrice - basePrice * (discountPercent.value || 0);
});

// GOOD
const basePrice = computed(
  () => manufactureCost.value / (1 - profitMargin.value)
);

const discount = computed(() => basePrice.value * (discountPercent.value || 0));

const finalPrice = computed(() => basePrice.value - discount.value);
```

### 属性赋值规则

template 中属性值使用引号(equoted)包裹。如果值为匿名对象则需要增加空格，保证值可读性。

```

// BAD
<input type=text>
<AppSidebar :style={width:sidebarWidth+'px'}>

// GOOD
<input type="text">
<AppSidebar :style="{ width: sidebarWidth + 'px' }">
```

### directive 指令统一使用简写，不能简写、全称混用

directive 统一使用简写：: for v-bind:, @ for v-on: and # for v-slot.

```javascript
// BAD
// v-bind全称和":"混用
<input
  v-bind:value="newTodoText"
  :placeholder="newTodoInstructions"
>
// v-on和@混用
<input v-on:input="onInput" @focus="onFocus" >

// v-slot和#混用
<template v-slot:header> <h1>Here might be a page title</h1> </template> <template #footer> <p>Here's some contact info</p> </template>

// GOOD
<input :value="newTodoText" :placeholder="newTodoInstructions" >

<input @input="onInput" @focus="onFocus" >

<template #header>
  <h1>Here might be a page title</h1>
</template>
<template #footer>
  <p>Here's some contact info</p>
</template>
```

### 组件属性定义添加校验

在定义组件属性时，如果属性值是必填、类型明确，则可添加 type、required、validator 限定值的有效性。

```javascript
// BAD
const props = defineProps({ status: String });

// GOOD
const props = defineProps({
  status: {
    type: String,
    required: true,
    validator: (value) => {
      return ["created", "loading", "loaded"].includes(value);
    },
  },
});
```

## Javascript 规范

### boolean 类型变量命名

boolean 变量、属性命名可添加 is、has 前缀，可以使用

is + Noun、
is + Adjective
is + Adjective + Noun
is + Noun + Adjective
has + X
按统一前缀规范，代码中只要看到 is、has 前缀的，则可初步判断为 boolean 类型。

```javascript
isString; // is + Noun
isStatic; // is + Noun
isSlot; // is + Noun
isMemberExpressionBrowser; // is + Noun, 为表达清楚变量意义，可多个名词链接
isSimpleIdentifier; // is + Adjective + Noun
isCompatEnabled; // is + Adjective
isVBind;
isVOn;
isFromSetup;
isUsedInTemplate;

hasFallback; // has + Noun
hasVnodeHook; // has + Noun
hasStyleBinding; // has + Noun
hasDynamicKeys; // has + Noun
hasText; // has + Noun
hashPrefix; // has + Noun
hasCommas; // has + Noun
hasName; // has + Noun
hasAttrsChanged; // has + Noun + Adjective
hasCloned; // has + Adjective
```

### boolean 类型参数命名

函数、方法、构造函数的参数，如果为 boolean 类型，命名：

is + Noun
is + Adjective
adjective
adv
verb + Noun
allow + Verb
不管使用那种方式，前提是直观上能推断是 boolean 类型。

```javascript

isLocal // is + Noun
isReference is + Noun
inline // adv
inheritAttrs // verb + Noun.
optimized // adjective
checked // adjective
allowRecurse // allow + Verb
force?: boolean // verb
sync?: boolean // noun

function genModulePreamble(
  genScopeId: boolean,
  inline?: boolean,
) {}

export function findProp(
  dynamicOnly: boolean = false,
  allowEmpty: boolean = false,
) {}

export interface WatchOptions<Immediate = boolean> extends WatchOptionsBase {
  immediate?: Immediate // adjective
  deep?: boolean // adjective
  once?: boolean // adv
}
```

### boolean 类型方法命名

boolean 类型方法命名，一般以 is、has、should、include、exclude、check 等为前缀, 以 Exists、Enabled、Equal 等为后缀。

```javascript
// is前缀
isInDestructureAssignment(...)
isInNewExpression(...)
isFragmentTemplate(...)
isTagStartChar(...)
isSameKey（...)
// has前缀
hasScopeRef(...)
hasForwardedSlots(...)
hasPropsChanged(...) // 表示状态变化的
hasMultipleChildren(...)
hasExplicitCallback（...)
hasCSSTransform(...)
shouldSkipAttr(...)
shouldReloadHmr(...)
includeBooleanAttr(...)
checkCompatEnabled（...)
// Exists、Equal后缀
fileExists(...)
looseEqual(...)
```

### Function:业务方法命名

使用 lowerCamelCase 小驼峰，以 verb 作为前缀，少数情况以描述性 noun 作为前缀。常用动词前缀：

- create、init、gen、walk、to、process、extract、unwrap、patch。

```javascript
reateObjectMatcher(obj: Record<string, any>) {}
createElementWithCodegen(...)
genFlagText(...)
parseWithForTransform(...)
createConditionalExpression(...)
convertToBlock(...)
walkFunctionParams(...) // work + Noun
walkBlockDeclarations(...) // 遍历Block定义
extractIdentifiers(...)
unwrapTSNode(...)
patchEvent(...)
patchDOMProp(...)
patchStyle(...)
// 描述性noun作为前缀
ssrRender(_ctx, _push, _parent, _attrs)
```

### Function:事件方法命名

注册事件的回调方法，经常纠结前缀要不要加"on"，后缀要不要加"ed"，错误示范：onChanged

如果是交互类事件的函数定义，通常采用 on + Verb + Noun?形式，如 onClick、onConfirm、onClose、onChange 等等。

```javascript

<el-button type="primary" size="small" @click="onConfirm">
<el-button @click="onDelete">Delete Item</el-button>
// on + Verb + Noun
<el-button v-if="!isAdding" @click="onAddOption">
```

如果是逻辑处理类事件，通常采用 handle+Verb，handle + Noun + Verb，例如：

```javascript
<el-icon class="el-input__icon" @click="handleIconClick">
    <edit />
</el-icon>

<el-cascader
  v-model="value"
  :options="options"
  :props="props"
  @change="handleChange"
/>

<el-autocomplete @select="handleSelect" >
```

如果是处理单一业务逻辑，则可直接调用 function，不需要专门定义事件方法，例如:

```

<Transition name="el-fade-in" @enter="lock" @after-leave="cleanup">

<el-button type="primary" plain @click="updateServiceWorker()">
```

如果是更改单一状态，则可直接在 template 赋值，例如：

```
<el-button plain @click="alwaysRefresh = true">

<el-button plain @click="needRefresh = false">

el-button @click="centerDialogVisible = false">Cancel</el-button>
<el-button type="primary" @click="centerDialogVisible = false">
```

### Funtion: 数据请求、处理类方法

数据查询通常采用 get 或 fetch 作为前缀，数据提交通常采用 post、send、upload 前缀。命名规则采用前缀 + Noun + Noun?.例如：

```

getBookData(id)
fetchBookData(id)

postBookData(data)
uploadBookFile(file)
```

### Class 命名

Class 采用 PascalCase 方式命名，命名可使用

- 多个 Noun
- Adjective + Noun
- Adjective + Adjective + Noun
- Noun + Verb + Noun。

```javascript
// Noun + Noun
export class TypeScope {}

// Adjective + Adjective + Noun
export class BaseReactiveHandler {}
// Adjective + Adjective + Noun
export class ReadonlyReactiveHandler {}

// Adjective + Noun + Noun
export class ComputedRefImpl {}

// Noun + Verb + Noun
export class ScriptCompileContext {}
```

### Class 私有方法、私有属性命名

TS 官方是极力反对 Class 私有方法或属性使用下划线"\_"前缀，由于有 private 标识私有，所以私有成员使用 lowerCamelCase 命名即可。

Vue.js 源码在早年前的 Class 偏向于使用\_lowerCamleCase 命名，现在也逐渐使用 loweCamelCase。

```javascript

// BAD
export class VueElement extends BaseClass {
  _instance: ComponentInternalInstance | null = null
  private _connected = false
  private _resolved = false
  private _numberProps: Record<string, true> | null = null
  private _styles?: HTMLStyleElement[]
  private _ob?: MutationObserver | null = null
 }

// GOOD
export default class Tokenizer {
  /** The current state the tokenizer is in. */
  private state = State.Text
  /** The read buffer. */
  private buffer = ''

  private stateInterpolation(c: number): void {
    ...
  }

  private stateInterpolationClose(c: number) {
    ...
  }
}
```

### 常量命名

常量统一放到一个文件定义，例如定义 constants.ts 文件，每个常量命名使用大写字母加下划线。组成可使用：

- 多个 NOUN
- ADJECTIVE_NOUN
- VERB_NOUN

```javascript
const PURE_ANNOTATION = `/*#__PURE__*/`;
export const KEEP_ALIVE = Symbol(__DEV__ ? `KeepAlive` : ``);
export const BASE_TRANSITION = Symbol(__DEV__ ? `BaseTransition` : ``);
export const OPEN_BLOCK = Symbol(__DEV__ ? `openBlock` : ``);

const DAYS_IN_WEEK = 7;
const MONTHS_IN_YEAR = 12;
const MAX_DOG_WEIGHT = 150;
```

### 枚举命名

枚举名称使用 PascalCase 命名规则，如果是集合类的枚举，通常以复数 s 形式结尾，表示复数类的后缀常有 Types、Levels、Tags、Codes、Hooks 等。如果是表示动作类状态枚举，则使用 Verb + Noun。

枚举值和常量定义规则一致，采用全大写模式, 多个单词用下划线"\_"分割。

```javascript

// Types
export enum Namespaces {
  HTML,
  SVG,
  MATH_ML,
}
// Codes
export enum ErrorCodes {
  ABRUPT_CLOSING_OF_EMPTY_COMMENT,
  CDATA_IN_HTML_CONTENT,
  DUPLICATE_ATTRIBUTE,
  END_TAG_WITH_ATTRIBUTES,
  END_TAG_WITH_TRAILING_SOLIDUS,
}
// Flags
export enum ReactiveFlags {
  SKIP = '__v_skip',
  IS_REACTIVE = '__v_isReactive',
  IS_READONLY = '__v_isReadonly',
  IS_SHALLOW = '__v_isShallow',
  RAW = '__v_raw',
}
// Hooks
export enum LifecycleHooks {
  BEFORE_CREATE = 'bc',
  CREATED = 'c',
  BEFORE_MOUNT = 'bm',
}

// Verb + Noun
export enum MoveType {
  ENTER,
  LEAVE,
  REORDER,
}
```

### 总结

#### 代码规范重要吗？

重要！优秀的代码规范，能够让别人在短时间能对你有正向的评价。特别在笔试时，优雅的代码也能让面试官眼前一亮，为后续流程降低门槛。

#### 如何保持代码规范？

- 客观上：可以借助代码检查 eslint、prettier、StyleLint、HTMLLint、SonarQube 等工具辅助检查。
- 主观上：要有工匠精神，个人认为正确的代码规范就得持续保持，"自成一派"，不能潜移默化地受其他风格影响。
