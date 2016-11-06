#### Subject Review review

---

##### JavaScript

- UI 逻辑与应用逻辑拆分开 Splitting the code into UI logic and Application logic.

- 虽然有时候已经有很好的上层包装，但是还是不得不深入到下水管道去自己修理一些底层问题。

- DOMContentLoaded 事件有时候可以 work ，有时候又不能？！

- `Document.readyState`

  ```javascript
  switch (document.readyState) {
    // var string = document.readyState
    case 'loading':
      // The document is still loading.
      break
    case 'interactive':
    	// The document has finished loading.
    	// we can access the DOM elements.
      // also fired 'DOMContentLoaded' event.
    	var span = document.createElement('span')
      span.textContent = 'Another new <span> element.'
      document.body.appendChild(span)
      break
    case 'complete':
      // The page is full loaded.
    	console.log('The first CSS Rule is :' + 
                  document.styleSheets[0].cssRules[0].cssText)
      break
  }
  ```

  `readystatechange` as an alternative to `DOMContentLoaded` event

  ```javascript
  document.onreadystatechange = function () {
    if (document.readyState === 'interactive') {
      initApplication()
    }
  }
  ```

  `readystatechange` as an alternative to `load` event

  ```javascript
  document.onreadystatechange = function () {
    if (document.readyState === 'complete') {
      initApplication()
    }  
  }
  ```

  `function ready () {}` sample:

  ```javascript
  // https://github.com/HubSpot/offline/blob/master/js/ui.js#L123

  function ready () {
    if (document.readyState === 'complete') {
      init()
    } else if (document.addEventListener != null) {
      // no false will be right ?
      // document.addEventListener('DOMContentLoaded', init)
      document.addEventListener('DOMContentLoaded', init, false)
    } else {
      _onreadystatechange = document.onreadystatechange
      document.onreadystatechange = function () {
        if (document.readyState === 'complete') {
          init()
        }
        return typeof _onreadystatechange === 'function' ? 
          _onreadystatechange.apply(null, arguments) : void 0
      }
    }
  }
  ```

- 分模块 IEFF

  ```javascript
  (function () {
    // CODE
  }).call(this)
  ```

