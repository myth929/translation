# React v16.9.0 and Roadmap Upate
[原文地址](https://reactjs.org/blog/2019/08/08/react-v16.9.0.html	"原文地址")

## 新的注意事项

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

### Note
#### 点击链接学习更多关于[版本控制政策和稳定性承诺](https://reactjs.org/docs/faq-versioning.html#commitment-to-stability)

## 不建议使用 javascript: URLs
URLs 通过JavaScript开始是一种危险的受攻击点，因为这容易在像`<a href>`中意外的造成有污染的输出和安全漏洞:
``` javascript
const userProfile = {
  website: "javascript: alert('you got hacked')",
};
// This will now warn:
<a href={userProfile.website}>Profile</a>
```
在react 16.9中，这些模块将继续可以使用，但是会报warning。如果你在逻辑中使用javascript: URLs，请尝试使用React时间去替代。（最常用的方法，你可以绕开[dangerouslySetInnerHTML](https://reactjs.org/docs/dom-elements.html#dangerouslysetinnerhtml)的保护，但是它有非常高的阻碍性经常容易造成安全漏洞。）

在将来的主要版本中，React将会在遇到JavaScript：URL时抛出错误。

## 不建议使用"Factory"组件
在JavaScript类通过Babel编译变得流行前，React支持通过"Factory"组件返回一个`render`方法的对象：
``` javascript
function FactoryComponent() {
  return { render() { return <div />; } }
}
```
这个模块因为与函数组件相似（但他不是）导致容易使人混淆。（在上面的例子中如果是函数组件将只返回`<div />`）
这种模式几乎没有在wild（不会翻）中使用过，支持这些会导致React稍微变的更大和更慢。所以我们在16.9中不建议使用，如果遇到这个模块并会报warning。如果你依赖它，增加`FactoryComponent.prototype = React.Component.prototype`作为支持的替代方法。或者你将它转成类或者函数组件。
我们不认为很多代码库会受此影响。

## 新特性
### 测试异步`act()`
[React 16.8](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html)介绍一个新的测试单元名为`act()`帮助你写出更好地匹配浏览器行的测试。例如：单个`act()`内多个状态更新可以批量处理。这与React在处理实际浏览器事件时的工作方式相匹配，并帮助你的组件做好频繁批量更新的准备。
然而，在16.8`act()`只支持同步函数。有时，你可能在测试中看到如下warning，但是不能简单的修复：
An update to SomeComponent inside a test was not wrapped in act(...).
在React 16.9，`act()`也支持异步函数。你可以在await中调用：
``` javascript
await act(async () => {
  // ...
});
```
这会解决你之前不能使用`act()`所剩下的case，列如在一个异步函数中进行状态更新。现在，你应该修复你测试中所有剩下的`act()`warning。


















