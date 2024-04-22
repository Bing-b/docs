### 一、vue 文件命名规范

#### 常用的命名规范：

- camelCase （小驼峰式命名法–首字母小写）
- PascalCase （大驼峰式命名法–首字母大写）
- kebab-case（短横线连接式）
- Snake（下划线连接式）

##### 1.1 项目文件命名

规则可以快速记忆为 “ 静态文件下划线，编译文件短横线 ”

1.1.1 项目名
全部采用小写方式，以短横线分隔。例：==my-project-name==
1.1.2 目录名

```
my-project-name/
|- builds         // 部署配置
|- docs           // 项目的细化文档目录（可选）
|- nginx          // 部署在容器上前端项目 nginx 代理文件目录
|- config         // 全局配置
|- node_modules   // 下载的依赖包
|- public         // 静态页面目录
|- src            // 源码目录
  |- api          // http 请求目录
  |- assets       // 静态资源目录，这里的资源会被wabpack构建
    |- icon   // icon 存放目录
    |- img    // 图片存放目录
    |- js     // 公共 js 文件目录
    |- scss   // 公共样式 scss 存放目录
      |- frame.scss   // 入口文件
      |- global.scss  // 公共样式
      |- reset.scss   // 重置样式
  |- components     // 组件
  |- hooks          // hook
  |- directives     // 公用指令
  |- layout         // 框架布局
  |- theme          // 主题
  |- types          // 类型
  |- enums          // 枚举
  |- plugins        // 插件
  |- router         // 路由
    |- routes     // 详细的路由拆分目录（可选）
    |- index.js
  |- store  // 全局状态管理
  |- utils  // 工具存放目录
    |- request.js // 公共请求工具
  |- views          // 页面存放目录
  |- App.vue        // 根组件
  |- main.js        // 入口文件
|- tests          // 测试用例
|- .browserslistrc// 浏览器兼容配置文件
|- .editorconfig  // 编辑器配置文件
|- .eslintignore  // eslint 忽略规则
|- .eslintrc.js   // eslint 规则
|- .gitignore     // git 忽略规则
|- Dockerfile // Docker 部署文件
|- index.html // 项目入口
|- package-lock.json
|- package.json // 依赖
|- README.md // 项目 README
|- vite.config.js // vite 配置
```

1.1.3 图像文件名
全部采用小写方式， 优先选择单个单词命名，多个单词命名以下划线分隔。

```
apple.png
logo_police.gif
```

1.1.4 HTML 文件名
全部采用小写方式， 优先选择单个单词命名，多个单词命名以下划线分隔。

```
|- error_report.html
|- success_report.html
```

1.1.5 CSS/Less/Sass 文件名
全部采用小写方式， 优先选择单个单词命名，多个单词命名以短横线分隔。

```
|- normalize.less
|- base.less
```

1.1.6 Js/Ts 文件名

```
index.js
config.ts
```

##### 1.2 Vue 组件命名
