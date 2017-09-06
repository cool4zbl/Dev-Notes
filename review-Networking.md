最近想总结下前后端数据交互方式，顺便继续学习下网络相关，以下是第一篇，最简单、最常见且应用最广泛的「表单提交」。

主要有两种，提交前后有刷新的普通表单提交和无刷新的 Ajax 表单提交。

## 1. 普通表单提交

下面是一个很普通的用户登录表单：

```html
<form class="form login-form" id="loginForm" 
      method="POST" action="api.test.com/login">
  <label for="name">Username</label><input type="email" id="name">
  <label for="password">Password</label><input type="password" id="password">
  <label for="captcha">Captcha</label><input type="text" id="captcha">
  
  <label for="selLoc">Location</label>
  <select name="location" id="selLoc">
    <option value="cn">China Mainland</option>
    <option value="hk">Hong Kong</option>
    <option value="tw">Tai Wan</option>
    <option value="us">America</option>
    <option value="jp">Japan</option>
    <option value="">Somewhere</option>
    <option >Anywhere</option>
  </select>
  
  <!--  General submit button  -->
  <input type="submit" value="Submit Form">
  <!--  Customize  -->
  <button type="submit">Submit Form</button>
  <!--  Image button   -->
  <input type="image" src="graphic.gif">
</form>
```

- 当用户单击提交按钮或者图像按钮的时候，就会提交表单；

- `<input type="submit">` / `<button type="submit">` / `<input type="image" src='img.gif'>` 都可以作为表单提交按钮。且只要存在任何一种按钮，在相应表单控件有焦点的情况下，按回车键就可以提交该表单，这里应该是浏览器对表单元素回车键的默认行为，所以在提交表单前，会触发 `submit Event`，可以用来做表单验证；

- 如果直接使用 JS `submit()` 提交表单，这里则不会触发表单的 `submit Event`，所以需要在调用方法前做表单验证；

   ```javascript
   let form = document.getElementById('loginForm')
   form.submit()
   ```

  - 以上的方法提交表单，就是一个普通的表单提交，会触发浏览器提交表单的默认行为：刷新页面！用户体验也将大打折扣。所以出现了优化版的 Ajax 表单提交。




## 2. Ajax 表单提交

仍然是上面的表单元素，因为 ajax 时候可以指定 URL，所以这里的表单元素可以省略 `action` 属性。
```html
<form class="form login-form" id="loginForm" method="POST">
  <label for="name">Username</label><input type="email" id="name">
  <label for="password">Password</label><input type="password" id="password">
  <label for="captcha">Captcha</label><input type="text" id="captcha">
  
  <label for="selLoc">Location</label>
  <select name="location" id="selLoc">
    <option value="cn">China Mainland</option>
    <option value="hk">Hong Kong</option>
    <option value="tw">Tai Wan</option>
    <option value="us">America</option>
    <option value="jp">Japan</option>
    <option value="">Somewhere</option>
    <option >Anywhere</option>
  </select>
  
  <!--  General submit button  -->
  <input type="submit" value="Submit Form">
  <!--  Customize  -->
  <button type="submit">Submit Form</button>
  <!--  Image button   -->
  <input type="image" src="graphic.gif">
</form>
```



我们可以利用每个表单字段的 `type` 属性，连同 `name` 和 `value` 属性一起实现对表单的序列化。先了解下浏览器将数据发送给服务器的过程如下：

> -    对表单字段的名称和值进行 URL 编码，使用和号(&) 分隔。
>
> -    不发送禁用的表单字段。
>
> -    只发送勾选的复选框和单选按钮。
>
> -    不发送 type 为 "reset" 和 "button" 的按钮 。
>
> -    多选选择框中的每个选中的值单独一个条目。
>
> -    在单击提交按钮提交表单的情况下，也会发送提交按钮；否则，不发送提交按钮。也包括 type
>
>   为 "image" 的`<input>`  。
>
> -    `<select> ` 的值，就是选中的 `<option>`  元素的 value 特性的值。如果 `<option>`  没有
>
>   value 特性，则是 `<option>` 元素的文本值。
>
> — — 《JavaScript 高级程序设计》

在表单序列化的过程中，一般不包含任何按钮字段，因为结果字符串很可能是通过其他方式提交的，除此之外其他规则都应该遵循。以下就是实现表单序列化的代码。

