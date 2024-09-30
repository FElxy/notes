万能文档需求，CAD，xmind文件打开都是接入三方厂商的SDK
而Apple iworks需要自己转换成pdf文件后查看，这里需要有Mac的虚拟机支持，脚本使用Apple script

完整代码
```js
const path = require('path')
var spawn = require("child_process").spawn;
const fs = require('fs');

const applescriptCommand = `on run {input}
    log input
    -- 使用逗号作为分隔符
    set AppleScript's text item delimiters to ","
    set inputArray to text items of input
    set AppleScript's text item delimiters to {""}
    log inputArray
    repeat with theFile in inputArray
    set logMessage to "我的日志：" & theFile
    log logMessage
    log (theFile & ".key")
      tell application "Keynote"
          set theDoc to open (theFile & ".key")
          set theDocName to name of theDoc
          set thePDFPath to (theFile & ".pdf")
          log thePDFPath
          export theDoc to file thePDFPath as PDF
          close theDoc
      end tell

      delay 0.1
    end repeat
end run

`;
const inputFilePaths = path.join(__dirname, 'files/keynote例子.key');
const outputFilePaths = path.join(__dirname, 'outputs/keynote例子.pdf');

function changePathToMacPath(pathString) {
  const appleScriptPath = pathString.replace(/\//g, ':');
  const ret = `Macintosh HD${appleScriptPath}`
  console.log(ret)
  return ret
}

function removeSuffix(fileName) {
  return fileName.replace('.key', '')
}

const list = fs.readdirSync('./files');
const filePaths = list.filter((item) => item.endsWith('.key'))
                      .map((item) => {
                        let filePath = path.join(__dirname, 'files', item)
                        filePath = changePathToMacPath(filePath)
                        return removeSuffix(filePath)
                      })
console.log(filePaths)


// 使用 spawn 函数执行 AppleScript 命令，并传递参数
const applescript = spawn('osascript', ['-e', applescriptCommand, filePaths]);

applescript.stdout.on('data', (data) => {
  console.log(`Result: ${data.toString()}`);
});

applescript.stderr.on('data', (data) => {
  console.error(`Log: ${data.toString()}`);
});

```

---
具体分析

首先是找到的一段apple script，主要是输入一段路径字符串数组，拉起keynote执行转换pdf功能
```js
const applescriptCommand = `
on run {input}
    log input
    -- 使用逗号作为分隔符
    set AppleScript's text item delimiters to ","
    set inputArray to text items of input
    set AppleScript's text item delimiters to {""}
    log inputArray
    repeat with theFile in inputArray
    set logMessage to "我的日志：" & theFile
    log logMessage
    log (theFile & ".key")
      tell application "Keynote"
          set theDoc to open (theFile & ".key")
          set theDocName to name of theDoc
          set thePDFPath to (theFile & ".pdf")
          log thePDFPath
          export theDoc to file thePDFPath as PDF
          close theDoc
      end tell

      delay 0.1
    end repeat
end run`;
```

两个工具函数用来修改路径和移除后缀
```js
function changePathToMacPath(pathString) {
  const appleScriptPath = pathString.replace(/\//g, ':');
  const ret = `Macintosh HD${appleScriptPath}`
  console.log(ret)
  return ret
}

function removeSuffix(fileName) {
  return fileName.replace('.key', '')
}

```

读取指定目录下的文件，并转换路径
```js
const list = fs.readdirSync('./files');
const filePaths = list.filter((item) => item.endsWith('.key')).map((item) => {
                        let filePath = path.join(__dirname, 'files', item)
                        filePath = changePathToMacPath(filePath)
                        return removeSuffix(filePath)
                      })
```

执行script
```js
// 使用 spawn 函数执行 AppleScript 命令，并传递参数
const applescript = spawn('osascript', ['-e', applescriptCommand, filePaths]);

applescript.stdout.on('data', (data) => {
  console.log(`Result: ${data.toString()}`);
});

applescript.stderr.on('data', (data) => {
  console.error(`Log: ${data.toString()}`);
});
```

