# AceEditor 封装及自定义扩展语言实现

### 📢 前言

最近调研前端代码编辑器，选中了 Ace editor，其本身功能丰富，扩展性也极强，插件目前更新稳定。ace 本身支持上百种语言的语法，然鹅日常项目中总有些奇葩的需求，以致我们需要去了解更多的技术研究。
技术支持： Vue3 + Vite ，基于 ace-builds 进行二次扩展封装。

### 📄 Ace 简介

#### 1、什么是 Ace editor ?

Ace（Ajax.org Cloud9 Editor）是一个用 JavaScript 编写的可嵌入代码编辑器。它与 Sublime，Vim 和 TextMate 等本地编辑器的功能和性能相匹配。它可以轻松地嵌入任何网页和 JavaScript 应用程序中。

<p align="center">
  <img src="../../public/images/ACE.png" alt="ACE">
</p>

#### 2、特征

- 超过 110 种语言的语法高亮显示（可以导入 TextMate/Sublime Text.tmlanguage 文件）
- 超过 20 个主题（可以导入 TextMate/Sublime Text .tmtheme 文件）
- 自动缩进和缩进
- 可选的命令行
- 处理大型文档（四百万行似乎是极限！
- 完全可定制的键绑定，包括 vim 和 Emacs 模式
- 搜索并替换为正则表达式
- 突出显示匹配的括号
- 在软标签和真实标签之间切换
- 显示隐藏字符
- 使用鼠标拖放文本
- 换行
- 代码折叠
- 多个光标和选择
- 实时语法检查器（目前为 JavaScript/CoffeeScript/CSS/XQuery）
- 剪切、复制和粘贴功能

### ✒️ Ace 使用及封装

#### 1、安装

使用 pnpm 包管理器，引入 ace-builds 库

```javascript
	pnpm i ace-builds -D
	pnpm i vue-loader-v16 -D // 引入 ace 报错时需安装
```

#### 2、引入

引入基础模块，测试 ace 编辑器基础功能

```javascript
	<template>
	  <div class="aceEditor" ref="aceEditor"></div>
	</template>

	<script lang="ts" setup>
	import { onBeforeUnmount, onMounted, ref, watch } from 'vue';
	import * as ace from 'ace-builds';

	import "ace-builds/src-noconflict/mode-javascript"; // 语言模式
	import "ace-builds/src-noconflict/theme-monokai" // 主题
	import "ace-builds/src-noconflict/ext-language_tools"; // 语法提示
	import "ace-builds/src-noconflict/snippets/javascript"; // 语法段提示模块
	</script>
```

#### 3、配置

```javascript
const options = {
  theme: "ace/theme/monokai", // 设置语法高亮主题
  mode: "ace/mode/javascript", // 设置语法 mode
  tabSize: 1,
  maxLines: 25,
  minLines: 25,
  showPrintMargin: false,
  fontSize: 14,
  printMarginColumn: 20,
  useWorker: false,
  showLineNumbers: true, // 显示行号
  showGutter: true, // 显示行号区域
  highlightActiveLine: false,
  highlightSelectedWord: false, // 高亮选中文本
  readOnly: false, // 控制编辑器是否只读
  enableSnippets: true, // 启用代码段提示
  enableLiveAutocompletion: true, // 启用实时自动完成
  enableBasicAutocompletion: true, // 启用基本自动完成
};
```

#### 4、初始化

```javascript
const initEditor = () => {
  if (editor) editor.destroy();

  // 初始化
  editor = ace.edit(aceEditor.value, options);

  // 切换自动换行
  editor.getSession().setUseWrapMode(true);
};

onMounted(() => {
  initEditor();
});
```

测试 aceEditor 基础封装是否功能正常，方便后续扩展开发。

### 🔧 Ace 自定义语言扩展

