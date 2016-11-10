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

Install vim-UltiSnips

反正，自己编译的 Vim 不行。

可能是调用 Python 接口的方法不太对。

Finally, `brew install vim` solved.

```shell
Error detected while processing /Users/zhangbinliu/.vim/bundle/ultisnips/autoload/UltiSnips.vim:
line  226:
Traceback (most recent call last):
  File "<string>", line 1, in <module>
ImportError: No module named os
line  229:
Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File "C:\Documents and Settings\username\vimfiles\bundle\UltiSnips-2.2\pl
ugin\UltiSnips\__init__.py", line 4, in <module>
    from functools import wraps
ImportError: No module named functools
line  230:
Traceback (most recent call last):
  File "<string>", line 1, in <module>
NameError: name 'UltiSnips_Manager' is not defined
line  231:
Traceback (most recent call last):
  File "<string>", line 1, in <module>
NameError: name 'UltiSnips_Manager' is not defined
line  232:
Traceback (most recent call last):
  File "<string>", line 1, in <module>
NameError: name 'UltiSnips_Manager' is not defined
```



---

对于 `webpack` 打包好的文件，
在 开发的时候，如果只是改动了一小部分，
`Git` 相关可以暂时忽略掉 `register.js` `register.js.map` 相关的修改。

否则，`git rebase` 简直...

---

`RegExp.test()` 方法千万别用 `g` 标识！！！！

- `true` ` false` 交替返回；
- 因为下一次匹配会从上一次匹配到的位置重新开始。

---

连字符相关：

- CSS className
  - 默认`-`， e.g.: `flag-wrapper`；
  - `_` ，如果是可能需要用到 Python 和 JS 操作的，使用`_` ，e.g.:`vote_useful / vote_useless`
- JS Variable name:
  - 默认 `camelCase`


---

`captcha_code_img` 前端 md5 验证



---

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
