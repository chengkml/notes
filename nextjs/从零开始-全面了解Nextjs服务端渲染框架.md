# 从零开始：全面了解 Next.js 服务端渲染框架

> Source: [从零开始：全面了解 Next.js 服务端渲染框架](https://juejin.cn/post/7540858814171381823)

## 引言：为什么选择 Next.js？

在现代 Web 开发的浪潮中，React 已经成为构建用户界面的事实标准。然而，随着应用复杂度的提升，单页应用（SPA）的局限性逐渐显现——尤其是首屏加载慢、SEO 不友好、用户体验不佳等问题。为了解决这些问题，服务端渲染（SSR）技术应运而生。

而 Next.js，作为 React 生态中最受欢迎的服务端渲染框架之一，由 Vercel（原 Zeit）团队开发并维护，凭借其开箱即用的 SSR 支持、静态生成（SSG）、API 路由、文件系统路由、TypeScript 集成、图像优化、国际化路由等强大功能，迅速成为企业级应用和内容型网站的首选框架。

本篇博客将带你从零开始，深入理解 Next.js 的核心概念、项目搭建、路由机制、数据获取方式、渲染模式（CSR vs SSR vs RSC vs SSG）、开发技巧与最佳实践。

## 第一章：环境准备与项目初始化

### 1.1 安装 Node.js 与 npm

Next.js 是基于 Node.js 构建的，因此首先需要确保你的开发环境中已安装了 Node.js 和 npm（Node Package Manager）。

你可以通过以下命令检查是否已安装：

```bash
node -v
npm -v
```

推荐使用 Node.js 18.x 或更高版本（LTS 版本更佳），以获得更好的兼容性和性能支持。

如果你尚未安装，请前往 [nodejs.org](https://nodejs.org) 下载并安装。

### 1.2 使用 npx 创建 Next.js 项目

在现代前端开发中，我们经常使用 npx 来快速创建项目，而无需全局安装 CLI 工具。这正是你在笔记中提到的关键点：

```bash
npx create-next-app@latest my-todo
```

让我们逐行解析这条命令的含义：

#### ✅ npx 是什么？

npx 是 npm 5.2+ 引入的一个工具，用于执行 npm 包中的可执行文件，而无需先将其全局安装。

它的优势包括：

- 无需全局安装：避免污染全局环境。
- 自动下载并运行：如果本地没有该包，npx 会临时下载并执行。
- 适合测试新技术：尝试新框架或工具时非常方便，用完即走。
- 版本控制灵活：可以直接指定版本，如 @latest 或 @14.2.0。

💡 对比：

```bash
# 全局安装 create-next-app（不推荐频繁使用）
npm install -g create-next-app@latest

# 之后才能运行
create-next-app my-todo
```

而使用 npx，一步到位，干净利落。

#### ✅ create-next-app@latest

这是 Next.js 官方提供的脚手架工具，用于快速生成一个标准的 Next.js 项目模板。

- create-next-app：创建 Next.js 应用的 CLI 工具。
- @latest：表示使用最新稳定版本（也可指定具体版本号，如 @13.5.6）。

#### ✅ my-todo

这是你项目的名称，npx 会根据这个名字创建一个同名目录，并在其中初始化项目结构。

### 1.3 初始化过程详解

运行上述命令后，终端会提示你进行一系列配置选项：

```
✔ What is your project named? … my-todo
✔ Would you like to use TypeScript? … No / Yes
✔ Would you like to use ESLint? … No / Yes
✔ Would you like to use Tailwind CSS? … No / Yes
✔ Would you like to use `src/` directory? … No / Yes
✔ Would you like to use App Router? (recommended) … No / Yes
✔ Would you like to customize the default import alias (@/*)? … No / Yes
```

我们来逐一解释这些选项：

| 选项 | 推荐选择 | 说明 |
| :--- | :--- | :--- |
| TypeScript | ✅ Yes | 强类型语言，提升代码质量和可维护性 |
| ESLint | ✅ Yes | 代码规范检查工具，团队协作必备 |
| Tailwind CSS | ✅ Yes | 实用优先的 CSS 框架，提升开发效率 |
| Use src/ directory? | ❌ No（或 ✅ Yes） | 是否使用 src 目录组织源码 |
| App Router (recommended) | ✅ Yes | 使用最新的 App Router 路由系统（Next.js 13+ 推荐） |
| Import Alias | ✅ Yes（如 @/*） | 自定义模块导入别名，简化路径引用 |

⚠️ 注意：从 Next.js 13 开始，官方推荐使用 App Router 而非旧的 Pages Router。App Router 基于 React Server Components 和文件系统路由，提供了更现代化的开发体验。

假设你选择了以下配置：

- 项目名：my-todo
- TypeScript：Yes
- ESLint：Yes
- Tailwind CSS：Yes
- src/ directory：No
- App Router：Yes
- Import Alias：Yes（@/*）

那么最终生成的项目结构如下：

```
my-todo/
├── app/                  # App Router 核心目录
│   ├── layout.tsx        # 根布局组件
│   ├── page.tsx          # 首页页面
│   └── globals.css       # 全局样式
├── public/               # 静态资源（图片、字体等）
│   ├── vercel.svg
│   └── next.svg
├── .gitignore
├── next.config.js        # Next.js 配置文件
├── package.json
├── tsconfig.json         # TypeScript 配置
└── tailwind.config.js    # Tailwind CSS 配置
```

🔍 提示：如果你选择使用 src/ 目录，则 app/ 会位于 src/app/ 下。

### 1.4 启动开发服务器

进入项目目录并启动开发服务器：

```bash
cd my-todo
npm run dev
```

默认情况下，Next.js 会在 http://localhost:3000 启动开发服务器，并启用热重载（Hot Reload）功能。

打开浏览器访问该地址，你应该能看到 Next.js 的欢迎页面，说明项目已成功启动！

## 第二章：Next.js 核心概念解析

### 2.1 CSR vs SSR vs RSC：前端渲染的演进

在深入 Next.js 之前，我们必须理解现代前端应用的几种主要渲染范式。从客户端渲染（CSR）到服务端渲染（SSR），再到革命性的 React Server Components（RSC），这是一场持续优化用户体验和性能的进化。

#### 🔹 客户端渲染（Client-Side Rendering, CSR）

这是传统 React SPA（单页应用）采用的方式。

工作流程：

- 用户请求页面 → 服务器返回一个空的 HTML 文件（通常只有一个 `<div id="root"></div>`）。
- 浏览器下载 JavaScript 文件。
- React 在浏览器中执行，通过 JavaScript 动态生成 DOM 并挂载到页面。
- 发起 API 请求获取数据，更新 UI。

优点：

- 交互性强，用户体验接近原生应用。
- 前后端分离，便于维护。

缺点：

- 首屏加载慢：必须等待 JS 下载、解析、执行后才能看到内容。
- SEO 不友好：搜索引擎爬虫抓取的是初始 HTML，而初始 HTML 是空的，无法获取有效内容。
- CLS（Cumulative Layout Shift）问题：页面先显示空白，再突然出现内容，影响用户体验。

📌 典型场景：后台管理系统、内部工具、对 SEO 无要求的应用。

#### 🔹 服务端渲染（Server-Side Rendering, SSR）

Next.js 的核心优势之一就是支持 SSR。

工作流程：

- 用户请求页面 → 服务器接收到请求。
- 服务器运行 React 组件，在服务端完成组件的渲染，生成带有实际内容的完整 HTML。
- 将完整的 HTML 返回给客户端。
- 客户端接收到 HTML 后，立即显示内容（首屏快）。
- 同时下载 JavaScript，进行“注水”（Hydration），使页面具备交互能力。

优点：

- 首屏加载快：用户无需等待 JS 加载即可看到内容。
- SEO 友好：爬虫可以直接抓取到完整的 HTML 内容。
- 更好的用户体验：减少白屏时间，提升感知性能。

缺点：

- 客户端 JS 体积大：所有组件逻辑（包括非交互部分）都需要打包并发送到客户端。
- 水合（Hydration）成本高：浏览器需要为整个页面的 HTML “注入”事件监听器和状态，这个过程可能很耗时，影响 交互时间 (TTI)。
- 数据获取瀑布流：深层嵌套的组件可能需要等待父组件获取数据后才能发起自己的请求。

📌 典型场景：企业官网、博客、新闻站、电商产品页、内容平台（如掘金、知乎）。

#### 🔹 React Server Components (RSC)：范式级革新

RSC 是 React 19 的核心新特性，它不是 SSR 的简单替代，而是对渲染范式的彻底重构。Next.js 率先在生产环境中实现了 RSC，使其成为构建现代 Web 应用的强大工具。

核心思想：将组件分为服务器组件和客户端组件。

- 服务器组件 (Server Components)：
    - 仅在服务器端执行和渲染。
    - 代码（JSX、逻辑、依赖）永远不会发送到客户端。
    - 可以直接访问数据库、文件系统、环境变量、API 密钥等后端资源。
    - 可以是 async 函数，直接使用 await 获取数据。
    - 生成的是一种“UI 描述”（类似 JSON 的结构），通过流式传输到客户端。

- 客户端组件 (Client Components)：
    - 在浏览器中运行，可以使用 useState、useEffect、事件处理等。
    - 需要交互逻辑时使用。
    - 必须通过 'use client' 显式声明。

工作流程 (RSC + SSR)：

- 用户请求页面。
- 服务器开始渲染。服务器组件在服务端执行，获取数据并生成 UI 描述。
- 服务器将预渲染的 HTML（来自服务器组件）和 UI 描述流式传输到客户端。
- 客户端接收到 HTML 后立即显示内容（首屏极快）。
- 客户端同时接收 UI 描述和大幅精简的客户端 JS 包（仅包含客户端组件的代码）。
- 浏览器为需要交互的客户端组件进行部分水合 (Partial Hydration)。

优点 (对比 SSR)：

- ✅ 极致的包体积优化：服务器组件代码不发送到客户端，显著减少 JS 下载量。
- ✅ 更快的交互时间 (TTI)：需要水合的 JS 更少，水合过程更快。
- ✅ 解决数据获取瀑布流：服务器组件可以在组件树深处直接获取数据，无需层层传递。
- ✅ 更细粒度的缓存：可以在组件级别进行缓存。
- ✅ 更安全：敏感逻辑和密钥保留在服务器端。

缺点：

- ❌ 心智模型更复杂：开发者需要明确区分服务器和客户端组件，并理解它们之间的交互规则（如 props 传递的可序列化性）。
- ❌ 生态系统适配：一些依赖浏览器 API 的库（如 window、document）不能在服务器组件中使用。

📌 核心结论：RSC 利用了 SSR 的传输管道，但通过引入服务器组件模型，解决了 SSR 的核心性能瓶颈（大包体积、高水合成本），是构建高性能、SEO 友好应用的终极方案。

### 2.2 静态生成（Static Site Generation, SSG）

除了 SSR，Next.js 还支持 静态生成（SSG），这是一种更高效的预渲染方式。

工作流程：

- 在构建时（build time），Next.js 预先将所有页面渲染为静态 HTML 文件。
- 这些 HTML 文件被部署到 CDN 上。
- 用户请求时，直接从 CDN 返回静态文件，速度极快。

优点：

- 极致性能：无需服务器实时渲染，响应速度最快。
- 成本低：可部署在任何静态托管服务（如 Vercel、Netlify、GitHub Pages）。
- 安全性高：无后端逻辑暴露。

适用场景：

- 博客文章、文档网站、营销页等内容不频繁变动的页面。

💡 Next.js 支持 增量静态再生（ISR）：允许在构建后更新特定页面，结合了 SSG 和 SSR 的优点。

### 2.3 App Router：基于文件系统的路由系统

Next.js 13 引入了全新的 App Router，取代了旧的 Pages Router，带来了更现代化的开发体验。

#### ✅ 核心理念：目录即路由

App Router 的最大特点是：路由由 app 目录下的文件结构决定。

例如：

```
app/
├── page.tsx            → 路由：/
├── about/page.tsx      → 路由：/about
├── blog/page.tsx       → 路由：/blog
└── blog/
    └── [slug]/page.tsx → 路由：/blog/:slug（动态路由）
```

每个路由对应一个 page.tsx 文件，导出的默认组件即为该页面的内容。

#### ✅ 布局（Layout）系统

App Router 提供了内置的布局组件机制，允许你在多个页面之间共享 UI 结构（如导航栏、页脚）。

创建一个 layout.tsx 文件：

```tsx
// app/layout.tsx
import { ReactNode } from 'react';

export default function RootLayout({ children }: { children: ReactNode }) {
  return (
    <html lang="zh-CN">
      <body>
        <header>我的网站</header>
        <main>{children}</main>
        <footer>© 2025 My Todo App</footer>
      </body>
    </html>
  );
}
```

⚠️ 注意：layout.tsx 必须返回 `<html><body>...</body></html>` 结构，这是 App Router 的要求。

你还可以为特定子路径创建嵌套路由：

```
app/
├── layout.tsx          # 根布局
├── dashboard/
│   ├── layout.tsx      # 仅作用于 /dashboard/* 的布局
│   └── page.tsx        # /dashboard 页面
```

嵌套路由的布局会自动包裹子页面。

#### ✅ 加载状态与错误处理

App Router 支持 loading.tsx 和 error.tsx 文件，用于处理异步加载和错误状态。

```tsx
// app/blog/loading.tsx
export default function Loading() {
  return <p>加载中...</p>;
}
```

```tsx
// app/blog/error.tsx
'use client'; // 必须标记为客户端组件

import { useEffect } from 'react';

export default function Error({ error, reset }: { error: Error; reset: () => void }) {
  useEffect(() => {
    console.error(error);
  }, [error]);

  return (
    <div>
      <h2>出错了！</h2>
      <button onClick={() => reset()}>重试</button>
    </div>
  );
}
```

这些文件会自动在对应路由下生效。

## 第三章：深入 App Router 与文件结构

### 3.1 app 目录详解

app 目录是 App Router 的核心，所有路由和布局都基于此目录。

#### ✅ page.tsx：页面组件

每个路由必须有一个 page.tsx 文件，导出的默认组件即为页面内容。

```tsx
// app/page.tsx
export default function HomePage() {
  return <h1>首页</h1>;
}
```

#### ✅ layout.tsx：布局组件

布局组件用于包裹页面内容，支持嵌套。

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex">
      <nav className="w-64 bg-gray-100 p-4">
        <ul>
          <li><a href="/dashboard">概览</a></li>
          <li><a href="/dashboard/tasks">任务</a></li>
        </ul>
      </nav>
      <main className="flex-1 p-6">{children}</main>
    </div>
  );
}
```

🔍 布局组件的特点：

- 会保留状态（state）和 DOM，切换页面时不会重新挂载。
- 支持多个布局嵌套。
- 可以包含客户端组件（使用 'use client' 指令）。

#### ✅ loading.tsx：加载状态

当页面数据尚未加载完成时，显示加载 UI。

```tsx
// app/blog/loading.tsx
export default function BlogLoading() {
  return <p className="text-blue-500">正在加载博客文章...</p>;
}
```

#### ✅ error.tsx：错误边界

捕获子组件抛出的错误。

```tsx
// app/blog/error.tsx
'use client';

import { Button } from '@/components/ui/button';

export default function BlogError({ error, reset }: { error: Error; reset: () => void }) {
  return (
    <div className="p-4 bg-red-100 text-red-800">
      <h2>加载失败</h2>
      <p>{error.message}</p>
      <Button onClick={reset}>重试</Button>
    </div>
  );
}
```

⚠️ 必须添加 'use client' 指令，因为错误处理需要在客户端进行。

#### ✅ not-found.tsx：404 页面

自定义 404 页面。

```tsx
// app/not-found.tsx
export default function NotFound() {
  return (
    <div className="text-center py-10">
      <h1>404 - 页面未找到</h1>
      <p>抱歉，您访问的页面不存在。</p>
    </div>
  );
}
```

使用 notFound() 函数触发：

```tsx
import { notFound } from 'next/navigation';

export default async function BlogPost({ params }: { params: { slug: string } }) {
  const post = await getPostBySlug(params.slug);
  if (!post) {
    notFound();
  }
  return <article>{post.title}</article>;
}
```

#### ✅ template.tsx vs layout.tsx

- layout.tsx：状态共享，组件实例复用。
- template.tsx：每次进入路由都会重新创建组件实例，适合需要重置状态的场景。

```tsx
// app/dashboard/template.tsx
export default function DashboardTemplate({ children }: { children: React.ReactNode }) {
  return <div>{children}</div>;
}
```

### 3.2 动态路由与参数

Next.js 支持动态路由，使用方括号 [param] 定义。

#### ✅ 基本动态路由

```tsx
// app/blog/[slug]/page.tsx
export default function BlogPost({ params }: { params: { slug: string } }) {
  return <h1>文章：{params.slug}</h1>;
}
```

访问 /blog/my-first-post，params.slug 的值为 "my-first-post"。

#### ✅ 多级动态路由

```tsx
// app/user/[id]/settings/page.tsx
export default function UserSettings({ params }: { params: { id: string } }) {
  return <h1>用户 {params.id} 的设置</h1>;
}
```

#### ✅ 可选动态路由（Catch-all Segments）

使用 [...param] 匹配多个层级。

```tsx
// app/docs/[...slug]/page.tsx
export default function DocsPage({ params }: { params: { slug?: string[] } }) {
  return <h1>文档路径：{params.slug?.join('/')}</h1>;
}
```

- /docs → slug = undefined
- /docs/a → slug = ['a']
- /docs/a/b/c → slug = ['a', 'b', 'c']

#### ✅ 通配符路由（Wildcard Segments）

使用 [...] 匹配任意路径（较少使用）。

### 3.3 并行路由与拦截路由（Advanced）

Next.js 还支持更高级的路由功能：

#### ✅ 并行路由（Parallel Routes）

允许在同一层级渲染多个独立的 UI 分支。

```
// app/(auth)/login/page.tsx
// app/(marketing)/home/page.tsx
```

括号 (...) 创建命名槽（slot），可用于并行渲染不同布局。

#### ✅ 拦截路由（Intercepting Routes）

用于在当前页面内打开模态框，同时保留背景页面。

```
// app/photos/[id]/page.tsx → /photos/1
// app/modal/photos/[id]/page.tsx → 拦截 /photos/1，在模态框中显示
```

使用 @modal 插槽：

```tsx
{/* app/layout.tsx */}
<div>
  {children}
  {modal && <div className="modal">{modal}</div>}
</div>
```

## 第四章：数据获取、RSC 与组件模型

### 4.1 RSC 与组件模型详解

App Router 默认使用 React Server Components (RSC)。理解服务器组件和客户端组件的区别是掌握现代 Next.js 开发的关键。

#### ✅ 服务器组件 (Server Components)

- 运行环境：仅在服务器端。
- 特点：
    - 不能使用 useState, useEffect, 事件处理 (onClick) 等。
    - 可以是 async 函数，直接 await 获取数据。
    - 可以直接访问数据库、文件、环境变量。
    - 代码不发送到客户端，体积为零。
    - 无法访问浏览器 API (window, document)。

```tsx
// app/page.tsx (Server Component)
import { getPosts } from '@/lib/api';

// 可以是 async 函数
export default async function HomePage() {
  // 直接在组件内获取数据
  const posts = await getPosts();

  return (
    <div>
      {posts.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.excerpt}</p>
        </article>
      ))}
    </div>
  );
}
```

#### ✅ 客户端组件 (Client Components)

- 运行环境：浏览器。
- 特点：
    - 必须使用 'use client' 指令声明。
    - 可以使用所有 React Hooks 和事件处理。
    - 代码会打包并发送到客户端。
    - 可以访问浏览器 API。

```tsx
// components/Counter.tsx
'use client';

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      点击了 {count} 次
    </button>
  );
}
```

### ✅ 组件间通信

- Props 传递：服务器组件可以将数据作为 props 传递给客户端组件。
- 可序列化性要求：这是 RSC 架构中一个至关重要的限制。在服务器组件与客户端组件之间传递的 Props 必须是可序列化的（即可以被 JSON.stringify() 处理）。这意味着不能传递函数、Promise、Date 对象、Map、Set、正则表达式等无法序列化的 JavaScript 值。

```tsx
// app/page.tsx (Server Component)
import Counter from '@/components/Counter';

