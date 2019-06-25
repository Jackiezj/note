# 前端文件开发服务器

> node.js 提供的服务器live-server作为前端开发服务器使用

- 安装`node.js`的版本控制工具`nvm`

  ```bash
  curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
  ```

- 使用`nvm`安装最新版本的`node.js`

  ```bash
  nvm install node
  ```

- 安装`live-serverr`

  ```bash
  npm install -g live-server
  ```

- 使用

  ```bash
  # 在静态文件目录front_end_pc下执行
  live-server
  ```

  > live-server运行在8080端口下，可以通过`127.0.0.1:8080`来访问静态页面。