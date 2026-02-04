
![image](./images/image-1770193568855-0.jpg)

 > 译自 Server-Side Rendering for Better Web Apps - Builder.io

服务器端渲染 (SSR) 已经存在一段时间了，但它值得进一步探索。这项技术可以使您的 Web 应用更快、更利于 SEO。

本指南将解释 SSR，为什么您可能想要使用它，以及如何在不费力的情况下实现它。我们将介绍基础知识，将其与客户端渲染进行比较，并讨论一些实际示例。

### 什么是服务器端渲染？

从根本上说，SSR 是指在服务器上而不是在浏览器上渲染您的网页。当用户请求页面时，服务器会完成所有繁重的工作并将完全渲染的页面发送到客户端。然后，客户端 JavaScript 接管以使其具有交互性。

服务器在厨房里做准备工作，浏览器只需摆盘和上菜。

这是一个最小的 Express.js 示例：

```javascript
const express = require('express');
const React = require('react');
const ReactDOMServer = require('react-dom/server');
const App = require('./App');

const app = express();

app.get('/', (req, res) => {
  const html = ReactDOMServer.renderToString();
  res.send(`
    
    
      
        ${html}
        
      
    
  `);
});

app.listen(3000, () => console.log('Server running on port 3000'));
```
### 从服务器到浏览器，页面完全渲染

当我们谈到 SSR 提供“完全渲染的页面”时，重要的是要理解这实际上意味着什么。让我们分解一下：

什么是完全渲染的页面？完全渲染的页面是一个 HTML 文档，其中包含用户首次加载页面时将获得的所有内容。这包括：

完整的 DOM 结构所有文本内容图片占位符和其他媒体元素初始样式这是一个基本示例：

```javascript

  
    My SSR Page
    
      /* Initial styles */
    
  
  
    
      Welcome to My Site
      
    
    
      
        Article Title
        This is the full content of the article...
      
    
    
    
  

```
与 CSR 的区别相比之下，客户端渲染 (CSR) 的初始 HTML 可能如下所示：

```javascript

  
    My CSR Page
  
  
    
    
  

```
CSR 页面完全依赖 JavaScript 来填充内容。

完全渲染HTML的好处 **更快的初始绘制**: 浏览器可以立即开始渲染内容。 **更好的 SEO**: 搜索引擎无需执行 JavaScript 即可读取所有内容。 **改进的辅助功能**: 屏幕阅读器和其他辅助技术可以立即访问内容。 **弹性**: 即使 JavaScript 无法加载，基本内容也可以使用。水合过程发送完全渲染的 HTML 后，SSR 应用程序通常会经历一个称为水合的过程：

服务器发送完全渲染的 HTML。浏览器立即显示此 HTML。JavaScript 加载并水合页面，添加交互性。```javascript
// Simplified React hydration example
import { hydrateRoot } from 'react-dom/client';
import App from './App';

const domNode = document.getElementById('root');
hydrateRoot(domNode, );
```
此过程允许快速初始加载，同时仍提供现代 Web 应用的丰富交互性。

请记住，虽然 SSR 提供了这些完全渲染的页面，但它并非没有权衡。服务器的工作量更大，您需要仔细处理服务器和客户端之间的状态。但是，对于许多应用程序而言，完全渲染页面的好处使 SSR 成为一个引人注目的选择。

### CSR 和 SSR 的区别

客户端渲染 (CSR) 和服务器端渲染 (SSR) 是渲染网页的两种不同方法。以下是它们主要区别的细分：

客户端渲染（CSR）服务器发送一个包含JavaScript捆绑包的最小HTML文件。浏览器下载并运行JavaScript。JavaScript创建页面内容并使其具有交互性。优点：

初始加载后交互流畅所需服务器资源更少缺点：

初始页面加载速度较慢可能面临搜索引擎优化挑战服务器端渲染（SSR）服务器创建完整的HTML内容。浏览器接收并快速显示预渲染的HTML。然后JavaScript加载以使页面完全交互。优点：

