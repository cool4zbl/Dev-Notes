### 中文 CSS 字体

![73801e86-36ac-406f-8264-3328491f4aeb](/Users/zhangbinliu/Library/Containers/com.tencent.WeWorkMac/Data/Library/Application Support/WXWork/Temp/ScreenCapture/73801e86-36ac-406f-8264-3328491f4aeb.png)

---



- 为什么要研究中文 CSS 字体

  - 应用场景？
  - 如何配置？

  ```css
  .title h3 {
    font-size: 16px;
    font-family: FZYANS_XIAN,-apple-system,Helvetica Neue,Arial,PingFang SC,Hiragino Sans GB,Microsoft YaHei,WenQuanYi Micro Hei,sans-serif;
  }
  ```

- 中文字体的排版

- 中文字体的坑

  - 都是方块字，字体源文件大，加载慢



- 中文字体的解决方案
  - https://www.zhihu.com/question/20727176 知乎 值得推荐的中文字体
  - https://github.com/zenozeng/fonts.css 跨平台字体解决
  - http://zenozeng.github.io/Free-Chinese-Fonts/ 免费中文字体
  - http://www.youziku.com/ 有字库
  - http://cn.justfont.com/fonts 各种中文字体。




------


  <link rel="preload"> 和 <link rel="prefetch"> 的缓存行为

[Chrome 有四种缓存](): HTTP 缓存，内存缓存，Service Worker 缓存和 Push 缓存。preload 和 prefetch 都被存储在 **HTTP 缓存中**。

当一个资源被 **preload 或者 prefetch** 获取后，它可以从 HTTP 缓存移动至渲染器的内存缓存中。如果资源可以被缓存（比如说存在有效的[cache-control]() 和 max-age），它被存储在 HTTP 缓存中可以被**现在或将来的任务使用**，如果资源不能被缓存在 HTTP 缓存中，作为代替，它被放在内存缓存中直到被使用。

 preload 还有哪些更广泛的应用？

**根据 HTTPArchive，很多网站应用 <link rel=”preload”> 来加载字体，包括 Teen Vogue 和以上提到的其他网站：**

