### Gist-js-global_err_debug

```javascript
window.onerror = function (errMsg, url, lineNumber, column, errObj) {
  alert('Error: ' + errMsg + ' Script: ' + url + ' Line: ' + 		  lineNumber + ' Column' + column + ' StackTrace: ' + errObj)
}
```



https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onerror

