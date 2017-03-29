
关于 vim 文本编辑。
就把编辑的文字想象成一个二维矩阵空间，横向一行是 X 轴，纵向是 Y 轴，所以只需要解决，如何在横着移动，和竖着移动就好。这两个方向组织起了 Vim 一些行列操作的大思想。

===



### Accounts TODOs： 

- `http://alexkuz.github.io/webpack-chart/` 优化性能 charts
- `NODE_ENV=production webpack --display-modules --sort-modules-by size —config ./makewebpackconfig.js`， 然后发现最需要优化的前三是 `get_captcha_code.jsx`(14kB) `page/register/index.jsx`(12.2kB) `page/bind/bind_phone.jsx` (10.7kB)
- 合理利用 localStorage 进行静态文件缓存，这里可能就涉及到版本号更新的问题。 https://www.zhihu.com/question/28467444 可以试下 CSS & JS 缓存，请求静态资源前先判断服务器端有没有更新，没有的话就拉 LS 存储，做到媲美原生的效果。（但是不知道客户端 webView 对待缓存会怎样）
- 在开发的时候利用 react-hotloader + Webpack，做到本地实时热更新；
- 增加 Mock Server，无需真正请求 API，前端快速开发。测试时候也可以使用 Mock 数据。
- 使用 Redux / Mobx 对组件和页面的状态进行管理，更整洁更易维护的代码。
- 函数式编程思想「FP」：一些组件之间耦合度过高，需要继续拆解，分成更细的粒度，副作用更小，也更易测试。
- What I found to be the real value from writing unit tests.
- 写测试时候遇到的一些坑。（Fake DOM / Event 模拟，数据填充...
- [Dynamic Routing](https://github.com/ReactTraining/react-router/blob/master/docs/guides/DynamicRouting.md)
- [Webpack Code Splitting + React-Router Dynamic routes](https://medium.com/@ryanflorence/welcome-to-future-of-web-application-delivery-9750b7564d9f#.5d5fthc2r)
