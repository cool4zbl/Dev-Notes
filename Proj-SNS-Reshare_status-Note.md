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
- 只能说，合理利用 HTML & CSS，任意操作 DOM。

---

### BUGS
- DAE `*.es6` 编译调试时候出现语法错误不会报错！！！
