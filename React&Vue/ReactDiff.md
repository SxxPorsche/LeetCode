虚拟DOM：
MVVM框架解决视图和状态同步问题
    虚拟DOM可以维护程序的状态,跟踪上一次的状态
    通过比较前后两次状态差异更新真实DOM
跨平台使用
    浏览器平台渲染DOM
    服务端渲染SSR(Nuxt.js/Next.js),前端是vue向,后者是react向
    原生应用(Weex/React Native)
    小程序(mpvue/uni-app)等

真实DOM的属性很多，创建DOM节点开销很大
虚拟DOM只是普通JavaScript对象，描述属性并不需要很多，创建开销很小

复杂视图情况下提升渲染性能,因为虚拟DOM+Diff算法可以精准找到DOM树变更的地方,减少DOM的操作(重排重绘)

React diff算法：
针对树结构(tree diff)：对UI层的DOM节点跨层级的操作进行忽略。（数量少）
针对组件结构(component diff)：拥有相同类的两个组件生成相似的树形结构，拥有不同类的两个组件会生成不同的属性结构。
针对元素结构(element-diff): 对于同一层级的一组节点，使用具有唯一性的id区分 (key属性)


Tree Diff:
React 通过使用 updateDepth 对 虚拟DOM树进行层次遍历
两棵树只对同一层级节点进行比较，只要该节点不存在了，那么该节点与其所有子节点会被完全删除,不在进行进一步比较。
只需要遍历一次，便完成对整个DOM树的比较。
React diff 只考虑同层次的节点位置变换,若为跨层级的位置变化，则是创建节点和删除节点的操作。即在新位置上重新创建相同的节点，而删除原位置的节点。
Tips:  React 官方建议不要进行DOM节点的跨层级操作，可是通过CSS来隐藏，显示节点，而不是真正地删除和添加DOM节点，保持稳定的DOM结构会对性能提升有帮助。

component diff的特点
同一类型的组件，按照原策略(tree diff) 比较 virtual DOM tree
同类型组件，组件A转化为了组件B，如果virtual DOM 无变化，可以通过shouldComponentUpdate()方法来判断是否
不同类型的组件，那么diff算法会把要改变的组件判断为dirty component,从而替换整个组件的所有节点。
就算结构再相似的组件，只要 React 判断是不同的组件，就不会判断是否为不同类型的组件，就不会比较其结构，而是删除组件以及其子组件，并创建新的组件以及其子节点。

element diff特点
对于处于同一层级的节点，React diff 提供了三种节点操作: 插入，移动，删除
插入：新的组件不在原来的集合中，而是全新的节点，则对集合进行插入操作。
删除：组件已经在集合中，但集合已经更新，此时节点就需要删除。
移动：组件已经存在于集合中，并且集合更新时，组件并没有发生更新，只是位置发生改变，React diff 则通过向同一层的节点添加 唯一key 进行区分，并且移动。


总结：
React 通过制定大胆的 diff 策略，将 O(n3) 复杂度的问题转换成 O(n) 复杂度的问题；

React 通过分层求异的策略，对 tree diff 进行算法优化；

React 通过相同类生成相似树形结构，不同类生成不同树形结构的策略，对 component diff 进行算法优化；

React 通过设置唯一 key的策略，对 element diff 进行算法优化；

建议，在开发组件时，保持稳定的 DOM 结构会有助于性能的提升；

建议，在开发过程中，尽量减少类似将最后一个节点移动到列表首部的操作，当节点数量过大或更新操作过于频繁时，在一定程度上会影响 React 的渲染性能。