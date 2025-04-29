<div align="center">
  <h1>Live Echo Avatar</h1>

  <p>🧑‍🦱 A modern dopamine-style avatar generator 🧑‍🦳</p>

[English](./README.md) | [简体中文](./README-CN.md)

</div>

<img src="./images/social-preview-1.png" alt="website-cover" />

## Preview

[`https://liveecho.hoshinoai.xin`](https://liveecho.hoshinoai.xin)

## Introduction

**This is a vector style avatar generator where you can mix and match different material components to create your personalized avatar.**

Key Features:

- Modern minimalist dopamine-style design
- Visual component configuration panel
- Random avatar generation
- Undo/Redo operations
- Internationalization support
- Batch generation of multiple avatars
- Share to social media platforms
- Responsive design, mobile device support

## Resources

> **Note**  
> Avatar assets are based on [Avatar Illustration System](https://www.figma.com/community/file/829741575478342595) by Micah Lanier. Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

## Development

This project is built with `Vue3` + `Vite`.

```sh
# 1. Clone the project
git clone https://github.com/Calculusai/Liveecho.git

# 2. Install dependencies
npm install

# 3. Run development server
npm run dev
```

## Build & Deploy

```sh
# Build for production
npm run build

# Preview production build
npm run preview
```

## Docker Deployment

```sh
# Clone the code
git clone https://github.com/Calculusai/Liveecho.git

# Docker build
cd Liveecho/
docker build -t liveecho-avatar:latest .

# Start server
docker run -d -p 3000:80 --name liveecho-avatar liveecho-avatar:latest
```

Once the container is running, open your browser and visit:

- http://localhost:3000 (if running locally)
- http://your-server-ip:3000 (if running on a server)

## Contribution

Issues and Pull Requests are welcome!

## License

[MIT](./LICENSE)
