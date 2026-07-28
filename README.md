# 豆包输入法开放日体验页

本仓库包含豆包输入法开放日体验页的 PC 版和移动端 H5 版。项目为纯静态网页，不依赖 Node.js、数据库或后端接口，可部署到对象存储静态托管、Nginx、容器服务或 Kubernetes。

## 页面入口

- PC 版：`/`
- 移动端 H5：`/mobile/`
- 容器健康检查：`/healthz`

## 目录结构

```text
.
├── index.html                 # PC 页面
├── mobile/
│   └── index.html             # 移动端 H5 页面
├── doubao-input-icon.png      # 公共图标资源
├── Dockerfile                 # 容器镜像构建文件
├── nginx.conf                 # Nginx 配置
└── .dockerignore
```

## 本地预览

安装 Python 3 后，在仓库根目录执行：

```bash
python3 -m http.server 8080
```

访问：

- PC：`http://localhost:8080/`
- H5：`http://localhost:8080/mobile/`

## Docker 部署

构建镜像：

```bash
docker build -t doubao-input-open-day:latest .
```

启动容器：

```bash
docker run --rm -p 8080:80 doubao-input-open-day:latest
```

验证：

```bash
curl http://localhost:8080/healthz
```

返回 `ok` 即表示服务正常。

## 字节云部署建议

### 容器服务

1. 将本仓库导入内部代码仓库。
2. 使用根目录 `Dockerfile` 构建镜像。
3. 容器监听端口设置为 `80`。
4. HTTP 健康检查路径设置为 `/healthz`。
5. 为服务绑定域名并开启 HTTPS。
6. PC 使用根路径 `/`，H5 使用 `/mobile/`。

### 对象存储静态托管

将以下文件按原目录结构上传到存储桶：

```text
index.html
mobile/index.html
doubao-input-icon.png
```

静态网站首页设置为 `index.html`。需要确保 `/mobile/` 能返回 `mobile/index.html`；如果平台不自动识别目录首页，可配置 CDN URL 重写：

```text
/mobile/  ->  /mobile/index.html
```

### 已有 Nginx 服务

也可以直接将静态文件复制到现有站点目录：

```bash
cp index.html /usr/share/nginx/html/
cp doubao-input-icon.png /usr/share/nginx/html/
cp -R mobile /usr/share/nginx/html/
```

随后重载 Nginx。

## 数据与隐私

- 输入内容仅通过浏览器 `localStorage` 保存在参与者当前设备中。
- 页面不会向服务器上传输入内容。
- 点击“重新体验”会清除当前页面对应的本地输入记录。
- PC 与 H5 使用不同的本地存储键，互不影响。

## 浏览器要求

建议使用最新版 Chrome、Edge、Safari 或移动端系统浏览器。页面已适配 13 英寸 MacBook、iPhone 和主流安卓手机。
