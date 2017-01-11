### SNS Status Redesign & Reshared Status with copy

---

- 需求：
  转播广播加文字
- PR:

- URL:
  Web 主站

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
-  ajax 回应 DOM reflow + repaint 每次重新计算位置；使用 pos: abs 来定位。
-  转播时候，点击回应，再查看带图片广播会定位出现问题。需要重新计算高度。height: auto;  啊啊啊，只能直接缩小图片了。带回应时候小图，大图时候隐藏回应。
- 这样的话，其实每次点击「隐藏回应」「显示回应」「查看/缩小图片」都会进行一次回流和重绘（Reflow & Repaint）[减少页面 Reflow & Repaint](http://harttle.com/2015/08/11/reflow-repaint.html)
- [What forces layout / reflow - Paul Irish](https://gist.github.com/paulirish/5d52fb081b3570c81e3a)
- [Rendering: repaint, reflow/relayout, restyle - Stoyan](http://www.phpied.com/rendering-repaint-reflowrelayout-restyle/)

-  修转播广播点击区域跳转 判断 `$(e.target).is('img')` `$(e.target).is('a')` 来根据点击区域进行跳转。这里注意如果原始广播内有 a 链接的时候注意不要 `e.preventDefault()` 而是默认跳转到对应链接

-  chrome 打开 dialog hd 的高度有 bug ?自动出现滚动条 overflow ?

-  发布成功 disabled btn 防止连击 beforeSend() 前 disable submit btn
-  throttle ? debounce ? Debounce／throttle／buffer等这些与时间相关的操作

-  文字排版 white-space: pre-wrap; 超出了四行的话需要去掉，小于这个字数再加上。

-  计算每次转播文字的长度，算出对应的高度，计算 原始广播顶端负边距 line0 line1 …. line7
-  只在广播流！不在 detail page !

-  转播成功加提示 关闭当前 dialog，涉及到 setTimeout 的 this 绑定问题。使用 () => {} 闭包解决

-  @人，
-  tagsug  插件
-  增加 #话题#
-  展开相似广播 prevAll() 会不会展开不属于该作者的隐藏广播？(我觉得会…）
-  如果 query str 在 # hash 后面会不起作用

- Loading 以及 一些 toast 动画使用 SVG
```CSS
// Background + Data URIs
$loadingSVG: 'data:image/svg+xml;utf8, <svg>...</svg>'
.loading {
background-image: url($loadingSVG)
background-size: 20px;
background-position: 0 50%;
}
```
> 这里的Sprite技术，类似于CSS中的Sprite技术。图标图形整合在一起，实际呈现的时候准确显示特定图标。其实基础的SVG Sprite也只是将原来的位图改成了SVG而已，控制SVG大小、颜色需要重新合并SVG文件。

-  使用 Sprite 可以认为加了一张普通背景图片, 可以修改 size / position 等
- 如果需要加载很多不同的 SVG 图的话，可以使用 `symbol/defs` `use` 来制作 SVG Sprite.
- 新方案：[Svg Sprites +Png Sprites + Image-Set](https://jarevrygo.gitbooks.io/webbook/content/5-2-1svgli_jie_yu_yun_yong.html)

- Animation: 打开和关闭转播 dialog 时候增加渐入渐出效果
```CSS
// Simple fadein & fadeout
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}
@keyframes fadeOut {
  from { opacity: 1; }
  to { opacity: 0; }
}
.fadein {
  animation: fadeIn .5s ease-in;
}
.fadeout {
  animation: fadeOut 1.5s ease-out;
}
```
- 关于 UI 动画一篇不错的 Keynote [The Art of UI Animation](http://markgeyer.com/pres/the-art-of-ui-animations)

- web 端出现了间距不一致的问题。
研究后才发现之前老广播有设置 `:after` 为 `display: block;` 作为清浮，
```css
.mod:after {
  content: '\0020';  // space
  display: block;
  clear: both;
}
```
这应该就是问题所在了。
更新如下：
```css
.status-item {
  overflow: hidden;
  .mod {
    margin: 0;
    &:after {
      display: inline-block;
    }
  }
}
```
看来 `overflow: hidden;` 结合 `:after { display: inline-block; }` 可以再深入学习下相关应用场景；
- [Quirksmode css display](http://quirksmode.org/css/css2/display.html)

- 新转播广播上线后，发现用手机端(iOS Safari)浏览 WWW 站时候出现转播广播文字叠字 bug，因为 margin 过小，一开始并不知道原因，后来发现是因为没有设置 `-webkit-text-size-adjust`，而转播广播上下 margin 使用 `em` 单位计算，之前历史遗留问题，广播各个模块设置了自己独立的 `font-size`，所以每处地方的 margin 都不一样，所以出现了叠字。强制设定 `-webkit-text-size-adjust: 100%;` 后解决了手机浏览器打开 WWW 的文字缩放问题。
- [Can I Use text-size-adjust](http://caniuse.com/#search=text-size-adjust)
- [text-size-adjust W3C Spec](https://drafts.csswg.org/css-size-adjust/#adjustment-control)

- textarea / input `placeholder` 在文本框获得焦点的时候自动消失
这里还是想着用强大的 CSS 解决
```css
/* do not group these rules */
textarea::focus::-webkit-input-placeholder {
  opactity: 0;
}
textarea::focus::-moz-input-placeholder {
  opactity: 0;
}
textarea::focus::-ms-input-placeholder {
  opactity: 0;
}
textarea::focus::placeholder {
  opactity: 0;
}
```
在获得焦点的时候，设置 placeholder 透明，可以达到设计师说的消失效果。
这里要注意的是，这些规则只能放在最外面而不能被包裹；
且根据浏览器前缀的不同，每条规则都要单独写出来，不然不能奏效。

---

---
### 后续的更新和维护
- 转播框精简
- 隐藏不带文字的转播
- 代码优化

### BUGS
- DAE `*.es6` 编译调试时候出现语法错误不会报错！！！

