# latex公式解析

vue 数学公式解析组件安装

```shell
npm install --save vue-katex-auto-render
npm install --save kate
```

需要安装katex依赖来渲染，官方网址：https://katex.org/docs/api.html

vue 中引用

```javascript
import Katex from 'vue-katex-auto-render'
Vue.directive('katex', Katex);
```

如何使用组件，使用前先简单做下配置：

```javascript
data: {
        options: { // katex 配置
            delimiters: [
                { left: "$", right: "$", display: true } //表示在$号之间的公式才会被解析
            ]
        }
    }
```

由于我们前面自定义了vue指令，只是在需要被解析的页面标签加上v-katex标签即可

```html
<div v-katex>需要解析的内容</div>
```

安装mathquill ，mathquill提供了公式输入的API ，安装如下

```shell
npm i mathquill --save
```

mathquill 使用参考文档

文档网址：http://docs.mathquill.com/en/latest/