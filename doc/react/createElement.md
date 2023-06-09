# createElement

`createElement`方法位于react包ReactElement文件中

#### 何时调用

在我们编写jsx的时候，总是要在开头引入react（后面版本可以不用引入）

```js
import React from 'react'
```

原因是在我们编写jsx的时候，babel会将我们编写的如下代码进行转换

```html
<div>hellow React</div>
```

具体转换可以在babel playground中进行尝试

```js
React.createElement("div", null, "hellow React");
```

在最新版本中,createElement背替换成了jsx，代码如下

```js
import { jsx as _jsx } from "react/jsx-runtime";

_jsx("div", {
  children: "hellow React"
});
```

本仓库主要阅读的是17的代码，最新细节不做阐述

#### 接受参数

`createElement`接受三个参数，分别是`type`、`config`、`children`

`type`: 节点类型，如果是原生的dom节点，则类型是一个String，如果是一个Component节点，则是一个变量

`config`: 我们在节点中写的属性

`children`: 孩子节点

#### 方法体

1、对config(我们在节点中写的属性)进行一些判断，比如是否有ref、key，有则赋值；然后对config的属性进行遍历（会对属性做筛选，如`key、ref、__self, __source`则不进筛选），并存放在方法声明的props变量中。

2、对`children`进行处理，将多个的`children`存放在一个数值中，`props.children`指针指向`children`或者`childArray`

3、对props默认值的一些处理，在类组件中，我们可以通过`Comp.defaultProps`对props进行一些默认值的设定。在此会将默认值放入方法声明的props中

4、最终将一系列初始化完的值进行传入`ReactElement`中并返回

#### ReactElement

参数列表 `type, key, ref, self, source, owner, props`代码过于简单不过多描述

```js
const ReactElement = function(type, key, ref, self, source, owner, props) {
  const element = {
    // This tag allows us to uniquely identify this as a React Element
    // 译：这个标签使我们能够唯一地将其识别为一个React元素
    // 用于标识element是什么类型的，通过createElement创建的节点type都是REACT_ELEMENT_TYPE
    // 经常在渲染里进行判断
    $$typeof: REACT_ELEMENT_TYPE,

    // Built-in properties that belong on the element属于元素的内置属性
    // 记录节点的类型，如是ClassComponent、funcComponent、原生dom还是react提供的Component
    type: type,
    key: key, // null
    ref: ref, // null
    // createElement中处理过后的props
    props: props,

    // Record the component responsible for creating this element.// 记录负责创建该元素的组件
    _owner: owner,
  };
  return element;
};
```

需要注意的是$$typeof属性，是用于标识element是什么类型的，通过createElement创建的节点type都是REACT_ELEMENT_TYPE，在后续的渲染里经常会使用这个字段进行判断
