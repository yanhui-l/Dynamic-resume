﻿**本项目源于：[https://github.com/jirengu-inc/animating-resume](https://github.com/jirengu-inc/animating-resume "https://github.com/jirengu-inc/animating-resume")。向作者表示深深的敬意。**

## 使用方法
```bash
git clone https://gitee.com/zhousiwei/anires.git
cd anires
npm install
npm run dev
```

## 部署方法

1. 编辑 **config/index.js**，修改第 10 行的 `assetsPublicPath`，值为 `项目名/public`。如果你没有修改项目名 `anires`，则可跳过此步骤。

2. 编译、上传
```bash
npm run build
git add .
git commit -m "update"
git push
```
3. 开启 **GitHub Pages** 功能，把生成的目标文件`public/*`放在你的web服务器上。

4. 访问地址：[https://your-github-username.github.io/anires/public](https://zhousiwei.gitee.io/anires/index.html "https://zhousiwei.gitee.io/anires/index.html")
