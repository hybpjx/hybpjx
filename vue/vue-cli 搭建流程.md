# vue-cli 搭建流程

## 1. 下载node

下载玩node.js 然后再cmd中输入npm检测



## 2. npm换源

1.临时使用
 `npm --registry https://registry.npm.taobao.org install express`

2.持久使用

>   `npm config set registry https://registry.npm.taobao.org`
>    // 配置后可通过下面方式来验证是否成功
>    `npm config get registry`
>    // 或npm info express

3.通过cnpm
 使用
 `npm install -g cnpm --registry=https://registry.npm.taobao.org`
 // 使用cnpm install expresstall express



## 3. 下载vue-cli

npm install -g vue-cli



验证：vue-v



## 4. 创建一个脚手架



vue init webpack vueproject

回车确定使用 vueproject 这个名字

回车确定描述

输入自己的名字 然后回车



使用路由 yes

其他n

使用npm