```javascript
// ref: Professional JavaScript Ch14.4
function serialize(form) {
  let parts = [],
      field = null,
      option, optValue
  
  for (let i = 0, len = form.elements.length; i < len; i++) {
    field = form.elements[i]
    
    switch(field.type) {
      // select elements
      case 'select-one':
      case 'select-multiple':
        if (field.name.length) {
          for (let j = 0, optLen = field.options.length; j < optLen; j++) {
            option = field.options[j]
            if (option.selected) {
              // DOM
              optValue = ''
              if (option.hasAttribute) {
                optValue = option.hasAttibute('value')
                  ? option.value : option.text
              } else {
                // IE
                optValue = option.attributes['value'].specified
                  ? option.value : option.text
              }
              parts.push(`${encodeURIComponent(filed.name)}=${encodeURIComponent(optValue)}`)
            }
          }
        }
        break
      case undefined:
      case 'file':  // files input
      case 'submit': // submit buttons
      case 'reset': // reset buttons
      case 'button': // customize buttons
        break
      
      case 'radio':  // radio buttons
      case 'checkbox':  // checkbox
        if (!field.checked) {
          break
        }
      default:
        // excluding no name field
        if (field.name.length) {
          parts.push(`${encodeURIComponent(filed.name)}=${encodeURIComponent(optValue)}`)
        }
    }
  }
  return parts.join('&')
}
```

回到用户登录场景，当用户填写了表单字段，`name` 为 `cool4zbl`，`password` 为 `secret@Pass` ，选择 `location` 为`America`，然后点击 `submit` 按钮提交表单。我们可以监听到 `submit Event`，拦截默认的表单提交事件，然后在提交表单前执行 `serialize(document.getElementById('loginForm'))` ，将会返回

`name=cool4zbl&password=sercret%40Pass&location=America`，然后就可以使用 Ajax Post 序列化后的数据到服务器，根据服务器端返回的信息（一般为`json`格式）来显示是否登录成功或者是出现了错误。

```javascript
let form = document.getElementById('loginForm')
form.onsubmit = (e) => {
  e.preventDefault()
  const data = serialize(form)
  $.ajax({
    data,
    url: 'api.test.com',
    type: form.getAttribute('method'),
    contentType: 'application/json; charset=UTF-8',
    dataType: 'json',
    success: function(resData) {
      // do something with success resData
      // blah
    },
    error: function(xhr, type) {
      // Oops, something wrong occurs...
    }
  })
}
```

这里为了专注于解释提交表单前后浏览器的行为，使用 jQuery ajax API 简化了具体前后端 Ajax 通信时候发生了什么。

**表单提交**这应该是前端应该掌握的最基本，也是应用得最广泛的前后端数据交互方式了。

当然还有浏览器通信时的一些坑会被踩到，需要去填，所以更详细的 Ajax 通信总结请参考下一篇「前后端数据交互 - Ajax」。

以上。







前后端数据交互方式

1. 表单提交
2. Ajax
3. WebSocket?

- 基本的原生 XMLHttpRequest / Promise / fetch / async & await
- 一些库 jQuery / Zepto / axios / superagent 对于 AJAX 实现
- 继续拓展，在 react 中的应用？ fetch 高阶组件。 在 redux 中的应用？API middlewares 或者是 dispatch async aciton




**XMLHttpRequest**



**Ajax GET vs POST** 

- GET 只能发送经过 `encodeURIComponent` 编码后的查询字符串信息
- POST 主体可以发送大量数据，且格式不限
- 与 GET 请求相比，POST 请求消耗的资源会更多一些。从性能角度来看，以发送相同的数据计，GET 请求的速度最多可达到 POST 请求的两倍。

> 不过，我们可以使用 XHR 来模仿表单提交：首先将 Content-Type 头部信息设置为 application/x-www-form-urlencoded，也就是表单提交时的内容类型，其次是以适当的格式创建一个字符串。第 14 章曾经讨论过，POST 数据的格式与查询字符串格式相同。 需要 serialize() 化
>
> `xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");`
>
> — JS  高程



**XMLHttpRequest.readyState**

