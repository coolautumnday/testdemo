# AI网页生成器 Atoms‑Demo
> 对标 Atoms.dev 简易前端AI代码生成Demo，浏览器单页应用，输入自然语言，AI生成完整可交互HTML网页，实时预览。

## ✨ 项目特性
1. **双模型自动调度**
   - 普通网页生成：`glm‑4‑flash`
   - 绘图/图形绘制请求（关键词：画、图、图标、绘制、图形等）：自动切换 `glm‑4v‑flash`
2. **CSS绘图强制约束**：图形、图标全部使用纯CSS（div+clip‑path/gradient/transform）实现，禁止img、svg标签，不依赖任何外部图片资源
3. **思考过程输出**：AI思考思路在左侧聊天框打字机展示，HTML代码送入编辑器 + iframe沙箱实时预览
4. **安全沙箱预览**：iframe设置`sandbox="allow‑scripts"`，禁止访问父页面localStorage，规避XSS风险
5. **API‑Key本地输入**：不在源码硬编码密钥；打开页面弹窗输入智谱API‑Key，密钥仅保存在浏览器`localStorage`，不会上传服务端
6. **本地持久化存储**
   - 会话聊天记录
   - 生成的HTML代码
   - 三栏面板拖拽布局宽度
7. **交互按钮**
   - `新建会话`：清空对话、代码、预览，完全重置
   - `清空对话`：仅清空聊天历史，**保留当前代码和预览页面**，适合迭代修改网页
   - `清除Key`：清除本地保存的API‑Key，直接跳转回密钥输入弹窗
   - `导出HTML`：将编辑器内代码导出为本地`.html`文件
8. **健壮性处理**
   - 请求超时AbortController防止按钮永久卡死
   - localStorage读写异常捕获，存储损坏不会白屏崩溃
   - HTML解析兜底截断，自动剔除模型多余说明文字，只保留完整HTML片段
   - XSS HTML转义，聊天内容防注入
   - 拖拽面板设置最大/最小宽度，面板不会被挤出视口
   - 打字机异常finally复位，防止发送按钮锁死

## 📦 运行方式
> 本项目为**纯静态单页面应用**，只有一个 `index.html`，无需后端服务。

### 方式1：本地直接打开
1. 将代码保存为 `index.html`
2. 使用浏览器直接打开该文件
> ⚠️ 注意：部分浏览器本地文件模式会存在CORS跨域限制，智谱API请求会失败。**推荐使用部署方式**。

### 方式2：线上部署（推荐，笔试交付）
支持 Netlify / Vercel / GitHub Pages / DevFile 等静态网页托管平台。
1. 将 `index.html` 上传托管平台，获取公开HTTPS访问链接。
2. 浏览器打开部署链接。
3. 输入你的**智谱开放平台API Key**，验证通过进入主界面。

> 笔试交付物清单：
> 1. 在线Demo访问链接
> 2. GitHub源码仓库链接（存放index.html）
> 3. 项目说明文档（本README）

## 🚀 使用流程
1. 打开页面，弹窗输入智谱API Key，点击`验证并进入应用`，验证成功进入主界面。
2. 在左下角输入框输入你的需求：
   - 示例1：`做一个可增删的待办清单网页`
   - 示例2：`画一个彩虹太阳，使用CSS绘制`
3. 点击【发送，让AI生成应用】
4. 左侧聊天框展示AI思考过程；中间代码编辑器出现完整HTML；右侧iframe实时渲染可交互网页预览。
5. 可以继续输入指令迭代修改当前页面，例如：`把按钮改成蓝色，增大字体`。
6. 点击`导出HTML`，下载生成的网页文件到本地。

## ⚠️ 重要注意事项
1. **CORS跨域问题**：本Demo是浏览器前端直接请求智谱大模型接口。部分托管环境会触发浏览器跨域CORS报错，导致生成功能失效。
   > 生产环境解决方案：需要搭建简易后端代理，转发LLM请求，避免浏览器直接调用智谱API。本Demo为笔试前端演示，未实现后端代理。
2. **密钥安全**：API‑Key仅存储在浏览器本地localStorage，源码中没有硬编码密钥。不要把你的密钥提交到github公开仓库。
3. **模型限制**：虽然设置了严格System Prompt约束输出格式，大模型偶尔会不遵守格式；代码内置解析截断兜底，尽量保证HTML可用。
4. **存储上限**：浏览器localStorage容量有限，不要生成体积超大HTML代码，避免存储超限。

## 📁 项目文件
└── index.html        # 全部代码，单文件应用
## 🛠️ 技术栈
- HTML + CSS + JavaScript（原生JS）
- Tailwind CSS CDN 样式
- Font‑Awesome 图标
- 智谱开放平台API：`glm‑4‑flash` / `glm‑4v‑flash`
- localStorage：本地会话、布局、密钥持久化
- iframe sandbox：网页隔离预览

## 🔧 核心配置（代码内常量区）
```js
const LLM_BASE_URL = "https://open.bigmodel.cn/api/paas/v4/chat/completions";
const MODEL_CODE = "glm-4-flash";      // 普通网页生成模型
const MODEL_DRAW = "glm-4v-flash";     // 绘图生成模型
const LLM_TEMPERATURE = 0.7;
const LLM_TIMEOUT_MS = 60000;
