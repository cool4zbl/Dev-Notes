# Handling Long and Unexpected Content in CSS

## 解决 CSS 中又长又并不期待的小内容（主要是英文字符内容）

---

原文:

https://css-tricks.com/handling-long-unexpected-content-css/


> When you code CSS, you're writing abstract rules to take *unknown* content and organize it in an *unknown* medium. - [Keith J. Grant](https://twitter.com/keithjgrant/status/842728744653676544)


- Write CSS forgetting about some *edge cases* in the designs.
- At least reduce the break by testing different types of content.


## A button with an icon placed on the right/left side   放置在左、右两端带图标的按钮

### Issue
![img](https://cdn.css-tricks.com/wp-content/uploads/2017/06/button-w-icon.jpg)


### Solution

```scss
// Add an enough padding to the right side to accommodate for the size of the icon:

<a href="#" class="issue-fixed"><strong>1 eBooks</strong> (PDF, ePub, Amazon Kindle)</a>

a.issue-fixed {
  width: 200px;
  padding-right: 2.5rem;
}
```


## Input Placeholder

![img](https://cdn.css-tricks.com/wp-content/uploads/2017/06/long-label.jpg)



### Issue

[float label pattern](https://css-tricks.com/float-labels-css/) for our forms, especially with a button on the right side.

### Solution

```scss
<form action="" class="issue-fixed">
  <label for="">
    <input type="text"/>
    <span>Here is a long placeholder content</span>
  </label>
  <button>Login</button>
</form>

.issue-fixed button {
  position: relative;
}
```



## Long Names

![img](https://cdn.css-tricks.com/wp-content/uploads/2017/06/long-name.jpg)


### Issue

The image is floated to the left and we have an author name on the right.

When the name is longer than expected, the UI will break.

**The issue there is that we only floated the image to the left side. This might cause the author name to move beside it.**

### Solution

1. *BFC*(Block-Formatting Context): Float author image and add `overflow`
2. Use `flexbox`

```scss
//  Float the author image and add overflow: hidden to the author name wrapper
<article class="card card--fixed">
  <time>31 March 2017</time>
  <h2>Using DevTools to Tweak Designs in the browser</h2>
  <div class="card-meta">
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/182774/shadeed_copy_copy.jpg" alt="">
    <div class="author">
      <span>Written by</span>
      <h3>Christopher Armstrong</h3>
    </div>
  </div>
</article>
.card-meta {
  overflow: auto;
  margin-top: auto;
}
/***************/
.card--fixed {
  .card-meta {
    display: flex;
  }
}

.card--fixed-2 {
  .author {
    float: none;
  }
}
/*************/
```



## Long links/words inside an article body

![img](https://cdn.css-tricks.com/wp-content/uploads/2017/06/long-link.jpg)

### Issue

Not cause an issue with a very wide viewport. But for *smaller sizes* like mobile or tablet, this will cause *a horizontal scrolling* and it will be annoying.

### Solution

#### 1) Use CSS word-break

Ref: Handling Long Words and URLs (Forcing Breaks, Hyphenation, Ellipsis, etc) https://css-tricks.com/snippets/css/prevent-long-urls-from-breaking-out-of-container/

![img](http://cdn.css-tricks.com/wp-content/uploads/2012/01/commentbreak.png)

```css
.article-body p {
  word-break: break-all;
}

// Here's a big snippet
.dont-break-out {

  /* These are technically the same, but use both */
  overflow-wrap: break-word;
  word-wrap: break-word;

  -ms-word-break: break-all;
  /* This is the dangerous one in WebKit, as it breaks things wherever */
  word-break: break-all;
  /* Instead use this non-standard one: */
  word-break: break-word;

  /* Adds a hyphen where the word breaks, if supported (No Blink) */
  -ms-hyphens: auto;
  -moz-hyphens: auto;
  -webkit-hyphens: auto;
  hyphens: auto;
}

```

- `overflow-wrap: break-word;` makes sure the long string will wrap and not bust out of the container.
- `overflow-wrap` 和 `word-wrap` 只是名字不一样。这个属性可以确保长字符串的自动换行，但又不会炸开 container.
- With `overflow-wrap` in use all by itself, words will break kinda anywhere they need to. If there is an "acceptable break" character (like a literal dash, for instance), it will break there, otherwise it just does what it needs to do.
- `overflow-wrap` 基本是自动处理，如果这里有个"可接受的换行" 字符，比如文本下划线，那它就会换行。否则需要才加上。
- You might as well use `hyphens` as well.
- 也还是可以用`hyhphens` 连字符属性，支持这个属性的浏览器会优雅地加一个连字符直到炸了。
- `word-break: break-all;` is to tell the browser that it's OK to break the word wherever it needs to. Even though it kinda does that anyway so I'm not sure in what cases it's 100% necessary.
- `work-break: break-all;` 告诉浏览器，需要的话这里可以断开一个单词。但是我也不确定在什么情况下 100% 需要这样写。

#### 2) Add overflow to the wrapper element and text-overflow to the links

```css
.article-body p {
  overflow: hidden;
  text-overflow: ellipsis;
}

// mixin
.ellipses {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
```

![img](https://cdn.css-tricks.com/wp-content/uploads/2017/06/long-link-solution.jpg)

## Long article tags

![img](https://cdn.css-tricks.com/wp-content/uploads/2017/06/article-tags.jpg)

### Issue

We should only add padding for the spacing. Determining width and height might make the UI break when the tag content is too long.

### Solution

If you want to have a minimum width for the tag, that's fine. We can use `min-width` with padding around the tag content. That way, the width will be dynamic.

少用固定宽度。`min-width` 动态宽度。

```scss
<article class="card">
  <span class="category fix">web design and development</span>
  <time>31 March 2017</time>
  <h2>Using DevTools to Tweak Designs in the browser</h2>
</article>

.category.fix {
  // could alternatively use
  min-width: 120px;
  height: auto;
}
```



### Section header with a link

we have a section title along with a "view more" link on the right.

If we use absolute positioning for the link.

![img](https://cdn.css-tricks.com/wp-content/uploads/2017/06/section-header.jpg)



### Issue

The title is too long.

![img](https://cdn.css-tricks.com/wp-content/uploads/2017/06/section-header.gif)



### Solution

```scss
<header class="header-2">
  <h2>Web Design & Development</h2>
  <a href="#">View more</a>
</header>
.header-2 {
  position: relative;
  display: flex;  // Use flexbox
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: center;

  h2 {
    margin-right: 0.5rem;
  }

  a {
    position: absolute;
    right: 0;
    top: 5px;
  }
}

```

*Bonus*

Testing the layouts with all types of content until something breaks.

 I like to use Heydon Pickering's [forceFeed.js](https://github.com/Heydon/forceFeed) for adding content randomly to a specific component.

对需要的组件自动填充各种极端情况测试内容！



---



**思考：**

最近做的 SNS people_portflio 会出现以下情况。

![white-space-bug](../../Desktop/Code-Screenshots-代码截图/white-space-bug.png)

原因是 `title` `info` 等虽然设置了 `ellipsis`，但是没有设置最大宽度，所以会导致宽度超过父元素的最大宽度。

workaround: 给它们都设置一个固定的最大宽度。不知道还有没有更好的办法。

![white-space-fixed](../../Desktop/Code-Screenshots-代码截图/white-space-fixed.png)

```css
.title, .info .more {
  display: block;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  
  max-width: $itemW;  // 在这里写死一个最大宽度
}
```

Ref:  [Clamp.js](https://github.com/josephschmitt/Clamp.js/blob/master/clamp.js)

一个把对应元素限定在某个具体数值行数的库。

待继续研究...