```
 0:未初始化。尚未调用 open()方法。
 1:启动。已经调用 open()方法，但尚未调用 send()方法。
 2:发送。已经调用 send()方法，但尚未接收到响应。
 3:接收。已经接收到部分响应数据。
 4:完成。已经接收到全部响应数据，而且已经可以在客户端使用了。
```






下面是一个用`Promise`对象实现的 Ajax 操作的例子。

```javascript
var getJSON = function(url) {
  var promise = new Promise(function(resolve, reject){
    var client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();

    function handler() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
  });

  return promise;
};

getJSON("/posts.json").then(function(json) {
  console.log('Contents: ' + json);
}, function(error) {
  console.error('出错了', error);
});
```

上面代码中，`getJSON`是对 XMLHttpRequest 对象的封装，用于发出一个针对 JSON 数据的 HTTP 请求，并且返回一个`Promise`对象。需要注意的是，在`getJSON`内部，`resolve`函数和`reject`函数调用时，都带有参数。



### 可以跨域的元素

在浏览器中，`<script>`、`<img>`、`<iframe>`和`<link>`这几个标签是可以加载跨域（非同源）的资源的，并且加载的方式其实相当于一次普通的GET请求，唯一不同的是，为了安全起见，浏览器不允许这种方式下对加载到的资源的读写操作，而只能使用标签本身应当具备的能力（比如脚本执行、样式应用等等）。

### CORS

其实客户端可以向任意域名发送请求（除非 https ajax 请求 http 则不能），只要后端设置响应头 `'Access-Control-Allow-Origin': request.origin` 即可。

>Redirect from 'https://douban.com/' to 'https://www.douban.com/' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'null' is therefore not allowed access.

对于浏览器， Chrome Safari 会包含一个 `Origin` 头在同源的 POST/PUT/DELETE 请求中，GET 方法没有。

Firefox 不会包括 Origin Header 在同源的请求中。

> Chrome and Safari include an `Origin` header on same-origin POST/PUT/DELETE requests (same-origin GET requests will not have an Origin header). Firefox doesn't include an `Origin`header on same-origin requests. Browsers don't expect CORS response headers on same-origin requests, so the response to a same-origin request is sent to the user, regardless of whether it has CORS headers or not.
>
> — [stackoverflow](https://stackoverflow.com/questions/15512331/chrome-adding-origin-header-to-same-origin-request)




客户端 `xhr.withCredential = true` // 带凭证

```
CORS 通过一种叫做 Preflighted Requests 的透明服务器验证机制支持开发人员使用自定义的头部、 GET 或 POST 之外的方法，以及不同类型的主体内容。在使用下列高级选项来发送请求时，就会向服务 器发送一个 Preflight 请求。这种请求使用 OPTIONS 方法，发送下列头部。
 Origin:与简单的请求相同。
 Access-Control-Request-Method:请求自身使用的方法。
 Access-Control-Request-Headers:(可选)自定义的头部信息，多个头部以逗号分隔。
以下是一个带有自定义头部 NCZ 的使用 POST 方法发送的请求。
Origin: http://www.nczonline.net
Access-Control-Request-Method: POST
Access-Control-Request-Headers: NCZ
```

服务器端 Header `Access-Control-Allow-Credentials: true`

```
 Access-Control-Allow-Origin:与简单的请求相同。
 Access-Control-Allow-Methods:允许的方法，多个方法以逗号分隔。
 Access-Control-Allow-Headers:允许的头部，多个头部以逗号分隔。
 Access-Control-Max-Age:应该将这个 Preflight 请求缓存多长时间(以秒表示)。
例如:
Access-Control-Allow-Origin: http://www.nczonline.net
Access-Control-Allow-Methods: POST, GET
Access-Control-Allow-Headers: NCZ
Access-Control-Max-Age: 1728000
```



### jsonp

1. 本地有个 `callback function`需要 responseData 参数，动态创建 `<script src='http://target.com/test.js'>` 
2. test.js 里面调用 callback(responseData) 
3. 本地加载 test.js, 则会自动调用 callback

升级版，因为 `<script>` tag src 加载跨域资源，那么本地动态创建

`<script src='http://target.com/test?q=queryWhat&callback=result'>` 

本地有个 const result = resData => { doSomething(resData); }

服务器端对于 src 请求地址返回内容：`context.Response.Write(callback + "(" + json + ")");`

那么加载完则会自动调用 result()



**网络离线检测**

`window.offline` API