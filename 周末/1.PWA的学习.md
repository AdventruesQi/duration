# 1.PWA简介：

### 一、PWA简介：

progressive web app间接式网络应用

PWA技术组成🧩：

- Service Worker🥁：服务工作线程
  - 常驻内存运行
  - 代理网络请求
  - 依赖https
- Promise：“承诺”控制流
  - 优化回调地狱
  - async/await语法同步化
  - service worker的API风格
- fetch🎰：网络请求
  - 比xmlhttpreuqest更加简洁
  - Promise风格
  - 依旧内存不足，不能够完全替代xml...
- cache API🍭:支持资源的缓存系统
  - 缓存资源（css/image/script）
  - 依赖service worker代理网络请求
  - 支持离线程序运行
- Notification APi：消息推送
  - 依赖用户授权
  - 适合在service worker中推送

