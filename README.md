## MyKanade的react学习笔记      
### JSX
{js表达式}
`<div tabIndex="xxx"></div>;` 引号定义字符串属性
`<img src={xxx}></img>;` 大括号定义js表达式属性 外面不能有引号
JSX可以嵌套，**最外层一个根节点，小驼峰命名法**
### 元素
元素不可变，更新界面的**唯一**办法是创建一个新的元素，然后将它传入 `ReactDOM.render()`，类似动画帧
### 组件 &emsp;&emsp;
接收任意的输入值（props，仅父级向子级），并返回一个需要展示的元素，**必须以大写字母开头，决不能修改它自己的props**。当元素是**用户自定义**的组件，它会将JSX属性作为单个对象传递给该组件
### State
类中使用，局部状态、生命周期钩子
### 事件处理
`<button onClick={activateLasers}>`属性不是字符串而是{函数}

    class Toggle extends React.Component {
      constructor(props) {
        super(props);
        this.state = {isToggleOn: true};

        // This binding is necessary to make `this` work in the callback
        this.handleClick = this.handleClick.bind(this);
      }

      handleClick() {
        this.setState(prevState => ({
         isToggleOn: !prevState.isToggleOn
        }));
      }

      render() {
        return (
          <button onClick={this.handleClick}>
            {this.state.isToggleOn ? 'ON' : 'OFF'}
          </button>
        );
      }
    }

    ReactDOM.render(
      <Toggle />,
      document.getElementById('root')
    );

### 列表

    function NumberList(props) {
      const numbers = props.numbers;
      const listItems = numbers.map((number) =>
        <li key={number.toString()}>
          {number}
        </li>
      );
      return (
        <ul>{listItems}</ul>
      );
    }

    const numbers = [1, 2, 3, 4, 5];
    ReactDOM.render(
      <NumberList numbers={numbers} />,
      document.getElementById('root')
    );

Keys可以在DOM中的某些元素被增加或删除的时候帮助React识别哪些元素发生了变化。

### 表单
>受控组件

    class NameForm extends React.Component {
      constructor(props) {
        super(props);
        this.state = {value: ''};

        this.handleChange = this.handleChange.bind(this);
        this.handleSubmit =   this.handleSubmit.bind(this);
      }

      handleChange(event) {
        this.setState({value: event.target.value});
      }

      handleSubmit(event) {
        alert('A name was submitted: ' + this.state.value);
        event.preventDefault();
      }

      render() {
        return (
          <form onSubmit={this.handleSubmit}>
            <label>
              Name:
              <input type="text" value={this.state.value} onChange={this.handleChange} />
            </label>
            <input type="submit" value="Submit" />
            </form>
        );
      }
    }

由于 value 属性是在我们的表单元素上设置的，因此显示的值将始终为 React数据源上this.state.value 的值。由于每次按键都会触发 handleChange 来更新当前React的state，所展示的值也会随着不同用户的输入而更新。

### 状态提升
在React应用中，对应任何可变数据理应只有一个单一“数据源”。通常，状态都是首先添加在需要渲染数据的组件中。此时，如果另一个组件也需要这些数据，你可以将数据提升至离它们最近的 **父组件**中。你应该在应用中保持**自上而下的数据流**，而不是尝试在不同组件中同步状态。

### 组合vs继承
React 具有强大的组合模型，建议使用组合而不是继承来复用组件之间的代码。
