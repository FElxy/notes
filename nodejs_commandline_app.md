# 总结
bin命令
参数设置 commander
环境 dotenv
输入 inquirer | prompts
输出 figlet chalk cli-progress

# Enviroment Variables
process.env
As per the definition from its package, Dotenv is a zero-dependency module that loads environment variables from a .env file into process.env. Storing configuration in the environment separate from code is based on The Twelve-Factor App methodology. 
```js
// npm install dotenv --save
import 'dotenv/config'
// require('dotenv').config()
```

Common examples of configuration data that are stored in environment variables include:

HTTP port
database connection string
location of static files
endpoints of external services

The .env file should never be committed to the source code repository. We must place the file into the .gitignore file. (When using git.)

```js
require('dotenv').config({ path: `.env.${process.env.NODE_ENV}` });
```

cross-env
NODE_ENV=production node app.js

在 Windows 中，你需要使用 set 命令来设置环境变量，然后启动 Node.js 应用程序：
set NODE_ENV=production
node app.js

# Input
The process.stdin is a standard Readable stream which listens for user input and is accessible via the process module. It uses on() function to listen for input events.

inquirer.js 用来创建可交互的命令行
输入
```js
const inquirer = require('inquirer');

inquirer
  .prompt([
    {
      name: 'faveReptile',
      message: 'What is your favorite reptile?',
      default: 'Alligators'
    },
  ])
  .then(answers => {
    console.info('Answer:', answers.faveReptile);
  });
```
```
Output
? What is your favorite reptile? (Alligators)
```

type: [list, rawlist, expand, checkbox, password, editor]

选项
```js
const inquirer = require('inquirer');

inquirer
  .prompt([
    {
      type: 'list',
      name: 'reptile',
      message: 'Which is better?',
      choices: ['alligator', 'crocodile'],
    },
  ])
  .then(answers => {
    console.info('Answer:', answers.reptile);
  });
```
```
Output
? Which is better? (Use arrow keys)
❯ alligator
  crocodile
```
> https://www.digitalocean.com/community/tutorials/nodejs-interactive-command-line-prompts

prompts 另一个交互工具
> https://www.npmjs.com/package/prompts


# Output
console.log() calls process.stdout.write with formatted output. See format() in console.js for the implementation.
chalk
Modifiers: bold italic underline...
Colors: black red yellowBright...
Background colors: bgBlack bgYellow ...
> https://github.com/chalk/chalk#readme

figlet 创建文字字符画
> https://github.com/patorjk/figlet.js

cli-progress 创建进度条
> https://www.npmjs.com/package/cli-progress
# Command line args

---

1. Build Your First Node.js Command Line Application

```js
#!/usr/bin/env node
console.log( "Hello!" );
```
package.json 添加命令

The first line that begins with #! is usually called a “shebang.” This is normally only used on Linux or UNIX operating systems to inform the system what type of script is included in the rest of the text file. However, this first line is also required for Node.js scripts to be installed and run properly on macOS and Windows.
作用：
自动识别：无论 node 安装在哪里，env 会找到它的路径。这使得你的脚本在不同的环境中更具可移植性。
可执行文件：当你将脚本文件标记为可执行（使用 chmod +x index.js），你可以直接在终端中运行这个文件，而不需要显式调用 node。
这样就可以执行./index.js -n YourName，而不需要显示的node index.js -n YourName

> Tip: You can list all globally installed Node.js modules using npm ls -g --depth=0.

```js
 "bin": {
   "hello": "./bin/index.js"
 }
```

2. Make Text Stand Out with Color and Borders

To modify the color of text and background color, you can use chalk. To add borders around your text to make it more visible, you can use a module named boxen.

chalk改变文字颜色和背景色，boxen增加border
```js
#!/usr/bin/env node

const chalk = require("chalk");
const boxen = require("boxen");

const greeting = chalk.white.bold("Hello!");

const boxenOptions = {
 padding: 1,
 margin: 1,
 borderStyle: "round",
 borderColor: "green",
 backgroundColor: "#555555"
};
const msgBox = boxen( greeting, boxenOptions );

console.log(msgBox);
```

3. Add Support for Command Line Arguments

Although you can parse command line parameters by inspecting the Node.js process.argv value, there are modules available that will save you a lot of time and effort. 

使用yargs
```js
#!/usr/bin/env node

const yargs = require("yargs");

const options = yargs
 .usage("Usage: -n <name>")
 .option("n", { alias: "name", describe: "Your name", type: "string", demandOption: true })
 .argv;

const greeting = `Hello, ${options.name}!`;

console.log(greeting);
```

使用commander
```js
const { Command } = require('commander');
const chalk = require('chalk');

const program = new Command();

program
  .version('1.0.0')
  .description('A simple CLI app')
  .option('-n, --name <type>', 'your name');

program.parse(process.argv);

const options = program.opts();

if (options.name) {
  console.log(chalk.green(`Hello, ${options.name}!`));
} else {
  console.log(chalk.red('Please provide a name with -n option.'));
}

```

https://developer.okta.com/blog/2019/06/18/command-line-app-with-nodejs