Gist-js-background-image-onload



```javascript

// Test the background-image when it download
//  当背景图加载完 xxx
// 比如整体显示一个 icon 之类，可以先把整体隐藏起来，图片下载完了再展示

const element = document.getElementById('span.transparent')
let bgImg = new Image()
bgImg.onload = () => {
  element.setAttribute('style', `background-image: url(${imgLocation});`)
  element.classList.remove('transparent')
}
bgImg.src = imgLocation

```

```scss
// TODO: 如何解决 retina 2x 图问题 
// 使用 ES6 模板替换然后直接 elem.setAttribute('style', bgImgSetTmpl) ?
// or 使用 mediaQuery 检查当前客户端分辨率 ratio，>=2 则使用 retina 图

@mixin bg_img_set($url_1x, $url_2x) {
  background: url($url_1x) no-repeat;
  background: -webkit-image-set(url($url_1x) 1x, url($url_2x) 2x) no-repeat;
  background: -moz-image-set(url($url_1x) 1x, url($url_2x) 2x) no-repeat;
  background: -ms-image-set(url($url_1x) 1x, url($url_2x) 2x) no-repeat;
  background: -o-image-set(url($url_1x) 1x, url($url_2x) 2x) no-repeat;
}
```

