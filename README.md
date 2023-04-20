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
表达式：会产生一个值，例如a
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
