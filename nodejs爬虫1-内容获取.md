对于爬虫我实际上了解不多，正好有一个项目需要从目标网站上爬取正文。那么我们主要就关注到获取内容这部分，该如何去实现这个需求。

最简单的方式，发送一个请求到目标网站，获取到网站返回的html，再解析它就完成了。作为一名前端来说，还是nodejs相对更熟悉一些，所以技术选型就以nodejs为核心了。

# 发送http请求
在发送请求这里，大多数的爬虫文章都建议安装`request`库，但这个库已经是不维护状态了https://github.com/request/request，当然不是因为这个库不好，具体原因在[这里](https://github.com/request/request/issues/3142)。也可以选择用`axios`，主要目的就是发送`GET`请求。

我在这里直接用了nodejs原生的请求模块`http`。
为了能用的更舒服一些，对`http`模块做一点改造：
```js
async function request(url, headers = {}) {
  return new Promise((resolve, reject) => {
    const lib = url.startsWith('https') ? https : http;
    let options = {
      rejectUnauthorized: false, // 解决一些有证书问题网站无法爬取内容
      timeout: 6000,
      headers: {
        'User-Agent': 'Mozilla/5.0 (Linux; Android 13; ) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/87.0.4280.141 Mobile Safari/537.36',
        ...headers
      },
    }

    const callback = (response) => {
      if (response.statusCode < 200 || response.statusCode > 299) {
        const e = new HttpError('Failed to load page, status code: ' + response.statusCode, response.statusCode)
        reject(e)
      }

      const chunks = [];

      response.on('data', (chunk) => chunks.push(chunk));
      response.on('end', () => {
        const bodyBuffer = Buffer.concat(chunks)
        resolve({ content: bodyBuffer.toString() })
      });
    }

    request = lib.get(url, options, callback);

    request.on('error', (err) => {
      reject(new HttpError(err, -1))
    })
  })
}
```
这里封装了一个最简单的request请求函数，主要功能是可以使用promise发送请求。拿到的结果就是目标网站的html了，接下来就可以进行文档的解析。

# 解析html文档
解析这里同样也有一些选择，有[cheerio](https://github.com/cheeriojs/cheerio), [jsdom](https://github.com/jsdom/jsdom)等，cheerio可以使用类jquery的语法，jsdom可以使用原生语法。

我在这里采用了jsdom，主要是在下一步提取目标内容中需要用到一个内容解析库。jsdom使用方法可以参考官方文档：
```js
const { JSDOM } = require("jsdom");

function getDocument (content) {
    const html = content
    const window = new JSDOM(html).window
    const document = window.document
    return document
}
```
拿到document之后，那一些dom操作方法也都可以使用了，但是也会遇到一些方法是undefined，这个后面再说。

# 获取目标内容
网站的内容形式各异，有时我们只需要提取正文内容，对于导航菜单、或者广告这些内容都不需要的，识别内容确实比较麻烦，但是这里有个Readability三方库为我们省了很多事。
```js
const { Readability } = require("@mozilla/readability");
//...
const document = getDocument(content)
let documentClone = document.cloneNode(true);
const article = new Readability(documentClone).parse();
//...
```

如果是基本提取的话，那经过了上面的步骤就可以拿到过滤后的正文内容了，但是实际上还有各种坑要填，后面一步步来改进。