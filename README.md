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

# ref属性
目的是让类中函数知道标签是什么。例如这里通过在标签中使用一个箭头函数，让类中有了一个input1对象，就可以在change函数中调用了。
此外，如果render里面标签有了修改，那么render就会重新从头执行一次。
```
   class MyComponent extends React.Component {
        change = () => {
            alert(this.input1.value)
        }
        render() {
            return(
                <div>
                    <input ref = {(c) => {this.input1 = c}}/>
                    <button onClick = {this.change}>aaa</button>
                </div>
            )
        }
    }
```

# 使用createRef定义ref
这里相当于在类中创建了一个容器，容器内容在标签中指定为该标签。注意多个标签不可以公用一个ref。
```
    class MyComponent extends React.Component {
        myRef = React.createRef()
        change = () => {
            alert(this.myRef.current.value)
        }
        render() {
            return(
                <div>
                    <input ref = {this.myRef}/>
                    <button onClick = {this.change}>aaa</button>
                </div>
            )
        }
    }
```

# 使用字符串直接创建ref：不推荐
```
    class MyComponent extends React.Component {
        change = () => {
            alert(this.refs.input1.value)
        }
        render() {
            return(
                <div>
                    <input ref = "input1"/>
                    <button onClick = {this.change}>aaa</button>
                </div>
            )
        }
    }
```

# 如果事件源和事件都指的是一个标签，那么可以省略ref而通过event.target 方法
```
    class MyComponent extends React.Component {
        change = (event) => {
            alert(event.target.value)
        }
        render() {
            return(
                <div>
                    <input onBlur = {this.change}/>
                </div>
            )
        }
    }
```

# 函数柯里化：即函数输出是一个函数
例如在下例中，通过让change函数输出一个函数的形式，能完成让state属性动态根据文本框内容改变
```
    class MyComponent extends React.Component {
        state = {username: "", password: ""}
        change = (dataType)=> {
            return () => {
                var val = event.target.value
                this.setState({[dataType]: val})
            }
        }
        render() {
            return(
                <form>
                    userName: <input onChange = {this.change("username")}/>
                    pass: <input onChange = {this.change("password")}/>
                </form>
            )
        }
    }
```

# 不用柯里化的写法：
```
    class MyComponent extends React.Component {
        state = {username: "", password: ""}
        change(datatype) {
            this.setState({[datatype]: event.target.value})
        }
        render() {
            return(
                <form>
                    userName: <input onChange = {()=>{this.change("username")}}/>
                    pass: <input onChange = {()=>{this.change("password")}}/>
                </form>
            )
        }
    }
```

# 各种内置方法调用时间
render：每次经过更新调用一次
componentDidMount：组件第一次挂载调用一次，更新不会调用
componentWillUnmount: 组件将要被卸载时调用
下面的例子能让文字透明度渐渐变化，按下按钮会删除所有组件
注意定时器不可以放在render下，因为这样会被调用很多次（每次改变透明度都会调用一次render）
另外也不能不写componentWillUnmount函数，因为这样在删除组件时还保留有定时器
```
    class MyComponent extends React.Component {
        state = {opacity: 1}
        change(datatype) {
            this.setState({[datatype]: event.target.value})
        }

        death = (() => {
            ReactDOM.unmountComponentAtNode(document.getElementById("test"))
        })

        componentDidMount() {
            this.timer = setInterval(() => {
                var val = this.state.opacity
                if (val <= 0) {
                    val = 1
                }
                else {
                    val = val - 0.1
                }
                this.setState({"opacity": val})
            }, 200)
        }

        componentWillUnmount() {
            clearInterval(this.timer)
        }

        render() {
            return(
                <div>
                    <h1 style = {{opacity: this.state.opacity}}>aaa</h1>
                    <button onClick = {this.death}> 关闭所有组件 </button>
                </div>
            )
        }
    }
```

# 与组件生命周期有关的
有几种调用顺序
1. construct -> componentWillMount -> render
2. construct -> componentWillMount -> render
3. setState方法 -> shouldComponentUpdate -> componentWillUpdate -> render
4. forceUpdate方法 -> componentWillUpdate -> render
5. 父组件render -> componentWillReceiveProps -> shouldComponentUpdate -> componentWillUpdate -> render
其中 shouldComponentUpdate是一个返回布尔类型的函数，如果自己不写默认true，如果为false，那么接下来的步骤都不会进行。

# 第五个调用顺序中父子组件例子
这里两个组件都是继承Component，但是在MyComponent组件中render方法里有SonComponent的内容。这里点击按钮后，SonComponent会得到一个名叫name的prop，便会触发componentWillReceiveProps函数（该函数也可以有参数props）。注意这里再未点击按钮时不会有输出，componentWillReceiveProps只有在收到新的props才会调用。
这里可以理解为点击按钮后，调用了父的render，因此调用了子组件。
```
    class MyComponent extends React.Component {
        state = {name: "A"}
        change = () => {
            if (this.state.name == "A") {
                this.setState({name: "B"})
            }
            else {
                this.setState({name: "A"})
            }
        }
        render() {
            return(
                <div>
                    <h1>This is father</h1>
                    <button onClick = {this.change}>change Name</button>
                    <SonComponent name = {this.state.name}/>
                </div>
            )
        }
    }

    class SonComponent extends React.Component {
        componentWillReceiveProps() {
            console.log("receive props")
        }
        render() {
            return (
                <h1>{this.props.name}</h1>
            )
        }
    }
```

# 第一个脚手架完成的react程序
index.html内容
```
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>React App</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

index.js 内容
```
import React from "react"
import ReactDom from "react-dom"
import App from "./App"

ReactDom.render(<App/>, document.getElementById("root"))
```

App.js 内容
```
import React from "react"
class App extends React.Component {
  render() {
    return (
      <div>
        hello react
      </div>
    )
  }
}

// 暴露App组件，让别人可以引入
export default App
```

# 上一个的问题
App是一个壳子，不应该出现过多文字
在App.js中改变为以下内容
```
import React from "react"
import Hello from "./Hello"
class App extends React.Component {
  render() {
    return (
      <Hello/>
    )
  }
}

// 暴露App组件，让别人可以引入
export default App
```
同时添加Hello.js
```
import React from "react"
class Hello extends React.Component {
  render() {
    return (
      <h1>
        hello react
      </h1>
    )
  }
}

export default Hello
```

# 项目结构
1. App.js，index.html，index.js 不用改名
2. 在src文件夹下建立components文件夹，每一个小组件及其css等放在components下的一个文件夹中，例如上面例子中Hello.js放在components文件夹下Hello文件夹下
3. 组件后缀名建议写jsx，区别于js

# 样式模块化
组件对应css文件取名例如：hello.module.css
引入时引入为 import hello from "./hello.module.css"

```
import React from "react"
import hello from './Hello.module.css'

class Hello extends React.Component {
  render() {
    return (
      <h1 className={hello.demo}>
        hello react
      </h1>
    )
  }
}

export default Hello
```
