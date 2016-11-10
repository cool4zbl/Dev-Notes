### SNS 发现更多话题页

---

- 需求：增加一个发现更多话题的入口，设计上采用网格布局
- PR: https://github.intra.douban.com/sns/sns/pull/783 
- URL: https://www.douban.com/gallery/explore[?column_id=xxx]

#### CSS
- 既然是网格布局，思考使用 `CSS Grid` or `Flexbox`。
与设计师交流到他的设计思想就是用了 `Grid` 布局，
所以本考虑根据收藏了很久的[这篇文章](http://gridbyexample.com/) 来学习，
`Grid` 也适合水平与垂直方向都是规则对称的盒子.

- 但是 [CanIUse Grid](http://caniuse.com/#search=grid) 结果触目惊心。

- 仍使用 `flexbox` 布局
[CanIUse Flexbox](http://caniuse.com/#search=flexbox) 记录了一些 IE Issue 和解决方案。

```CSS
@mixin flexbox() {
  display: -webkit-flex;
  display: -moz-flex;
  display: -ms-flex;
  display: flex;
}

@mixin flex($val) {
  display: inline-block; //  compatible to IE 9+
  -webkit-flex: $val;
  -moz-flex: $val;
  -ms-flex: $val;
  -o-flex: $val;
  flex: $val;
}

/* container */
.flexcontainer {
  @include flexbox();

  -webkit-flex-direction: row;
  -webkit-flex-flow: nowrap;


  // or just one line short-hand
  flex: row nowrap;
}
/* item */
.flexitem {
  @include flex(1);
}
```

#### Reference
- [Simplitify Flexbox Cheat Sheet](http://www.sketchingwithcss.com/samplechapter/cheatsheet.html)

