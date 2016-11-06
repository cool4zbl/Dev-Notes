#### Chrome Select Option bug

back & forward

doesn't match the actual option attr `selected`


#### 后退

- Safari 会使用之前缓存的 HTML / CSS
- Chrome 有缓存



#### 前进

- Safari 会刷新


---
*Note*:
回退时候，
- Chrome 对于 HTML & CSS 均有缓存，但辛亏 JS 是重新运行一遍，所以手动更新下 select 的值好了。
- Safari 下，因为这个跳转是 JS 跳转，后退的时候发现直接回到了空白页，手动加下 Safari 下的 state 值。