页面初始加载更快对搜索引擎优化（SEO）更有利适合在较慢的设备上工作缺点：

设置可能更复杂可能会使用更多的服务器资源这是一个简单的视觉比较：

![image](./images/image-1770193573264-1.webp)
本质上，CSR 在浏览器中运行更多，而 SSR 在服务器上运行更多。它们之间的选择取决于项目的特定需求，平衡初始加载时间、SEO 要求和服务器资源等因素。

### SSR和搜索引擎：HTTP中的完美搭配

服务器端渲染会对搜索引擎查看您网站的方式产生重大影响。让我们分解一下：

更快的索引搜索引擎机器人没有耐心。它们想立刻看到内容。有了服务器端渲染（SSR），当机器人来抓取时，页面已经准备好了——不需要等待JavaScript加载和渲染。

![image](./images/image-1770193578229-2.webp)
内容一致性SSR确保搜索引擎看到的内容与用户看到的相同。使用客户端渲染，总是存在机器人可能错过一些动态加载内容的风险。

提升加载时间搜索引擎喜欢快速的网站。服务器端渲染（SSR）可以显著减少初始加载时间，这可能会让你在排名上获得轻微的优势。

```javascript
// Pseudo-code for search engine ranking
function calculateRanking(site) {
  let score = site.relevance;
  if (site.loadTime 
```
移动优先的索引谷歌的移动优先索引使得服务器端渲染在较慢的移动连接上的性能优势变得更加重要。

社交媒体预览虽然不是严格的搜索引擎功能，SSR使得在内容被分享到社交平台时生成准确的预览变得更加容易。这可以通过增加参与度和反向链接间接提升你的SEO。

```javascript

```
SSR是SEO的强大工具，但并非唯一因素。内容质量、相关性和整体用户体验在搜索引擎排名中至关重要。SSR只是确保搜索引擎能够高效地爬取和索引你的内容，可能会让你在可见性和性能指标上获得优势。

### 如何实际进行SSR

实现SSR并不复杂。让我们来探讨如何使用Next.js，一个流行的React框架，使得SSR变得简单直接：

设置一个Next.js项目。创建服务器端渲染页面。让Next.js处理完全渲染的HTML和客户端水合。这里有一个使用App Router的简单Next.js示例：

```javascript
// app/page.js
async function getData() {
  const res = await fetch('')
  if (!res.ok) {
    throw new Error('Failed to fetch data')
  }
  return res.json()
}

export default async function Home() {
  const data = await getData()

  return Hello {data.name}
}
```
在这个例子中：

Home组件是一个异步函数，允许进行服务器端数据获取。getData()获取我们所需的数据。组件直接渲染数据。Next.js自动处理SSR过程：

当请求进来时，Next.js在服务器上运行这个组件。它等待数据被获取。它用获取到的数据渲染组件。完全渲染的HTML被发送到客户端。一旦浏览器中的JavaScript加载完成，页面就变得可交互。这种方法让你享受到SSR的好处，而无需手动设置服务器或自己管理渲染过程。

### 更高级的 SSR 解决方案

如果你不想重新发明轮子，有几个框架可以为你处理服务器端渲染（SSR）的复杂性。以下是不同生态系统中流行的选项：

