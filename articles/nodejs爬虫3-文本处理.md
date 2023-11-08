根据爬取业务需求的不同，对爬虫获取到的文本做针对性的处理

以一个内容网站举例，我们的目标是获取到这个网站的信息，当前爬取获得的结果是网站的html文件，因此需要做一些处理工作。

# 使用jsdom获取指定内容
获取指定内容，也就是通过一些查找dom元素的方法找到文本、链接之类的，举例来说
```html
<a href="https://xxx.abc.com" class="link">link</a>
```
这样的一个链接，如果想要获取链接地址可以通过类名查找
```js
const link = document.querySelector('a.link').href
```

但是由于我们是通过http的方式获取的文本文档，因此需要使用[jsdom](https://github.com/jsdom/jsdom)来获取dom

```js
const jsdom = require("jsdom");
const { JSDOM } = jsdom;
const { document } = (new JSDOM(htmlContent)).window;
```
有了document后，就可以随心所欲的提取内容了

另外，jsdom转换的dom可以使用很多dom的查找方法，但是有些要注意，比如转换后的节点没有innerText方法，需要使用textContent方法替代

innerText和textContent的区别
| 属性           | `innerText`                                 | `textContent`                            |
|----------------|--------------------------------------------|----------------------------------------|
| 是否标准属性     | 非标准属性                                   | 标准属性                                |
| 返回内容         | 元素及其后代元素的可见文本内容                 | 元素及其后代元素的所有文本内容            |
| 是否考虑CSS样式 | 考虑CSS样式，只返回可见文本内容                | 不考虑CSS样式，返回所有文本内容           |
| 包括隐藏元素的文本 | 不包括隐藏元素的文本                         | 包括隐藏元素的文本                      |
| 包括注释和内部的   | 不包括注释和内部的`<script>`或`<style>`元素   | 包括注释和内部的`<script>`或`<style>`元素  |
| `<script>`或`<style>` |                                           |                                        |


# 使用xpath匹配内容
另一种情况，我们想要做一个通用的内容解析，不同网站使用的标签、类名都不一样，但是都有相同的文字，比如“下一页”，这里可以使用xpath做匹配

```js
const btnBase = document.evaluate(".//a[contains(text(),'下一页')] | .//span[contains(text(), '下一页')]", document.body, null, 0, null)
const btn = btnBase.iterateNext()
if (btn) {
    const href = btn.href || btn?.dataset['href'] // 获取到了btn的链接
}

```
xpath写法有很多，可以查询文档。因为jsdom里面evaluate解析的xpath版本比较低，没法用正则匹配的方式，因此这里只能是并联的列举可能的情况

# 使用readability提取正文
有时我们只要提取正文内容，可以借助三方工具[readability](https://github.com/mozilla/readability)提取正文
因为readability是接收dom作为参数的，所以在拿到html文本后，同样需要用jsdom做转换

```js
var { Readability } = require('@mozilla/readability');
var { JSDOM } = require('jsdom');
var doc = new JSDOM("<body>Look at this cat: <img src='./cat.jpg'></body>", {
  url: "https://www.example.com/the-page-i-got-the-source-from"
});
let reader = new Readability(doc.window.document);
let article = reader.parse();
```

readability会根据自己的权重计算出哪些属于正文内容，哪些需要剪枝掉的内容。

另外，readability对传入的dom直接进行操作，因此如果想不改变原dom的情况下，应该克隆一份
```js
let domClone = dom.cloneNode(true);
let reader = new Readability(domClone);
// ...
```

以上就是在爬取后可以做的文本处理工作，具体还是需要根据业务需求进行调整