### SNS Reshared Status with copy

---

- 需求：
- PR:
- URL:

---

### 方案
-因为广播模板过多（大概有接近百种的广播类型，以及对应的五十多种广播模板），为了尽可能快的上线和复用现有的模板结构，采用了直接 `include` 对应 Mako 模板；
- 一开始有想过改 `mod` 的 `caller.hd()` `caller.bd()` ，因为传过来的模板调用只有一个 `status`，不是当前广播，就是当前广播的祖先广播 `status.reshared_status`，而根据设计图，这两者必须同时显示出来，而且需保持现有广播模板设计，重写的话工作量也是挺大；
- 如果改 `mod` 的话，需要给每个 `mod` 及 `tmpl` 都多传一个 `status` 参数，还需要 copy 对应的模板样式，特别麻烦，头疼尝试了两天后决定放弃；
- 仔细研究了下最开始的渲染模板代码 `mods/status.html`，有个表结构, 然后根据 `status.target_type` map 到需要渲染的对应的 `template`， 最后 `include` 该模板；
- 这里就开始开脑洞了，既然设计上就是嵌套的广播模板结构，只是隐藏了一些祖先广播的操作，那代码实现何不复用现有的结构，直接再 `include` 现有模板呢？
- 于是在 `mods/status.html` 下加了一行

```html
<div class="status-reshared-wrapper">
  % if st.reshared_status:
    <%include file="${template}" argu="st=st.reshared_status">
  % endif
</div>
```

这样就可以同时渲染出两个不同的独立的广播了。

- 再运用各种 HTML & CSS ，fix 各种样式对齐，达到设计稿的样子就好了！(这里真想吐槽原来的广播结构和样式，写得非常死，特别难以拓展，需要各种 hack...)
- 复用模板更妙的一点是，这里原广播的一切交互都是有的，不需要再写一套对应的事件处理；
- 在转播框的时候，都可以直接取 `div.status-real-wrapper`, `div.status-reshared-wrapper` ，放到转播框中，只需要针对设计做一点点小调整，所有的交互功能依旧是保留的。
- 只能说，合理利用 HTML & CSS & JS，任意操作 DOM。

---

### CSS
- 因为基本是 include 各种现有的模板，但是不改变它们原有的交互，所以这里重新写样式来达到设计稿的要求；
- 感慨就是 CSS 很强大；
- 嵌套层级复用；
- CSS 优先级的斗争；
- `position: relative;` `position: absolute;` 完美运用;

---

### JS
- Constructor Inheritance or Prototype Inheritance ?

- Think: 如果一个元素同时被各种事件绑定，事件绑定触发的顺序是？

- 动态 textarea 字数统计：
  - 使用 ES6 模板 render, 但是需要之后手动 init，及绑定 `keyup` `input` 等事件来每次更新值
  - 试试 VisualDOM ?

- `new ReshareDialog()`;

- 使用 tagsug 增加 @someone 功能；

- 在不改变布局的情况下，复用 ajax 加载的回应框及回应者和内容

- `Do.add('dialog', ${path})`  -> 添加 dialog 作为接下来需要异步加载的 JS;
- `Do('dialog')` 向 path 发送请求，加载 dialog JS

---

### 遇到的一些问题及思路

-  dialog 里推荐网页去掉动作
-  第一次转播单纯的说 都去掉描边
-  修转播回应框
-  修转播广播点击区域跳转
-  chrome 打开 dialog hd 的高度有 bug ?自动出现滚动条 overflow ?
-  发布成功 disabled btn 防止连击 beforeSend() 前 disable submit btn
-  throttle ? debounce ? Debounce／throttle／buffer等这些与时间相关的操作
-  文字排版 white-space: pre-wrap; 超出了四行的话需要去掉，小于这个字数再加上。
-  计算每次转播文字的长度，算出对应的高度，计算 原始广播顶端负边距 line0 line1 …. line7
-  ajax 回应 DOM reflow + repaint 每次重新计算位置；使用 pos: abs 来定位。
-  转播时候，点击回应，再查看带图片广播会定位出现问题。需要重新计算高度。height: auto;  啊啊啊，只能直接缩小图片了。带回应时候小图，大图时候隐藏回应。
-  只在广播流！不在 detail page !
-  celebrity.html blockquote 改位置
-  fix 点击转播内的链接正确跳转，而不是跳转到广播 url
-  转播成功加提示 关闭当前 dialog，涉及到 setTimeout 的 this 绑定问题。使用 () => {} 闭包解决
-  @人，
-  增加 #话题# 
-  tagsug  插件 
-  展开相似广播 prevAll() 会不会展开不属于该作者的隐藏广播？(我觉得会…）
-  如果 query str 在 # hash 后面会不起作用
-  恢复 grammarly 插件)"

### BUGS
- DAE `*.es6` 编译调试时候出现语法错误不会报错！！！

