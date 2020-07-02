### setState batchUpdate

+ 不可变值
+ 有时异步，有时同步
+ 有时合并，有时不合并



**setState主流程**

![setState](C:/Users/Alber/Pictures/setState.PNG)

**batchUpdate机制**

![isBatchingUpdate](C:/Users/Alber/Pictures/isBatchingUpdate.PNG)



setState异步还是同步？

+ setState无所谓异步还是同步
+ 看是否能命中batchUpdate机制
+ 判断isBatchingUpdates



**哪些能命中BatchUpdates机制**

+ 生命周期（和它调用的函数）
+ React中注册的事件（和它调用的函数）
+ React可以“管理”的入口（生命周期中自定义的事件就不归React管理的入口了）

**哪些不能命中BatchUpdates机制**

+ setTimeout setInterval （和它调用的函数）
+ 自定义的DOM事件（和它调用的函数）
+ React“管不到”的入口



**transaction（事务）机制**

先执行一个开始的逻辑，然后执行函数体，然后在执行结束的逻辑



![transaction事务机制](C:/Users/Alber/Pictures/transaction事务机制.PNG)