Ace 编辑器本身不支持直接自定义语言，但支持通过扩展语言模式的方式实现对自定义语言的支持。在 Ace 中，语言模式是指将文本分解为标记，定义了每个标记的样式和语法高亮规则，以此来实现语法高亮、代码折叠、自动完成等功能。
想要自定义新增语言模型，首先需要了解如何定义一个 mode （语言模式），可参考官方文档[ Ace - The High Performance Code Editor for the Web (c9.io](https://ace.c9.io/)
1、在 node_modules\ace-builds\src-noconflict 下新增一个 mode-mylang.js 的文件，定义语言模式、语言高亮规则及代码提示。

```javascript
ace.define(
  "ace/mode/mylang",
  [
    "require",
    "exports",
    "module",
    "ace/lib/oop",
    "ace/mode/text",
    "ace/mode/custom_highlight_rules",
  ],
  (require, exports, module) => {
    const oop = require("ace/lib/oop");
    const TextMode = require("ace/mode/text").Mode;
    const { MyLangHighlightRules } = require("ace/mode/mylang_highlight_rules");
    const { Tokenizer } = require("ace/tokenizer");

    const Mode = function () {
      this.HighlightRules = MyLangHighlightRules;
      this.$tokenizer = new Tokenizer(new MyLangHighlightRules().getRules());
    };

    oop.inherits(Mode, TextMode);

    (function () {
      // 加载css 样式设置，以便控制自定义语言关键词高亮颜色
      const dom = require("ace/lib/dom");
      dom.importCssString(exports.cssText, exports.cssClass);
    }).call(Mode.prototype);

    (function () {
      // 添加代码提示
      this.completer = {
        getCompletions(editor, session, pos, prefix, callback) {
          const wordList = [
            "hello",
            "world",
            "AceEditor",
            "hello world this is AceEditor",
          ];
          callback(
            null,
            wordList.map((word) => ({
              caption: word,
              value: word,
              meta: "mylang", // 自定义语言标识
            }))
          );
        },
      };
    }).call(Mode.prototype);

    exports.Mode = Mode;
  }
);
```

2、接着定于语法高亮规则， 高亮规则里面定义一系列的规则（Rules）， 每个规则描述了如何处理输入文本中的单个字符序列。Ace Editor 中的规则是由 Tokenizer 对象处理的。Tokenizer 是 Ace Editor 内置的一种基于正则表达式的解析器，用于将输入文本转换为标记（Token）流。标记是 Ace Editor 中的基本元素，它们由不同类型的 token 组成，例如：keyword、comment、string 等等。

```javascript
ace.define(
  "ace/mode/mylang_highlight_rules",
  [
    "require",
    "exports",
    "module",
    "ace/lib/oop",
    "ace/mode/text_highlight_rules",
  ],
  (require, exports, module) => {
    const oop = require("ace/lib/oop");
    const { TextHighlightRules } = require("ace/mode/text_highlight_rules");

    const MyLangHighlightRules = function () {
      // 定义高亮规则
      const keywordList = "let|const|function|world"; // 高亮关键词
      this.$rules = {
        start: [
          {
            token: "keyword",
            regex: `\\b(?:${keywordList})\\b`,
          },
          {
            token: "string",
            regex: '".*?"',
          },
          {
            token: "constant",
            regex: /\b(true|false|null)\b/,
          },
          {
            token: "comment",
            regex: /\/\/.*$/,
          },
          {
            token: "comment",
            start: "/\\*",
            end: "\\*/",
          },
          {
            token: "mylang",
            regex: "\\b(?:hello|world|AceEditor)\\b",
          },
        ],
      };
    };

    oop.inherits(MyLangHighlightRules, TextHighlightRules);

    exports.MyLangHighlightRules = MyLangHighlightRules;
  }
);
```

3、自定义特殊高亮颜色，比如我们想对 true | false | null 进行特殊颜色高亮，只需更具规则中 “token” 属性值，以 .ace\_[属性值] 形式定义 css。
例：

```css
/* 自定义语言，匹配不同类型关键词高亮颜色 */
.ace_constant {
  color: #ff00ff;
  font-weight: bold;
}
```

4、最后引入我们自定义语言即可，可直接在 option 中配置即可

```javascript
	mode: 'ace/mode/mylang',
```

### 🏁 源码

插件本身还有很多配置和 api , 感兴趣的可以继续探索，本文旨在对初阶自定义功能实现讲解，后续会慢慢更新更深层次自定义方案。

```javascript
	<template>
	  <div class="aceEditor" ref="aceEditor"></div>
	</template>

	<script lang="ts" setup>
	import { onBeforeUnmount, onMounted, ref, watch } from 'vue';
	import * as ace from 'ace-builds';

	import "ace-builds/src-noconflict/mode-javascript"; // 语言模式
	import "ace-builds/src-noconflict/theme-monokai" // 主题
	import "ace-builds/src-noconflict/ext-language_tools"; // 语法提示
	import "ace-builds/src-noconflict/snippets/javascript"; // 语法段提示模块

	// 自定义语言
	import "ace-builds/src-noconflict/mode-mylang"; // 此模块需对应目录创建

	const props = withDefaults(defineProps<{
	  value: string,
	}>(), {
	})

	const emits = defineEmits(['update:value']);

	let editor: any = null;
	const aceEditor = ref<string | Element>('')

	// 编辑器默认配置项
	const options = {
	  theme: 'ace/theme/monokai',
	  //mode: 'ace/mode/javascript',
	  mode: 'ace/mode/mylang',
	  tabSize: 1,
	  maxLines: 25,
	  minLines: 25,
	  showPrintMargin: false,
	  fontSize: 14,
	  printMarginColumn: 20,
	  useWorker: false,
	  showLineNumbers: true, // 显示行号
	  showGutter: true, // 显示行号区域
	  highlightActiveLine: false,
	  highlightSelectedWord: false, // 高亮选中文本
	  readOnly: false, // 控制编辑器是否只读
	  enableSnippets: true, // 启用代码段
	  enableLiveAutocompletion: true, // 启用实时自动完成
	  enableBasicAutocompletion: true,  // 启用基本自动完成
	}

	// 初始化编辑器
	const initEditor = () => {
	  if (editor) editor.destroy();

	  // 初始化
	  editor = ace.edit(aceEditor.value, options);

	  // 切换自动换行
	  editor.getSession().setUseWrapMode(true);

	  // 支持双向绑定
	  editor.setValue(props.value ? props.value : "");
	  editor.on("change", () => {
	    emits("update:value", editor.getValue());
	  })
	}

	watch(
	  () => props.value,
	  (newProps) => {
	    //解决光标移动问题
	    const position = editor.getCursorPosition();
	    editor.getSession().setValue(newProps);
	    editor.clearSelection();
	    editor.moveCursorToPosition(position);
	  }
	);

	onMounted(() => {
	  initEditor()
	});

	onBeforeUnmount(() => {
	  editor.destroy();
	});

	</script>
	<style>
	.aceEditor {
	  width: 500px;
	  height: 500px;
	}

	/* 自定义语言，匹配不同类型关键词高亮颜色 */
	.ace_constant {
	  color: #FF00FF;
	  font-weight: bold;
	}
	</style>
```

### 📖 相关文档及网站

官网：<font color='blue'> https://ace.c9.io </font>
GitHub: <font color='blue'> https://github.com/ajaxorg/ace </font>
Vue3 ace 组件：<font color='blue'> https://github.com/CarterLi/vue3-ace-editor </font>
在线测试：<font color='blue'> https://ace.c9.io/build/kitchen-sink.html </font>
