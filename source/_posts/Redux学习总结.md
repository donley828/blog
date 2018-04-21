---
title: Redux学习总结
date: 2018-03-20 10:33:09
tags: [JavaScript,前端,React,Redux]
categories: 前端
---
Redux 的学习心得，待修改
<!-- more -->
## 1.Action
Action 是一个 JS 对象，是对行为类型的一个描述或者是添加数据时的具体数据，或者删除数据是的数据项 id，比如：

```javascript
{
	"type": "ADD_TODO",
	"text": "Back School"
}
```
一般需要一个叫做 actionCreator 的函数创建 action。比如： 

```javascript
function addItem(text) {
	return {
		type: "ADD_TODO",
		text: text
	}
}
```
## 2.Reducer
Reducer 就是根据 Action 的描述进行具体的数据操作，一般获得两个参数`State`和`Action`,一般根据 Type 的类型更新 State，一般不直接修改，而是返回新的 `State`。比如上面那条Action:

```javascript
···
case ADD_TODO:
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
···
```
### combineReducers
这个函数用来管理各个分离的小 Reducer
## 3.Store
Stroe 就是整个应用存放数据的地方，一个应用只有一个store。
> * store 可以维持应用的 state
> * 提供 **getState()** 方法获取 state
> * 通过 **dispatch(action)** 更新 state
> * 通过 **subscribe(listener)** 注册监听器
> * **replaceReducer**用来替换 store 当前用来处理 state 的 reducer

## 4.Redux流程图
![redux-process](https://camo.githubusercontent.com/76224d874f32535aa62c0cd01750fb71fb02cf53/687474703a2f2f70362e7168696d672e636f6d2f642f696e6e2f39613331326463632f7265647578466c6f772e706e67)
## 5.react-redux
### Provider
Provider 是一个 React 组件，作用是保存store给子组件中的connect使用

```jsx
<Provider store={this.props.store}>
	<h1>hello world!</h1>
</Provider>
```

### Connect
Connect 把State和dispatch转换成props传递给子组件。

```jsx


```

### mapStateToProps
### mapDispatchToProps
### bindActionCreators
