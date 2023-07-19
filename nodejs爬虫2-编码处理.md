在爬取html过程中，会出现返回乱码的现象，这里需要区分一下乱码产生的原因。我们使用http模块遇到的两种情况：
1.网站使用了gzip压缩，返回的数据需要先解压缩
2.网站使用了不同的字符编码标准，如UTF-8、GBK、GB2312等，需要做一些编码转换的工作。

## 解压缩
针对1的情况，在请求头中添加`Accept-Encoding`字段，gzip, deflate, br代表3种不同的编码
```js
// request 
  headers: {
    'Accept-Encoding': 'gzip, deflate, br'
  }
```


```js
// response
// 这里直接展示解析部分，省略了拼接chunks

const bodyBuffer = Buffer.concat(chunks)
const contentEncoding = response.headers["content-encoding"]
let html = ''
if (contentEncoding.includes('gzip')) {
    html = zlib.gunzipSync(bodyBuffer);
} else if (contentEncoding.includes('deflate')) {
    html = zlib.inflateSync(bodyBuffer);
} else if (contentEncoding.includes('br')) {
    html = zlib.brotliDecompressSync(bodyBuffer);
}
```

## 编码转换

在Node.js中，可以使用iconv-lite库来解决乱码问题。iconv-lite可以在多种字符集编码之间进行转换。它支持许多编码方案，包括UTF-8、GBK、GB2312等。

使用iconv-lite的decode()函数来解码数据。下面是一个示例代码：

```js
const charsets = contentType?.toLowerCase()
if (
charsets.indexOf('charset=gb') > -1 ||
(charsets.indexOf('charset') < 0 && /charset=['"]?gb/i.test(bodyBuffer.toString()))
) {
// 需要依赖第三方模块 iconv-lite 将gbk编码的网站转码
const result = iconv.decode(bodyBuffer, 'gbk');
}
```

总结：

解决乱码问题是Web爬虫和从API获取数据时常见的问题。在Node.js中，我们可以使用zlib处理解压缩问题，iconv-lite库来处理乱码。