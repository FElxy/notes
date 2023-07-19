# 服务器连接
`ssh root@xx.xxx.xxx.xx`

# 下载nodejs包
如果在线不能下载，自己下载然后上传

https://nodejs.org/zh-cn/download

解压二进制包
`tar -xvf   nodejs.tar.xz`

改名
`mv node-v10.16.3-linux-x64  nodejs `


配置软链
`ln -s /download/nodejs/bin/npm /usr/local/bin/ `

`ln -s /download/nodejs/bin/node /usr/local/bin/`

检查是否成功
`node -v`

`npm -v`

移除软链使用`unlink`


# 安装pm2
`npm i pm2@latest -g`

`ln -s /download/nodejs/bin/pm2 /usr/local/bin/`