export default async function HomePage() {
  const initialCount = await getInitialCount(); // 从服务器获取

  // 将数据传递给客户端组件
  return <Counter initialCount={initialCount} />;
}
```

小tips：这个限制仅存在于服务器组件与客户端组件之间的通信。如果是两个普通的客户端组件（都标记了 'use client'）之间进行父子通信，则没有此限制，可以自由传递任何 JavaScript 值，因为它们都在浏览器的同一个 JavaScript 运行时环境中，通过直接的内存引用进行交互，无需经过序列化过程。

#### 🔍 为什么 RSC 有这个限制？

这个限制源于 React Server Components 的核心工作原理：

- 生成“RSC Payload”：服务器组件在服务端执行后，不会直接生成最终的 HTML 字符串。相反，它会生成一个特殊的、描述 UI 结构的 “RSC Payload”。这个 Payload 是一个可序列化的数据结构（类似于 JSON）。
- 网络传输：这个“RSC Payload”通过 HTTP 流从服务器传输到客户端浏览器。
- 客户端重建：浏览器接收到 Payload 后，React 的客户端运行时会解析它，并据此：
    - 将服务器生成的 HTML 片段插入 DOM。
    - 找到需要在客户端渲染的组件。
    - 使用 Payload 中包含的 Props 数据来渲染这些客户端组件。

关键点在于：连接服务器和客户端的桥梁就是这个“RSC Payload”，而它必须是可序列化的。任何无法被 JSON.stringify() 处理的对象都无法通过这个网络桥梁。

- function：JSON.stringify(() => {}) 的结果是 undefined。
- Promise：JSON.stringify(Promise.resolve(1)) 的结果是 {}，丢失了所有状态和功能。
- Date：JSON.stringify(new Date()) 的结果是时间字符串（如 "2025-08-21T15:30:00.000Z"），接收方得到的是字符串，而非 Date 对象，无法调用 .getFullYear() 等方法。

因此，为了保证数据能安全、完整地从服务器传输到客户端，RSC 强制要求跨边界传递的 Props 必须是可序列化的纯数据。

### 4.2 数据获取方法

Next.js 提供了多种数据获取方式，适用于不同场景。

#### ✅ async 组件（推荐 - RSC）

在服务器组件中，页面组件可以直接是 async 函数，内部使用 await 获取数据。

```tsx
// app/blog/page.tsx
import { getPosts } from '@/lib/api';

