### What about `flex`？

### Be `flex`

---

```html
<h3 class="title flex-title">
  <span class="title-main">Main Title Here</span>
  <span class="title-note">This is a good look, right here.</span>
</h3>
```

```scss
.flex-title {
  display: flex;
  align-items: flex-end;
  flex-wrap: wrap;
  > span {
    white-space: nowrap;
  }
  .title-main {
    flex-grow: 1;
  }
}
```

