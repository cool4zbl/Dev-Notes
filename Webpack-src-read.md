## Webpack Source Code Analysis

#### With Q:
- 这个问题的 Root Cause 是什么？ 触发它的代码的流程是什么？
- 在读代码的过程中，哪些地方我猜对了，哪些没猜对？
- 有功夫的话，代码的哪个部分是值得细细品读把玩的？
- 下次再出现类似的问题，我该怎么更快地从源码中定位出问题？

- AST
- 编译
- Webpack hash 算法与 DAE Static_ng 上线 hash 「SHA-1」算法
- 可以写 webpack plugin 覆盖
- 有些时候读源码更让你感觉到自己是一个创造者而不是使用者
- 能够读懂代码和理解代码应用场景是两回事

### Refs:

- [Webpack-md5-hash] https://github.com/erm0l0v/webpack-md5-hash/blob/master/plugin/webpack_md5_hash.js
- [Webpack hash vs chunckhash] http://www.cnblogs.com/ihardcoder/p/5623411.html
- [Webpack 源码学习] https://github.com/youngwind/blog/issues/99
- [Webpack 异步加载实现] https://segmentfault.com/a/1190000006063554#articleHeader16

