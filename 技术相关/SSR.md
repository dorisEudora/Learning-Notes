# 服务器渲染与客户端渲染

## 什么是服务器渲染和客户端渲染？

在早期互联网时代，浏览器中的网页大多只注重功能，并没有一些复杂逻辑，这些页面都是由后端进行拼接然后返回给浏览器端进行渲染，浏览器拿到这个 html 文件之后就可以直接进行解析，然后展示给用户，这就是所谓的**服务器端渲染**。

而随着前端页面的复杂性提高，前端就不仅仅是普通的页面展示了，而可能添加了更多功能性的组件，复杂性更大，另外，由于`ajax`的渐渐兴起，使得业界就开始推崇**前后端分离**的开发模式，即后端不提供完整的 html 页面，而是提供一些 api 使得前端可以获取到 json 数据，然后前端拿到 json 数据之后再在前端进行 html 页面的拼接，然后展示在浏览器上，这就是所谓的**客户端渲染**，这样前端就可以专注 UI 的开发，后端专注于逻辑的开发。



## 如何区分服务器渲染与客户端渲染？

一个很简单的方法，在页面上点击右键查看网页源代码，如果页面上的数据都是在网页源代码中显示的，就是服务器渲染。反之，如果页面上的内容并没有直接在源代码中体现，就是客户端渲染。



## 客户端渲染的优势与弊端

**优势：**

- 客户端渲染实行的是前后端分离的开发模式，前端负责界面的渲染，后端负责接口数据的提供，各自做自己的事情，在团队开发中能够极大地提高开发效率。
- 对于用户来说体验效果会更好。比如，我们将网站做成 SPA 或者部分内容做成 SPA，这样，尤其是移动端，可以使体验更接近于原生 app。

**弊端：**

- 首屏加载速度更慢。如果网站中的源码包很大，用户从进入页面到看到实际的数据需要经过很长的一段时间，这样会大大降低用户的使用体验。
- 不利于 SEO（搜索引擎优化）。到目前为止，只有谷歌对 SPA 应用有着识别功能，而国内的搜索引擎诸如百度对于 SPA 应用都是无法识别的，只是记录了一个空的页面，所以 SEO 会很差，不利于该网站的在搜索引擎中的排名。

 

### 为什么客户端渲染的首屏加载更慢？

当用户进入网站时，浏览器的请求流程：

- 客户端渲染：浏览器发送请求 -> 服务器返回 HTML -> 浏览器发送打包后的 js 文件的请求 -> 服务器返回打包后的 js -> 浏览器执行服务器返回 js 文件中的代码
- 服务端渲染：浏览器发送请求 -> 服务器运行 React / Vue 代码生成 HTML 页面 -> 服务器返回生成的 HTML

可以看出，客户端渲染浏览器需要多执行解析 js，向服务器发送请求的过程，所以首屏加载的速度会更慢一些。



 

## 如何选择使用服务器端渲染还是客户端渲染

具体的使用场景需要根据具体的业务需求来进行选型。比如企业级网站，主要功能是**展示**而**没有复杂的交互**，并且需要**良好的 SEO**，则这时我们就需要使用服务器端渲染。

而类似后台管理页面，交互性比较强，不需要seo的考虑，那么就可以使用客户端渲染。

另外，具体使用何种渲染方法并不是绝对的，比如现在一些网站采用了**首屏服务器端渲染**，即对于用户最开始打开的那个页面采用的是服务器端渲染（比如 Next 和 Nust）。

 