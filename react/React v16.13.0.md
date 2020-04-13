# React v16.13.0
[原文地址](https://reactjs.org/blog/2020/02/26/react-v16.13.0.html	"原文地址")

### 新的注意事项

#### render期间关于更新的注意事项
[一年多以前]( https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html	"Update on Async Rendering")我们宣布将重命名不安全的生命周期。
`componentWillMount → UNSAFE_componentWillMount
`componentWillReceiveProps → UNSAFE_componentWillReceiveProps
`componentWillUpdate → UNSAFE_componentWillUpdate

React  16.9 不包含中断更改，在新的版本中旧的生命周期命名依旧可以使用。但是当使用旧命名时，会报出warning。
![avatar](https://i.imgur.com/sngxSML.png)
就像warging所建议的，对于这些不安全的生命周期通常有更好的处理方法。然而可能你没有时间去迁移和测试这些组件。这种情况下，我们推荐运[codemod](https://medium.com/@cpojer/effective-javascript-codemods-5a6686bb46fb "Effective JavaScript Codemods") 来自动重命名:

``` javascript
npx react-codemod rename-unsafe-lifecycles
cd your_project
```
注意这里是npx，不是npm。npx是一个默认随Node 6+一起提供的实用程序。
运行codemod将会重置旧名称。例如：`componentWillMount`替换为`UNSAFE_componentWillMount`
![avatar](https://i.imgur.com/Heyvcyi.gif)

新的命名如：`UNSAFE_componentWillMount`将兼容React 16.9 和 React 17.x。然而新的`UNSAFE_`前缀将帮助有问题模式的组件在代码检查和调试期间脱颖而出。（如果你愿意，你可以通过[Strict Mode](https://reactjs.org/docs/strict-mode.html "Strict Mode" )进一步的阻止他们在你的项目中运行）。