# Question 1 : Vue和React中的key

> 问题描述：写 React / Vue 项目时为什么要在列表组件中写 key，其作用是什么？

这个问题的本质是在问diff算法的原理。

## Vue

> [Vue官方文档对于key的解释。](https://cn.vuejs.org/v2/api/#key)

key主要被用于Vue的虚拟DOM算法，也就是diff操作中，用来在比较新旧nodes时辨识VNodes。以下是Vue源码中对于key的使用：

```js
function sameVnode (a, b) {
  return (
    a.key === b.key && ( // key
      (
        a.tag === b.tag && // 标签名
        a.isComment === b.isComment && // 是否为注释
        isDef(a.data) === isDef(b.data) && // 是否定义了data，data包含一些具体信息，例如onclick、style
        sameInputType(a, b) // 当标签是<input>的时候，type必须相同
      ) || (
        isTrue(a.isAsyncPlaceholder) &&
        a.asyncFactory === b.asyncFactory &&
        isUndef(b.asyncFactory.error)
      )
    )
  )
}
```

### 不使用key

* **就地复用**。没有绑定key的情况下，并且在遍历模板简单的情况下，会导致虚拟新旧节点对比更快，节点也会复用。而这种复用是就地复用，一种鸭子辩型的复用。这种复用可能在某种程度上（创建和删除节点）会带来渲染性能上的提升；
* **无法维持组件的状态**。就地复用会带来一些隐藏的副作用，比如可能不会产生过渡效果，或者在某些节点有绑定数据（表单）状态，会出现状态错位。VUE文档也说明了这个默认的模式是高效的，但是只适用于不依赖子组件状态或临时DOM状态(例如：表单输入值)的列表渲染输出

### 使用key
* **维持组件的状态，保证组件的复用**。
* **性能提升**。性能提升分为两种：
  * **查找性能的提升**。有key的时候，会生成hash，这样在查找的时候基本就是hash查找了，时间复杂度接近O(1)。
  * **节点复用带来的性能提升**。因为key可以唯一标识组件，所以可以尽可能地对组件进行复用，那么删除和创建组件的操作就会减少。

## React
> [React官方文档对于key的解释。](https://zh-hans.reactjs.org/docs/lists-and-keys.html#keys)

React和Vue的diff算法原理不同，因此对于key的使用也略有不同。

key帮助React识别哪些项目已更改，已添加或已删除。应该为数组内部的元素赋予键，以使元素具有稳定的标识