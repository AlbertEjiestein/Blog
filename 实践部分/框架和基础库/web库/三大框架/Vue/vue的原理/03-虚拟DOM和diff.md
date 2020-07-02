# 虚拟DOM和diff（深度）

### vdom

js直接操作DOM计算量很大，而js的计算是基于V8引擎的，他的执行速度很快，如何把对DOM的操作转移为JS计算来提高性能？vdom-用JS模拟DOM结构，计算最小的变更，更新DOM。

HTML是XML规范的一种，任何XML结构都可以用JSON结构表示出来，所以对于HMTL的DOM结构，完全可以用JS模拟，模拟结构图会画。

vue react的vdom 是基于snabbdom的vdom理念做的改进，实现方式可能不一样，但基本理念是一样得。



### diff算法

diff是一个广泛的概念，如linux diff命令，git diff等。两个js对象也可以做diff，两棵树也可以做diff。

如果要对两棵树直接做diff，则时间复杂度为O(n^3)，因为要遍历tree1，遍历tree2，然后排序，这种算法肯定是不可用的。

diff算法则将上边的时间复杂度优化到O(n)，实现原理如下：

+ 只比较同一层级，而不跨级比较

+ 如果tag不相同，则直接删掉重新，不在深度比较

  > 所以建议注重标签语义化，而不是全都是div，这样对diff也有好处

+ 如果tag和key都相同，则认为是相同节点，不再深度比较



snabbdom源码解析 （**重要**）

+ updateChildren细节不重要，过程也不重要，知道其基本的思想原理就好

+ vdom核心概念很重要：h、vnode、patch、diff、key等
  + h函数传入啥，返回啥
  + vnode结构
  + patch的过程：sameVnode ? patchVnode->addVnodes和removeVnodes -> updateChildren : removeVnodes
  + diff的同一层级比较，性能，tag和key的比较
  + key的重要性，在updateChildren中体现，如果新旧节点开头结尾都不匹配，则新节点会去oldChildren中查找是否有和新节点同样key值的节点，没有的话则插入
+ vdom价值：数据驱动视图来控制DOM操作 

patchVnode函数说详细，那细节就到位了，其中最麻烦的是updateChildren函数

这篇文章说的很详细 https://juejin.im/post/5affd01551882542c83301da#heading-9



