### Accounts 相关

---

- 为什么一直好好的 `getCookie()` 不工作了？

  - 思考下可能保存的 cookie 的值或者存储类型有了变化..

  - http://github.intra.douban.com/accounts/accounts/pull/347

  - 涉及到拿 cookie 的某个值进行后端认证。

  - 之前后端返回时候 cookie 里某个值带双引号，现在不带，所以长度有变化。

    需要想到一些好办法来拿 cookie ...

  - 最好是在请求的时候，再动态取 cookie，否则有可能出现服务端没来得及种 cookie 的情况。




---

`RegExp.test()` 方法千万别用 `g` 标识！！！！

- `true` ` false` 交替返回；
- 因为下一次匹配会从上一次匹配到的位置重新开始。

---

`captcha_code_img` 前端 md5 验证



---

`window.postMessage()`

- Parent -> Child.  childwindow.postMessage('haaha', 'http://child.com'); window.addEventListener('message', handler, false)
- Child -> Parent parentWindow.postMessage('haha', 'http://parent.com')

-----

`React` 事件 `event.preventDefault()` 达到了类似冒泡的结果

ref:

- [React SyntheticEvent Doc](https://facebook.github.io/react/docs/events.html)
- [React nativeEvent Stop](http://stackoverflow.com/questions/24415631/reactjs-syntheticevent-stoppropagation-only-works-with-react-events)
- [React EventEmitter Source Code](https://github.com/facebook/react/blob/3b96650e39ddda5ba49245713ef16dbc52d25e9e/src/renderers/dom/client/ReactBrowserEventEmitter.js#L23)
- [腾讯 imweb React 事件初探](http://imweb.io/topic/5774e361af96c5e776f1f5cd)


---

`window.postMessage`  `window.top`

*note*:
  补充：框架之间的通信方式

Debug postMessage devTools keeping crash:

[Chrome Forum](https://productforums.google.com/forum/#!topic/chrome/RgxarClMbOM)

---

#### TDD Testing with Mocha, chai, sinon & Enzyme

- Mocking a userAgent in JS
  http://stackoverflow.com/questions/1307013/mocking-a-useragent-in-javascript

```javascript
navigator.__defineGetter__('userAgent', function () {
  return 'foobar' // customized user agent
})

navigator.userAgent  // 'foobar'
```

---
- >Consider marking event handler as 'passive' to make the page more responsive.

关于 passive event handler [Official Explanation](https://github.com/WICG/EventListenerOptions/blob/gh-pages/explainer.md)

---
ci-tools
tools/ci-scripts/run_eslint.sh
```bash
set -o pipefail
rm -rf testreports
mkdir testreports
touch testreports/eslint.xml
cd web/static/js/app
npm install
npm run lint-for-ci | tee ../../../../testreports/eslint.xml || RET=$?
exit $RET
```
```app.yaml
test_handlers:
- name: eslint
  type: eslint
  test_script: /tools/ci-scripts/run_eslint.sh
  enable_pr: True
```

---
- 如果当前文件夹下有 `package.json` ，里面有配置 `webpack` ，且 `npm install webpack` 过，
  那么在当前文件夹下 run `webpack` 会优先使用之前已经安装好的 `webpack` 版本，
  `npm i -g webpack` 安装好的 `webpack` 会先被屏蔽。

- 年前到 2017.02.09 做了下 动态路由和代码拆分，碰到了很多前端工程性的问题，发现自己特别爱研究这类问题，可能是个工具控吧。

  [require AMD vs require.ensure](http://blog.csdn.net/zhbhun/article/details/46826129)

- webpack code-splitting http://webpack.github.io/docs/code-splitting.html


- `webpack + react-router + single page`，单个模板页，多个路由页面复用。最终效果是：1。代码按功能模块拆开； 2. 打开 Index 页默认加载 index 页代码； 3. 其余页面代码按需加载，`require.ensuire([], () => {...})`（比如是 forget 再加载 forget 相关代码，缓存？）； 4. 每个页面下的子页面（组件）是一起加载而非按需加载。

- 代码分割其实有两种思路。一种是纯 webpack 的按页面分隔，个人觉得可以称为真分割，因为是实实在在把 JS 打包成了几个部分，然后在不同页面中加载不同的 JS，更适用于传统多页面打包；一种是使用 webpack + react-router 的动态路由加载，这种更适用于单页面打包（即 accounts 相关，只有一个 register_base.html 模板页），默认加载 index components, hash path 不一致的时候再加载对应的 components 相关JS，有点像假分割。两种都比较灵活，都可以自由控制 import 的组件。

- 如果文件中已经 import 了一次，require.ensure 会失效，webpack 仍然全部打包；ref: http://webpack.github.io/docs/code-splitting.html#es6-modules

- `import x from 'xxx' -> static` `require.ensure([], require => { cb(null, module)}) -> dynamic`

- Named chunks: https://github.com/webpack/webpack/tree/webpack-1/examples/named-chunks

- 使用 JSX 语法可以参考官方文档 https://github.com/ReactTraining/react-router/blob/1.0.x/docs/API.md

- 如果使用 plainRoutes 语法，结合 webpack 1.x 异步加载，需要使用

  ```javascript
  // React-router 1.0.x + webpack 1.x
  // 必须要 require.ensure 语法，否则 webpack 仍然是全部打包
  // 异步的话，webpack 会 code splitting 几个 chunk.js，在请求到对应的路由的时候自动加载.
  childRoutes: {
    {path: 'complete', getComponent(location, cb) {
      cb(null, require('../forget/complete').default)
    }}
  }
  ```

- 但是如果使用官方的 webpack `require.include` 语法，貌似就不对了。http://webpack.github.io/docs/code-splitting.html#require-include

- ES6 https://github.com/ModusCreateOrg/react-dynamic-route-loading-es6/blob/master/client/pages/routes.js

- Official Docs: https://github.com/ReactTraining/react-router/blob/1.0.x/docs/guides/basics/RouteConfiguration.md

- mxstbr.blog http://mxstbr.blog/2016/01/react-apps-with-pages/

- https://segmentfault.com/a/1190000006063554#articleHeader16

- **`tree -I node_modules\|js-codemod\|react-codemod` bash 里面使用正则 pattern 需要将 `|`转义**。


- 之前的页面不是特别多，只有「注册」「忘记密码」，后来出现了「第三方登录完善资料」、「绑定/解绑手机号」、「绑定/解绑微信号」，每个功能都基本有两三个 page，现在所有页面都是 webpack 打包成了一个文件，之前没有用 ReactDOM 的时候大概 140Kb，使用了 ReactDOM 以后达到了 340Kb 左右，是可以接受的样子，不过还是有优化空间；
- 比如可以针对每个功能，进行页面的拆分打包，独立出每个功能需要用的 JS，这样首屏加载更快。动态加载 JS。
- From PR: https://github.intra.douban.com/accounts/accounts/pull/603


- 之前注册都是简单粗暴的全部打包至单个 `register.js`，但是现在这块功能越来越多（注册、忘记密码、修改密码、第三方登录绑定、绑定/解绑手机），之后还需要增加「绑定、解绑微信号」功能。且有些页面相互独立，所以根据功能，使用 `react-router dynamic-routes + wepback code-splitting` ，进行了下代码形式的拆分，方便维护，且也方便以后页面的完整拆分。
- 之前的 app directory 结构：
```
.
├── README.md
├── index.html
├── login.js
├── makewebpackconfig.js
├── master_tree.txt
├── package.json
├── register.js
├── register.js.map
├── src
│   └── register
│       ├── component
│       │   ├── display_toast.jsx
│       │   ├── get_captcha_code.jsx
│       │   ├── get_input.jsx
│       │   ├── get_phone_input.jsx
│       │   ├── nations_list.jsx
│       │   ├── normal_input.jsx
│       │   ├── pic_verify_input.jsx
│       │   ├── popup.jsx
│       │   ├── set_password_input.jsx
│       │   └── voice_captcha.jsx
│       ├── const
│       │   ├── app_const.js
│       │   └── toast_copy.js
│       ├── main.jsx
│       ├── mod
│       │   └── lang.js
│       ├── page
│       │   ├── admin.jsx
│       │   ├── bind
│       │   │   └── bind_phone.jsx
│       │   ├── forget
│       │   │   ├── complete.jsx
│       │   │   └── index.jsx
│       │   ├── register
│       │   │   ├── complete.jsx
│       │   │   ├── email_complete.jsx
│       │   │   └── index.jsx
│       │   ├── thirdparty
│       │   │   └── login.jsx
│       │   └── update_pwd
│       │       └── index.jsx
│       └── utils
│           ├── enable_storage.js
│           ├── get_cookie.js
│           ├── get_location_query.js
│           ├── offline.js
│           ├── open_app.js
│           ├── safe_phone.js
│           └── test_ua.js
└── test
    ├── component
    │   ├── DisplayToast_spec.js
    │   ├── GetInput_spec.js
    │   └── PicVerifyInput_spec.js
    └── setup.js

14 directories, 42 files
```

- 拆分之后的结构：
```
.
├── README.md
├── build
│   ├── Admin.chuck.js
│   ├── Admin.chuck.js.map
│   ├── EmailComplete.chuck.js
│   ├── EmailComplete.chuck.js.map
│   ├── ForgetIndex.chuck.js
│   ├── ForgetIndex.chuck.js.map
│   ├── Routes.chuck.js
│   ├── Routes.chuck.js.map
│   ├── register.js
│   ├── register.js.map
│   ├── vendor.js
│   └── vendor.js.map
├── index.html
├── login.js
├── makewebpackconfig.js
├── master_tree.txt
├── new_tree.txt
├── package.json
├── src
│   └── register
│       ├── Entry.js
│       ├── component
│       │   ├── display_toast.jsx
│       │   ├── get_captcha_code.jsx
│       │   ├── get_input.jsx
│       │   ├── get_phone_input.jsx
│       │   ├── nations_list.jsx
│       │   ├── normal_input.jsx
│       │   ├── pic_verify_input.jsx
│       │   ├── popup.jsx
│       │   ├── set_password_input.jsx
│       │   └── voice_captcha.jsx
│       ├── const
│       │   ├── app_const.js
│       │   └── toast_copy.js
│       ├── mod
│       │   └── lang.js
│       ├── page
│       │   ├── App.js
│       │   ├── admin.jsx
│       │   ├── bind
│       │   │   └── bind_phone.jsx
│       │   ├── forget
│       │   │   ├── complete.jsx
│       │   │   └── index.jsx
│       │   ├── register
│       │   │   ├── complete.jsx
│       │   │   ├── email_complete.jsx
│       │   │   └── index.jsx
│       │   ├── thirdparty
│       │   │   └── login.jsx
│       │   └── update_pwd
│       │       └── index.jsx
│       ├── routes
│       │   ├── AdminRoute.js
│       │   ├── BindPhoneRoute.js
│       │   ├── ForgetRoute.js
│       │   ├── RegRoute.js
│       │   ├── RootRoute.js
│       │   ├── ThirdpartyLoginRoute.js
│       │   └── UpdatePwdRoute.js
│       └── utils
│           ├── enable_storage.js
│           ├── get_cookie.js
│           ├── get_location_query.js
│           ├── offline.js
│           ├── open_app.js
│           ├── safe_phone.js
│           └── test_ua.js
└── test
    ├── component
    │   ├── DisplayToast_spec.js
    │   ├── GetInput_spec.js
    │   └── PicVerifyInput_spec.js
    └── setup.js

16 directories, 61 files
```

同是用 pre 测试，

- 拆分前 JS 加载情况：
  master pre:
  https://dae-pre113.dapps.douban.com/app/register#/?_k=x5uqd1![image](https://media.github.intra.douban.com/user/62/files/98862f18-ec97-11e6-9cb1-1250eed5dc48)



- 拆分后 JS 加载情况：
  pre:
  https://dae-pre52.dapps.douban.com/app/register#/?_k=pu9dsx
  ![image](https://media.github.intra.douban.com/user/62/files/252d9e02-ec97-11e6-90a3-d6907293a526)
- https://medium.com/@ryanflorence/welcome-to-future-of-web-application-delivery-9750b7564d9f#.5d5fthc2r
- Khan Academic JS packaging problem http://jamie-wong.com/2014/11/29/the-js-packaging-problem/
- 然而上线后就出现了错误。


> 对于这种上线后静态文件地址，https://img3.doubanio.com/f/accounts/649fb780a720cd7f76e0cd6681cbaf439137a450/js/app/register.js，这些都是在 Mako 中用 static() 拿到的。但这样的话，如果没有在 mako 里面写明 static(/js/file.js) ，那上线后如何能拿到那个 file.js 静态文件的线上 url 呢？用 webpack 配置了半天的前端动态路由，结果上线遇到 CDN + hash 就没法了。。

https://github.com/webpack/webpack/blob/webpack-1/lib/JsonpMainTemplate.runtime.js vendor 这块的 sourceURL 出了问题。

https://img3.doubanio.com/f/accounts/[hash]/

关键看看 hash 算法是不是一致webpack: https://github.com/webpack/webpack/blob/webpack-1/lib/Compilation.js#L800

https://nodejs.org/api/crypto.html#crypto_crypto_createhash_algorithm

FM 解决方案：https://github.intra.douban.com/FM/fm/blob/master/fm/view/utils/san.py#L67

只是在想，我依旧是一个「使用」产品而不是「创造」产品的程序员。

什么时候可以成为创造产品的一员呢？

之前就是想成为一名「大前端」，所以工程化的东西还挺好玩的吧。



------

http://andrewhfarmer.com/react-ajax-best-practices/

React Container + Present Component Interaction with backend.



------

不使用 `Mobx` `Redux` 等 React Store 框架，自己造一个小的 store

例如显示全局图形验证码的 `CaptchaStore.js`

```javascript
// `/src/store/CaptchaStore.js`
const EventEmitter = require('event').EventEmitter
const emitter = new EventEmitter()

let captchaObj = {
  captcha_id: '',
  captcha_image_url: '',
  captcha_signature_sample: '',
  captcha_solution: ''
}

module.exports = {
  getCaptcha: () => captchaObj,
  subscribe: (cb) => {
    emitter.on('update', cb)
  },
  unsubscribe: (cb) => {
    emitter.removeListerner('update', cb)
  },
  updateCaptcha: (newCaptcha) => {
    captchaObj = Object.assign({}, catpchaObj, newCaptcha)
    emitter.emit('update')
  }
}

// Component.js
import React from 'react'
import CaptchaStore from '/store/CaptchaStore'
export default class Component extends React.component {
  constructor() {
    super()
    this.state = {
      captcha: CaptchaStore.getCaptcha()
    }
    // this.autobind()
  }
  updateCaptcha() {
    this.setState({
      captcha: CaptchaStore.getCaptcha()
    })
  }
  componentWillMount() {
    CaptchaStore.subscribe(this.updateCaptcha.bind(this))
  }
  componentWillUnmount() {
    CaptchaStore.unsubscribe(this.updateCaptcha.bind(this))
  }
}

```

---

关于 版本匹配

`let version = [m1, m2, m3]`

可能需要用到数组、字符串相关操作 https://ponyfoo.com/articles/fun-with-native-arrays#computing-with-reduce-reduceright

可以结合 creator ，总结三个值按顺序比大小的方法。