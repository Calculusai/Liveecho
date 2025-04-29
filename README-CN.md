<div align="center">
  <h1>Live Echo Avatar</h1>

  <p>🧑‍🦱 一个多巴胺风格的现代化头像生成器 🧑‍🦳</p>

[English](./README.md) | [简体中文](./README-CN.md)

</div>

<img src="./images/social-preview-1.png" alt="website-cover" />

## 在线预览

[`https://liveecho.hoshinoai.xin`](https://liveecho.hoshinoai.xin)

## 介绍

**这是一款矢量风格头像的生成器，你可以搭配不同的素材组件，生成自己的个性化头像。**

特色功能：

- 现代简约多巴胺设计风格
- 可视化组件配置面板
- 随机生成头像，有一定概率触发彩蛋
- 撤销/还原操作
- 国际化多语言
- 批量生成多个头像
- 一键分享到社交媒体
- 响应式设计，支持移动设备

## 设计资源

> **注意**  
> 头像素材基于 Micah Lanier 的 [Avatar Illustration System](https://www.figma.com/community/file/829741575478342595) 实现。根据 [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) 许可使用。

## 项目开发

该项目使用 `Vue3` + `Vite` 进行开发。

```sh
# 1. 克隆项目至本地
git clone https://github.com/Calculusai/Liveecho.git

# 2. 安装项目依赖
npm install

# 3. 运行项目
npm run dev
```

## 构建部署

```sh
# 构建生产版本
npm run build

# 预览生产构建
npm run preview
```

## Docker 快速部署

```sh
# 下载代码
git clone https://github.com/Calculusai/Liveecho.git

# docker 编译
cd Liveecho/
docker build -t liveecho-avatar:latest .

# 启动服务
docker run -d -p 3000:80 --name liveecho-avatar liveecho-avatar:latest
```

容器运行后，打开浏览器访问：

- http://localhost:3000 (本地运行)
- http://your-server-ip:3000 (服务器运行)

## 贡献

欢迎提交 Issue 或 Pull Request!

## 许可证

[MIT](./LICENSE)
