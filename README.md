# 第一个react程序：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div id = "test"></div>
</body>
<script src="../js/react.development.js" type="text/javascript"></script>
<script src="../js/react-dom.development.js" type="text/javascript"></script>

<script src="../js/babel.min.js"></script>

<script type="text/babel">
    const vdom = <h1>aaa</h1>
    ReactDOM.render(vdom, document.getElementById("test"))
</script>
</html>
```
在body部分定义了一个节点（容器），但是没有赋值。在script部分使用 render 方法进行赋值。

# 两种定义虚拟dom方式：
```
<script type="text/babel">
    const vdom = <h1 id = "title">aaa</h1>
    const vdom2 = React.createElement('h1', {id: 'title'}, 'bbb')
    ReactDOM.render(vdom, document.getElementById("test"))
    ReactDOM.render(vdom2, document.getElementById("test2"))
</script>
```
第一种方法是jsx创建的dom，第二种是js创建的dom。第二种方法不是很适合写嵌套的html

# 虚拟dom：
1. 本质就是一般类型的Object对象
2. 虚拟dom比较“轻”，即属性内容少。因为虚拟dom仅仅是react内部再用。
3. 虚拟dom最终会被react转换为真实dom

# JSX 语法 
1. 定义虚拟节点时动态定义，利用花括号括起来js表达式（不能是语句）
表达式：会产生一个值，例如a, myFun(1), arr.map()
语句：if、for等
```
<script type="text/babel">
    const content = "aaa"
    const title = "bbb"
    const vdom = <h1 id = {content}>{title}</h1>
    ReactDOM.render(vdom, document.getElementById("test"))
</script>
```
2. 样式类名指定不用class，用className
3. 标签首字母大写，表示组件，小写表示html标签

# 动态显示内容的例子
```
<script type="text/babel">
    const data = ["aaa", "bbb", "ccc"]
    const vdom = (
        <div>
            <ul>
                {
                    data.map(
                        (item, index)=> {
                            return <li key = {index}>{item}</li>
                        }
                    )
                }
            </ul>
        </div>
    )
    ReactDOM.render(vdom, document.getElementById("test"))
</script>
```
这里map函数目的是将数列中每一个元素都有输出值，并且要保证每一个节点都有key

# 函数组件：
```
    function Demo() {
        return <h1>aaa</h1>
    }
    ReactDOM.render(<Demo/>, document.getElementById("test"))
```
要求：
1. 函数名必须大写，不然会被认为是html标签
2. 使用render时必须加入/符号，表示节点开关

# 类式组件：
类定义时必须继承react的component类，并且必须类名大写以及重写render函数
```
<script type="text/babel">
    class MyComponent extends React.Component {
        render() {
            return <h1>bbb</h1>
        }
    }
    ReactDOM.render(<MyComponent/>, document.getElementById("test"))
</script>
```

# 动态定义：
在构造器中添加state构造，能得到动态效果
```
    class MyComponent extends React.Component {
        constructor() {
            super();
            this.state = {isHot: true}
        }
        render() {
            return <h1>bbb{this.state.isHot ? "Hot" : "Cold"}</h1>
        }
    }
    ReactDOM.render(<MyComponent/>, document.getElementById("test"))
```

# 添加点击事件的一种错误写法：
这里点击事件是传入了一个函数，函数是通过this找到的，但是调用时会没有指代对象去调用
```
    class MyComponent extends React.Component {
        constructor() {
            super();
            this.state = {isHot: false}
        }
        change() {
            this.state.isHot = !this.state.isHot
        }
        render() {
            return <h1 onClick = {this.change}>aaa{this.state.isHot ? "hot" : "cold"}</h1>
        }
    }
    ReactDOM.render(<MyComponent/>, document.getElementById("test"))
```

# 添加点击事件的第二种错误写法：
这里采用了bind方法，相当于将对象调用的函数改成了static的函数。这样再调用时就不会产生this的问题。但是这里change函数内改变参数的方式不正确。
```
    class MyComponent extends React.Component {
        constructor() {
            super();
            this.state = {isHot: false}
            this.change = this.change.bind(this)
        }
        change() {
            this.state.isHot = !this.state.isHot
        }
        render() {
            return <h1 onClick = {this.change}>aaa{this.state.isHot ? "hot" : "cold"}</h1>
        }
    }
    ReactDOM.render(<MyComponent/>, document.getElementById("test"))
```

# 正确添加点击事件
改变参数使用setState方法，并且这个方法只会改变传入的参数，而不是将别的参数也改掉。例如在这个例子中，如果有一个isWet属性，在change函数调用时不会影响该属性。
在这里render会使用1+n次，n是点击的次数
```
    class MyComponent extends React.Component {
        constructor() {
            super();
            this.state = {isHot: false}
            this.change = this.change.bind(this)
        }
        change() {
            this.setState({isHot: !this.state.isHot})
        }
        render() {
            return <h1 onClick = {this.change}>aaa{this.state.isHot ? "hot" : "cold"}</h1>
        }
    }
    ReactDOM.render(<MyComponent/>, document.getElementById("test"))
```

# 添加事件的简单方法
这里不在构造器中写state，表明state是类中的静态变量。此外，change函数用=>定义表示静态方法，其中this可以认为是引用类MyComponent
```
    class MyComponent extends React.Component {
        state = {isHot : true}
        change = ()=>{
            this.setState({isHot: !this.state.isHot})
        }
        render() {
            return <h1 onClick = {this.change}>aaa{this.state.isHot ? "hot" : "cold"}</h1>
        }
    }
    ReactDOM.render(<MyComponent/>, document.getElementById("test"))
```

# props 使用
可以动态生成页面。例如在下面的例子中，render函数动态调用props中属性并且输出。此外，通过propTypes与defaultProps两个变量可以控制输出的类型以及默认值。在ReactDom的render方法中，可以直接写标签，也可以提前写好标签，用{...p}形式传入。另外，props是只读变量，不可以修改其中值。
```
    class MyComponent extends React.Component {
        static propTypes = {
            name: PropTypes.string.isRequired,
            age: PropTypes.number,
            speak: PropTypes.func
        }
        static defaultProps = {
            name: "aaa",
            age: 10
        }



        render() {
            return(
                <ul>
                    <li>姓名：{this.props.name}</li>
                    <li>年龄：{this.props.age + 1}</li>
                    <li>说花：{this.props.speak()}</li>
                </ul>
            )
        }
    }

    function demo() {
        return "xyx"
    }
    const p = {name: "ls", speak: demo}
    ReactDOM.render(<MyComponent name = "zs" age = {20} speak = {demo}/>, document.getElementById("test"))
    ReactDOM.render(<MyComponent {...p}/>, document.getElementById("test2"))
```
