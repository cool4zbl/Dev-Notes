# Accounts 
## Codemod 代码迁移

### 为什么：
- Accounts 的代码过于老旧 React v0.14 (No ReactDOM）& ES5；
- 一些遗留代码需要整理。
- 注册这块大量的代码，超过十个组件，使用 Codemod 进行批量迁移，省时省力。

- 使用到的工具：
	- [Facebook jscoodeshift](https://github.com/facebook/jscodeshift)
	- [FB react-codemod](https://github.com/reactjs/react-codemod)
	- [js-codemod](https://github.com/cpojer/js-codemod)


- PR: https://github.intra.douban.com/accounts/accounts/pull/553
- 主要思路：
	- 一个一个 component 来
	- 先用 react-codemod 转
	- js-transform 继续整理
	- standard-format 格式化代码
	- 更改 /page 里引用的方式 require -> `import xxx from bbb`
	- 重新 build 运行


- 收获和技巧
- 拓展？
- AST
- Parser

### TODO：
- 之前的页面不是特别多，只有「注册」「忘记密码」，后来出现了「第三方登录完善资料」、「绑定/解绑手机号」、「绑定/解绑微信号」，每个功能都基本有两三个 page，现在所有页面都是 webpack 打包成了一个文件，之前没有用 ReactDOM 的时候大概 140Kb，使用了 ReactDOM 以后达到了 340Kb 左右，是可以接受的样子，不过还是有优化空间；
- 比如可以针对每个功能，进行页面的拆分打包，独立出每个功能需要用的 JS，这样首屏加载更快。动态加载 JS。
- 合理利用 localStorage 进行静态文件缓存，这里可能就涉及到版本号更新的问题。
- 在开发的时候利用 react-hotloader + Webpack，做到本地实时热更新；
- 增加 Mock Server，无需真正请求 API，前端快速开发。测试时候也可以使用 Mock 数据。
- 使用 Redux / Mobx 对组件和页面的状态进行管理，更整洁更易维护的代码。
- 函数式编程思想「FP」：一些组件之间耦合度过高，需要继续拆解，分成更细的粒度，副作用更小，也更易测试。

### 一些坑：
- 「兼容性问题」：帐号平台应用广泛，从一开始的 大豆瓣App，豆瓣阅读App，后来有了 M站，还有 FM… 基本都是在复用。如何在不同平台不同使用场景上，达到使用一致。