![image](https://developer.qcloudimg.com/http-save/yehe-11033471/2cc893a9afc0c08c248c787147d39b87.webp)
React

Next.js：内置SSR支持的最流行的React框架。Remix：利用React Router的全栈Web框架。Gatsby：主要是静态站点生成器，但也支持SSR。Vue

Nuxt.js：Vue应用的首选框架，具备SSR能力。Angular

Angular Universal：Angular应用的官方SSR解决方案。Svelte

SvelteKit：Svelte的官方应用框架，支持SSR。JavaScript（框架无关）

Astro：允许你使用多个框架，并支持SSR。Qwik：一个为最佳性能设计的新型框架，内置SSR支持。PHP

Laravel：通过Inertia.js或其自己的Livewire组件提供SSR能力。Ruby

Ruby on Rails：通过Stimulus Reflex或Hotwire等工具支持SSR。Python

Django：可以使用Django-Unicorn或HTMX等库实现SSR。Flask：可以配置为SSR，通常与Flask-SSE等扩展一起使用。这些框架各自提供了对SSR的不同方法，通常还包含静态站点生成、API路由等附加功能。选择取决于你偏好的语言、生态系统和特定项目需求。

### 部署和缓存

在部署SSR应用时：

构建客户端和服务器端的捆绑包。将SSR服务器作为后台进程运行。使用像PM2或Supervisor这样的进程监控器来保持服务器运行。这是一个基本的部署流程：

![image](./images/image-1770193587764-4.webp)
不要忘记缓存！缓存服务器渲染的页面可以显著降低服务器负载。

### Builder.io 中的 SSR

Builder.io 提供了对所有组件和框架的服务器端渲染（SSR）和静态站点生成（SSG）的支持。这种即开即用的功能允许你在无需额外设置的情况下利用 SSR 和 SSG 的优势。

![image](./images/image-1770193592768-5.webp)
关键特性 **框架无关性**: Builder.io 支持各种支持 SSR 和 SSG 的框架。 **自动优化**: Builder 会优化您的内容性能，包括代码分割和屏幕外组件的延迟加载。 **动态渲染**: 您可以根据用户属性或A/B 测试渲染不同的内容，同时保持SEO 优势。 **轻松集成**: Builder 提供SDK 和文档，以便无缝集成您的现有项目。实施示例以下是如何使用Builder 和 Next.js在服务器端获取和渲染内容的基本示例：

```javascript
import { builder, BuilderComponent } from '@builder.io/react'

builder.init('YOUR_API_KEY')

export async function getStaticProps({ params }) {
  const page = await builder
    .get('page', {
      userAttributes: {
        urlPath: '/' + (params?.page?.join('/') || '')
      }
    })
    .toPromise()

  return {
    props: {
      page: page || null,
    },
    revalidate: 5
  }
}

export default function Page({ page }) {
  return (
    
  )
}
```
最佳实践确保您正在使用支持 SSR 或 SSG 的框架。集成 Builder 页面或区块时，请遵循框架的服务器端数据获取指南。有关处理服务器端数据的更多信息，请参阅`getAsyncProps`自述文件。通过利用 Builder 进行 SSR，您可以将无头 CMS 的灵活性和服务器端渲染的性能优势相结合，同时保持易于使用的可视化编辑体验。

### 收尾

服务器端渲染 (SSR) 是一种强大的 Web 开发方法，可以显著提高应用程序的性能、SEO 和用户体验。在本文中，我们探讨了 SSR 的含义、它与客户端渲染的不同之处、它对搜索引擎的影响以及使用Next.js 等流行框架的实际实施策略。

我们还讨论了完全渲染页面的概念，并检查了不同生态系统中的各种 SSR 解决方案。虽然 SSR 提供了许多好处，但在决定是否实施它时，务必考虑项目的具体需求。

**问：SSR 如何影响我的开发工作流程？** 答：SSR 可能会使开发变得更加复杂，因为您需要同时考虑服务器和客户端环境。您可能需要调整构建过程并注意特定于浏览器的 API。

**问：SSR 如何影响我网站的交互时间 (TTI)？** 答：虽然 SSR 可以提高初始内容可见性，但它可能会稍微延迟 TTI，因为浏览器需要在收到初始 HTML 后加载和水化 JavaScript。

**问：SSR 有哪些特定的安全注意事项？** 答：是的，使用 SSR 时，您需要更加小心地保护服务器端敏感数据或 API。始终清理用户输入，并注意在初始渲染中包含哪些数据。

**问：SSR 如何与身份验证和个性化内容一起使用？** 答：SSR 可以与身份验证一起使用，但这需要仔细处理。您可能需要实现 JWT 令牌或服务器端会话等技术来管理经过身份验证的 SSR 请求。