export default async function BlogPage() {
  const posts = await getPosts();

  return (
    <div>
      {posts.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.excerpt}</p>
        </article>
      ))}
    </div>
  );
}
```

✅ 优点：简洁、无需额外 Hooks，自动支持 Suspense，解决瀑布流问题。

#### ✅ fetch API 增强

Next.js 对 fetch 进行了增强，支持缓存和重新验证。

```ts
const res = await fetch('https://api.example.com/posts', {
  cache: 'force-cache', // 默认，静态生成
  // next: { revalidate: 60 } // ISR：每60秒重新生成
});
```

- cache: 'force-cache'：使用缓存（SSG）。
- cache: 'no-store'：不缓存，每次都请求（SSR）。
- next: { revalidate: 60 }：ISR，60 秒后重新生成。

#### ✅ generateStaticParams：生成静态路径

用于 SSG，告诉 Next.js 在构建时生成哪些动态路由。

```tsx
// app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  const posts = await getPublishedPosts();
  return posts.map(post => ({ slug: post.slug }));
}

export default async function Post({ params }: { params: { slug: string } }) {
  const post = await getPostBySlug(params.slug);
  return <article>{post.content}</article>;
}
```

⚠️ 必须与 output: 'export' 配合使用才能生成完全静态站点。

## 第五章：实战项目：构建一个 Todo 应用

现在，让我们动手构建一个完整的 Todo 应用，巩固所学知识。

### 5.1 项目结构规划

```
app/
├── layout.tsx
├── page.tsx        # 首页：显示所有任务
├── new/page.tsx    # 新建任务
├── [id]/page.tsx   # 查看/编辑任务
├── api/            # API 路由（可选）
└── components/
    ├── TaskList.tsx
    └── TaskForm.tsx
