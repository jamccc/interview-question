## Virtual DOM

操作DOM 是渲染引擎的工作，JS是JS引擎的，用JS操作DOM 是跨线程，比较浪费性能，所以采用JS对象模拟DOM，即虚拟DOM
- 优点：
  1. 实现跨端开发
  2. 实现SSR,同构渲染等。
  3. 实现组件的高度抽象化。

## diff算法

只对比同层的节点，不跨层对比。
从上至下，从左至右的遍历对象，也就是树的深度遍历，一旦节点有子元素，就去判断子元素是否有不同。

## 树的递归

1. 新的节点的tagname或者key和旧的不同，这种情况代表需要替换旧的节点，并且也不再需要遍历新旧的子元素了，因为整个节点都被删掉了。
2. 新的节点的tagname和Key和旧的相同，开始遍历子树。
3. 没有新节点，什么都不做，
   
## 判断属性的更改

1. 遍历旧的属性列表，查看每个属是否存在于新的属性列表中。
2. 遍历新的属性列表，判断两个列表中都存在的属性值是否变化
3. 在第二部的同时检查是否有属性不存在与旧的属性列表中

## 判断列表差异

1. 遍历旧的节点列表，查看每个节点是否存在于心的节点列表中
2. 遍历新的节点列表，判断是否有新的节点
3. 在第二步中同时判断是否有节点移动

## 遍历子元素打标识

1. 判断两个列表差异
2. 给节点打上标识
   
## 渲染差异

1. 深度遍历，将需要做的变更操作取出来
2. 局部更新DOM