- `createHTMLDocument(docName)`  vs `createElement(tagName)`  

  - Safety ? Difference? Pros & Cons ?
  - Event Handler 区别？
  - https://github.com/cure53/DOMPurify/issues/47
  - [WHATWG Spec Doc](https://dom.spec.whatwg.org/#dom-document-createelement)

- Using `createHTMLDocument(docName)` to create a "fresh & purify" DOM properly & safely

  ```javascript
  // https://github.com/cure53/DOMPurify/pull/72/files

  if (typeof HTMLTemplateElement === 'function') {
    document = document.createElement('template').content.ownerDocument
  }
  var doc = document.implementation.createHTMLDocument('')
  var returnFragmentNode = document.createDocumentFragment.call(body.ownerDocument)
  ```

- Considering `document.createDocumentFragement()` 

  - lightweight container may better.
  - store the nodes while improving the performance of insertion.
  - [Interface DocumentFragment](https://dom.spec.whatwg.org/#interface-documentfragment)

  ```javascript
  // Better Performance
  /*
   * @params {String} html, Template HTML
   * @return {Object} DocumentFragment
   * if need event handler, return fragment.children[0]
  */
  var createFromHTML = function (html) {
    var div = document.createElement('div')
    div.innerHTML = html
    var fragment = document.createDocumentFragment()
    while (div.firstChild) {
      fragment.appendChild = div.firstChild
    }
    return fragment
  }
  someElem.appendChild(createFromHTML(html))
  ```

- `JavaScript Source Map`

  - [Ruanyifeng](http://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html)
  - [TutPlus](http://code.tutsplus.com/tutorials/source-maps-101--net-29173)

- `MutationObserver` 

  - [WHTAWG Spec Doc](https://dom.spec.whatwg.org/#mutation-observers)

- Fragment # Hash 

  ```javascript
  // 有 `#` 的请求，`#` 后的内容最终不会发出去。
  // Solved, encodeURIComponent(url, urlWithHash)
  (new Image()).src = url + encodeURIComponent(urlWithHash)
  ```

- Document Referrer

- `RegExp.test(string)=> true | false `  & `RegExp.match(string) => matched Array `

- Chrome Extension ?

- jQuery `input disabled`

  ```javascript
  $btn.attr('disabled', true)
  $btn.attr('disabled', false)
  ```

- `ajaxBeforeSend()` 前 check 是否有 ajax 请求 pending，或者 check button 加上 `disabled` ，

- In Private Browsing mode, localStorage can be read but not written.

  ```javascript
  // test before using it to avoid error.
  window.localStorage && localStorage.setItem(val, val) && localStorage.removeItem(val)
  ```

  ​

- **解决**：Ajax 返回的 HTML 中，`script` 代码不执行问题。

  ```javascript
  // why Ajax back script not execute?
  // jQuery.html() use `eval()`
  // Then, there turns to be another problem.

  ```

- If using `div.html(resData)`, if included a `script` tag as part of the content that was being returned in `resData`, the browser will warn:

  ```
  Synchronous XMLHttpRequest on the main thread is deprecated because of its detrimental effects to the end user's experience. For more help, check https://xhr.spec.whatwg.org/.
  ```

  that if you return HTML content like that via xhr, you will cause jQuery to make a call to get that script. That call happens with an async flag false since it assumes you need the script to continue loading.

  **Solution**

  - Promise + Async requests

  - Web Worker + Sync requests

    ​

- `(1, eval)('this')` vs `eval('this')` 获取当前上下文对象

   http://stackoverflow.com/questions/9107240/1-evalthis-vs-evalthis-in-javascript

  http://perfectionkills.com/global-eval-what-are-the-options/

- `Object.assign` & `Object.create` ? Perf ? 

- **跨域 jQuery 请求**

  ```javascript
  $.ajax({
      xhrFields: {
       // for sent request with cookie
       withCredentials: true  
      }
  })
  // Request will not sent if not config like this.
  //  http://www.jquery123.com/jQuery.support/
  // It is a property. not config if not necessary.
  jQuery.support.cors = $.support.cors = true
  // cors fail, even a request not sent.
  jQuery.support.cors = false

  ```

- `onlick` 绑定的事件，要在 DOM 之前初始化，或者放同一个命名空间。

- 展开 / 收起全文的时候，`javascript: void(0);` 会打开一个新 Tab 页面

  - http://stackoverflow.com/questions/22207222/browser-opens-both-javascriptvoid0-and-onclick-link
  - http://stackoverflow.com/questions/17094413/javascriptvoid0-and-target-blank-behaviour

- 要有**模块化**，甚至**组件化**的思想！

  - 设计时候以模块 / 组件为基准
  - 原型继承
  - 尽量高度耦合
  - 能复用尽量复用

---

##### CSS

- `overflow: hidden` 上下对齐 问题。

- `img { max-width: 100%; }`  overrides `width` property. & global is dangerous. 会使 img 尽最大限度填充父元素；

- `:not(selector)`  selector 不能是 ID selector.

- ```CSS
  ::-webkit-input-placeholder {
    color: $plhdColor;
    font-size: $fz;
  }
  ```

- [Textarea placeholder Not disappering](http://stackoverflow.com/questions/10186913/html5-textarea-placeholder-not-appearing) & [Textarea tricks](https://css-tricks.com/textarea-tricks/)

- 移动端 Tab 时候闪烁 `-webkit-tab-highlight: none;`

---

#### 工程

- 文件结构的组织 （按页面分？按组件分？按功能分？）
- 跨项目如何进行代码复用？
- 在使用 Mako 以及 DAE 服务同时，又能自定义并且优雅地进行前端工程化 （webpack / gulp / browser-sync etc.）