```

### 5.2 实现首页（RSC + 数据获取）

```tsx
// app/page.tsx
import TaskList from '@/components/TaskList';
import Link from 'next/link';
import { getTasks } from '@/lib/tasks';

// 服务器组件，可以直接获取数据
export default async function HomePage() {
  const tasks = await getTasks();

  return (
    <div>
      <h1>我的待办事项</h1>
      <Link href="/new" className="bg-blue-500 text-white px-4 py-2 rounded">
        新建任务
      </Link>
      <TaskList tasks={tasks} />
    </div>
  );
}
```

```ts
// lib/tasks.ts
let tasks = [
  { id: 1, title: '学习 Next.js', completed: false },
  { id: 2, title: '写博客', completed: true },
];

export async function getTasks() {
  return tasks;
}

export async function createTask(title: string) {
  const newTask = { id: Date.now(), title, completed: false };
  tasks.push(newTask);
  return newTask;
}
```

### 5.3 新建任务页面（客户端组件）

```tsx
// app/new/page.tsx
'use client';

import { useState } from 'react';
import { createTask } from '@/lib/tasks';
import { useRouter } from 'next/navigation';

export default function NewTaskPage() {
  const [title, setTitle] = useState('');
  const router = useRouter();

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    await createTask(title);
    router.push('/');
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={title}
        onChange={(e) => setTitle(e.target.value)}
        placeholder="输入任务标题"
        required
      />
      <button type="submit">创建</button>
    </form>
  );
}
```

🔍 注意：这里使用了 'use client' 和 useRouter，因为需要客户端交互。

## 第六章：部署与优化

### 6.1 部署到 Vercel

Vercel 是 Next.js 的官方推荐部署平台，支持一键部署。

```bash
# 登录 Vercel
npx vercel login

# 部署
npx vercel --prod
```

### 6.2 性能优化技巧

- 使用 next/image 优化图片。
- 启用 ISR 实现静态再生。
- 使用 next/link 预加载页面。
- 合理拆分客户端组件。
- 优先使用服务器组件，减少客户端 JS 体积。

## 结语

Next.js 是现代 Web 开发的强大工具，它将 React 的灵活性与服务端渲染的性能优势完美结合。通过本篇万字长文，你已经掌握了从项目创建、路由系统、数据获取到实际开发的完整流程，特别是深入理解了 RSC 这一 React 19 的革命性特性如何超越传统 SSR，带来极致的性能优化。

继续深入学习 Next.js 的 API 路由、中间件、国际化、缓存策略等高级特性，你将能够构建出更加复杂和高性能的应用。

学习不止，探索不息。
