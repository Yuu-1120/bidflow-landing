# 智标宝 BidFlow AI

面向客户与大众展示的招投标全流程 AI 聚合介绍页。

## 技术栈

```text
Vue 3
Vite
TypeScript
CSS Scroll Snap
IntersectionObserver
```

## 本地运行

```bash
npm install
npm run dev
```

默认开发端口：`5177`。

## 环境变量

复制 `.env.example` 为 `.env.local` 后配置：

```text
VITE_TENDER_LOGIN_URL=招采平台登录地址
VITE_BID_LOGIN_URL=投标平台登录地址
VITE_CONTACT_URL=联系我们地址，可为空
VITE_DEMO_URL=预约演示地址，可为空
VITE_FEEDBACK_URL=客户反馈表单地址，可为空
VITE_HERO_VIDEO_URL=首屏视频地址，可为空
VITE_HERO_POSTER_URL=首屏视频封面地址，可为空
```

如果联系或预约地址为空，页面会打开内置联系弹层。
如果客户反馈地址为空，页面会打开内置反馈弹层。

## 素材替换

可将正式图片或视频放入 `public/media`，页面会优先使用配置中的视觉素材；未提供时展示内置占位视觉。
