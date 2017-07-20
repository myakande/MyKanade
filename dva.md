# dva知识地图整理
>由sorrycc/dva-knowledgemap、阮一峰/ECMAScript 6 入门整理修改  

* [JavaScript 语言](#javascript-语言 )
	* [变量声明](#变量声明 )
			* [const和let](#const和let )
			* [模板字符串](#模板字符串 )
			* [默认参数](#默认参数 )
	* [箭头函数](#箭头函数 )
	* [模块的 Import 和 Export](#模块的-import-和-export )
	* [ES6 对象和数组](#es6-对象和数组 )
			* [析构赋值](#析构赋值 )
			* [对象字面量改进](#对象字面量改进 )
			* [Spread Operator（...）](#spread-operator )
	* [Promises](#promises )
	* [Generators](#generators )
* [React Component](#react-component )
	* [Stateless Functional Components](#stateless-functional-components )
	* [JSX](#jsx )
			* [Spread Attributes](#spread-attributes )
	* [CSS Modules](#css-modules )
			* [一张图理解](#一张图理解 )
			* [定义全局 CSS](#定义全局-css )
			* [classnames Package](#classnames-package )
* [Model](#model )
	* [namespace](#namespace )
	* [state](#state )
	* [reducers](#reducers )
	* [effects](#effects )
	* [subscriptions](#subscriptions )
* [Router](#router )
	* [react-route](#react-route )
	* [Route Components](#route-components )
			* [通过 connect 绑定数据](#通过-connect-绑定数据 )
			* [基于 action 进行页面跳转](#基于-action-进行页面跳转 )
* [history](#history )

## JavaScript 语言

### 变量声明

##### const和let

const 和 let，分别表示常量和变量。  
块级作用域，无变量提升。  
##### 模板字符串

使用（\`）反引号标识，表示多行字符串时，所有的空格和缩进都会被保留，需要将变量名写在${}之中。
```javascript
const user = &#x27;world&#x27;;
console.log(`hello"/>{user}`);
```
##### 默认参数

```javascript
function logActivity(activity = 'skiing') {
  console.log(activity);
}

logActivity();  // skiing
```
### 箭头函数

```javascript
var f = v => v;
```
等同于
```javascript
var f = function(v) {
  return v;
};
```
函数体内的this对象，就是**定义时所在的对象**，而不是使用时所在的对象。
### 模块的 Import 和 Export

```javascript
// 引入全部
import dva from 'dva';

// 引入部分
import { connect } from 'dva';
import { Link, Route } from 'dva/router';

// 引入全部并作为 github 对象
import * as github from './services/github';

// 导出默认
export default App;
// 部分导出，需 import { App } from './file'; 引入
export class App extend Component {};
```
### ES6 对象和数组

##### 析构赋值

析构赋值让我们从 Object 或 Array 里取部分数据存为变量。
```javascript
// 对象
const user = { name: 'guanguan', age: 2 };
const { name, age } = user;
console.log(`${name} : ${age}`);  // guanguan : 2

// 数组
const arr = [1, 2];
const [foo, bar] = arr;
console.log(foo);  // 1
```

我们也可以析构传入的函数参数。
```javascript
const add = (state, { payload }) => {
  return state.concat(payload);
};
```
析构时还可以配 alias(别名)，让代码更具有语义。
```javascript
const add = (state, { payload: todo }) => {
  return state.concat(todo);
};
```
##### 对象字面量改进

定义对象方法时，可以省去 function 关键字。
```javascript
app.model({
  reducers: {
    add() {}  // 等同于 add: function() {}
  },
  effects: {
    *addRemote() {}  // 等同于 addRemote: function*() {}
  },
});
```
##### Spread Operator（...）

可用于组装数组。
```javascript
const todos = ['Learn dva'];
[...todos, 'Learn antd'];  // ['Learn dva', 'Learn antd']
```
也可用于获取数组的部分项。
```javascript
const arr = ['a', 'b', 'c'];
const [first, ...rest] = arr;
rest;  // ['b', 'c']

// With ignore
const [first, , ...rest] = arr;
rest;  // ['c']
```
**如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。**
```javascript
const [...butLast, last] = [1, 2, 3, 4, 5];
// 报错

const [first, ...middle, last] = [1, 2, 3, 4, 5];
// 报错
```
还可收集函数参数为数组。
```javascript
function directions(first, ...rest) {
  console.log(rest);
}
directions('a', 'b', 'c');  // ['b', 'c'];
```
代替 apply。
```javascript
function foo(x, y, z) {}
const args = [1,2,3];

// 下面两句效果相同
foo.apply(null, args);
foo(...args);
```
对于 Object 而言，用于组合成新的 Object 。(ES2017 stage-2 proposal)
```javascript
const foo = {
  a: 1,
  b: 2,
};
const bar = {
  b: 3,
  c: 2,
};
const d = 4;

const ret = { ...foo, ...bar, d };  // { a:1, b:3, c:2, d:4 }
```
### Promises

Promise 用于更优雅地处理异步请求。比如发起异步请求：
```javascript
fetch('/api/todos')
  .then(res => res.json())
  .then(data => ({ data }))
  .catch(err => ({ err }));
```
定义 Promise  
```javascript
const delay = (timeout) => {
  return new Promise(resolve => {
    setTimeout(resolve, timeout);
  });
};

delay(1000).then(_ => {
  console.log('executed');
});
```
### Generators

dva 的 effects 是通过 generator 组织的。Generator 返回的是迭代器，通过 yield 关键字实现暂停功能。
```javascript
app.model({
  namespace: 'todos',
  effects: {
    *addRemote({ payload: todo }, { put, call }) {
      yield call(addTodo, todo);
      yield put({ type: 'add', payload: todo });
    },
  },
});
```
## React Component

### Stateless Functional Components

React Component 推荐尽量使用Stateless Functional Components，保持简洁和无状态。这是函数，不是 Object，没有 this 作用域，是 pure function。
比如定义 App Component 。
```javascript
function App(props) {
  function handleClick() {
    props.dispatch({ type: 'app/create' });
  }
  return <div onClick={handleClick}>${props.name}</div>
}
```
等同于：
```javascript
class App extends React.Componnet {
  handleClick() {
    this.props.dispatch({ type: 'app/create' });
  }
  render() {
    return <div onClick={this.handleClick.bind(this)}>${this.props.name}</div>
  }
}
```
### JSX

##### Spread Attributes

比如：
```javascript
const attrs = {
  href: 'http://example.org',
  target: '_blank',
};
<a {...attrs}>Hello</a>
```
等同于
```javascript
const attrs = {
  href: 'http://example.org',
  target: '_blank',
};
<a href={attrs.href} target={attrs.target}>Hello</a>
```
更多有关JSX参考[react学习记录-JSX](https://github.com/mykanade/MyKanade/blob/master/react.md#jsx )
### CSS Modules

##### 一张图理解

![](https://camo.githubusercontent.com/d1341a45402a32a6112f7a99cd99341eab2abbad/68747470733a2f2f7a6f732e616c697061796f626a656374732e636f6d2f726d73706f7274616c2f535742775754625a4b7178774550712e706e67 )
##### 定义全局 CSS

CSS Modules 默认是局部作用域的，想要声明一个全局规则，可用 :global 语法。

比如：
```javascript
.title {
  color: red;
}
:global(.title) {
  color: green;
}
```
然后在引用的时候：
```javascript
<App className={styles.title} /> // red
<App className="title" />        // green
```
##### classnames Package

在一些复杂的场景中，一个元素可能对应多个 className，而每个 className 又基于一些条件来决定是否出现。这时，classnames 这个库就非常有用。
```javascript
import classnames from 'classnames';
const App = (props) => {
  const cls = classnames({
    btn: true,
    btnLarge: props.type === 'submit',
    btnSmall: props.type === 'edit',
  });
  return <div className={ cls } />;
}
```
这样，传入不同的 type 给 App 组件，就会返回不同的 className 组合：
```javascript
<App type="submit" /> // btn btnLarge
<App type="edit" />   // btn btnSmall
```
## Model

```javascript
app.model({
  namespace: 'todo',
	state: [],
  reducers: {
    add(state, { payload: todo }) {
      // 保存数据到 state
      return [...state, todo];
    },
  },
  effects: {
    *save({ payload: todo }, { put, call }) {
      // 调用 saveTodoToServer，成功后触发 `add` action 保存到 state
      yield call(saveTodoToServer, todo);
      yield put({ type: 'add', payload: todo });
    },
  },
  subscriptions: {
    setup({ history, dispatch }) {
      // 监听 history 变化，当进入 `/` 时触发 `load` action
      return history.listen(({ pathname }) => {
        if (pathname === '/') {
          dispatch({ type: 'load' });
        }
      });
    },
  },
});
```
model包含以下5个属性:
### namespace

model 的命名空间，同时也是他在全局 state 上的属性，只能用字符串，不支持通过 . 的方式创建多层命名空间。
### state

初始值，优先级低于传给 dva() 的 opts.initialState。
### reducers

接受 state 和 action，返回老的或新的 state 。即：`(state, action) => state`  
用于处理同步操作，唯一可以修改 state 的地方。由 action 触发。
### effects

`*(action, effects) => void`  
用于处理异步操作和业务逻辑，不直接修改 state。由 action 触发，可以触发 action，可以和服务器交互，可以获取全局 state 的数据等等。
**put用于触发action**
```javascript
yield put({ type: 'todos/add', payload: 'Learn Dva' });
```
**call用于调用异步逻辑**，支持 promise
```javascript
const result = yield call(fetch, '/todos');
```
**select用于从state里获取数据**
```javascript
const todos = yield select(state => state.todos);
```
### subscriptions

`({ dispatch, history }, done) => unlistenFunction`
subscriptions 是订阅，用于订阅一个数据源，然后根据需要 dispatch 相应的 action。数据源可以是当前的时间、服务器的 websocket 连接、keyboard 输入、geolocation 变化、history 路由变化等等。
## Router

### react-route

参考[react学习记录-Route](https://github.com/mykanade/MyKanade/blob/master/react.md#route )
### Route Components

Route Components 是指 `./src/routes/` 目录下的文件，他们是 `./src/router.js` 里匹配的 Component。
##### 通过 connect 绑定数据

比如：
```javascript
import { connect } from 'dva';
function App() {}

function mapStateToProps(state, ownProps) {
  return {
    users: state.users,
  };
}
export default connect(mapStateToProps)(App);
```
然后在 App 里就有了 `dispatch` 和 `users` 两个属性。
##### 基于 action 进行页面跳转

```javascript
import { routerRedux } from 'dva/router';

// Inside Effects
yield put(routerRedux.push('/logout'));

// Outside Effects
dispatch(routerRedux.push('/logout'));

// With query
routerRedux.push({
  pathname: '/logout',
  query: {
    page: 2,
  },
});
```
## history

切换 history 为 browserHistory
```javascript
import { browserHistory } from 'dva/router';
const app = dva({
  history: browserHistory,
});
```
去除 hashHistory 下的 \_k 查询参数
```javascript
import { useRouterHistory } from 'dva/router';
import { createHashHistory } from 'history';
const app = dva({
  history: useRouterHistory(createHashHistory)({ queryKey: false }),
});
```
更多可参考[react学习记录-history](https://github.com/mykanade/MyKanade/blob/master/react.md#route )
