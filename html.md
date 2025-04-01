###  1. **HTML5 的新特性有哪些？**

- **语义化标签**：如 `<article>`, `<section>`, `<nav>`, `<header>`, `<footer>`, `<aside>`，增强文档的可读性和结构化。

- **新的表单输入类型**：如 `email`, `url`, `number`, `date` 等，简化了表单的输入验证。

- **多媒体支持**：引入了 `<audio>` 和 `<video>` 标签，直接支持音频和视频播放，而无需插件。

- **离线存储**：使用 `localStorage` 和 `sessionStorage` 来存储客户端数据，`indexedDB` 用于复杂数据存储。

- **Canvas 和 SVG**： `<canvas>` 标签提供了绘制图形的能力，SVG 支持矢量图形。

- **Geolocation API**：允许获取用户的地理位置。

- **Web Workers**：支持多线程后台任务执行，不阻塞 UI。

- **WebSocket**：提供了双向通信能力，适用于实时数据传输场景。

### 2. **如何使用 HTML5 实现表单验证？**

  HTML5 提供了内置的表单验证特性：
  - 必填字段：使用 required属性。
    ```html
    <input type="text" required>
    ```
  - 输入模式验证：使用 pattern属性来定义正则表达式。
    ```html
    <input type="text" pattern="[A-Za-z]{3}">
    ```

  - **新输入类型**：如 `email`, `url`, `number` 自动包含了相应的验证逻辑。
    例子:
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0, user-scalable=0, minimum-scale=1.0, maximum-scale=1.0"
    />
    <title>表单验证默认样式修改</title>
  </head>
  <style>
    .oneline {
      line-height: 1.5;
      margin: 10px auto;
    }
    .oneline label {
      width: 100px;
      text-indent: 15px;
      font-size: 14px;
      font-family: "Microsoft Yahei";
      display: inline-block;
    }
    .oneline .sinput {
      width: 60%;
      height: 30px;
      border-radius: 6px;
      border: 1px solid [[e2e2e2]];
    }
    .oneline input[type="submit"] {
      margin-left: 20px;
      width: 80px;
      height: 30px;
      border: 0;
      background-color: [[5899d0]];
      color: [[fff]];
      font-size: 14px;
      border-radius: 6px;
    }
    .error-message {
      color: red;
      font-size: 12px;
      text-indent: 108px;
    }
  </style>

  <body>
    <form id="forms">
      <div class="oneline">
        <label for="name">用户名:</label>
        <input id="name" name="name" class="sinput" required />
      </div>
      <div class="oneline">
        <label for="email">Email:</label>
        <input id="email" name="email" class="sinput" type="email" required />
      </div>
      <div class="oneline">
        <input type="submit" value="提交" id="thesubmit" />
      </div>
    </form>
    <script>
      function replaceValidationUI(form) {
        form.addEventListener(
          "invalid",
          function (event) {
            event.preventDefault();
          },
          true
        );

        form.addEventListener(
          "submit",
          function (event) {
            if (!this.checkValidity()) {
              event.preventDefault();
            }
          },
          true
        );
        var submitButton = document.getElementById("thesubmit");
        submitButton.addEventListener("click", function (event) {
          var inValidityField = form.querySelectorAll(":invalid"),
            errorMessage = form.querySelectorAll(".error-message"),
            parent;

          for (var i = 0; i < errorMessage.length; i++) {
            errorMessage[i].parentNode.removeChild(errorMessage[i]);
          }
          for (var i = 0; i < inValidityField.length; i++) {
            parent = inValidityField[i].parentNode;
            parent.insertAdjacentHTML(
              "beforeend",
              "<div class='error-message'>" +
                inValidityField[i].validationMessage +
                "</div>"
            );
          }
          if (inValidityField.length > 0) {
            inValidityField[0].focus();
          }
        });
      }
      var form = document.getElementById("forms");
      replaceValidationUI(form);
    </script>
  </body>
</html>
```

### 3. **HTML 中的 meta 标签有何作用？**

- `<meta>` 标签用于定义文档的元数据。常见的属性有：
    - `charset`：定义字符集，如 `UTF-8`。
	
    - `viewport`：控制视口的大小和缩放比例，常用于响应式设计。
    
    - `description`：为页面提供简短的描述，通常用于搜索引擎优化 (SEO)。
    
    - `keywords`：定义页面关键词，用于 SEO（尽管现代搜索引擎不再高度依赖这个标签）。
    
    - `robots`：控制搜索引擎抓取行为，如 `index`, `noindex`, `follow`, `nofollow`。
    
      **作用**
    
      1. 控制页面是否被索引
         通过meta robots，你可以告诉搜索引擎是否将页面内容纳入其索引库。例如，阻止某些页面（如隐私页面或临时页面）被搜索引擎收录。
      2. 控制链接是否被跟踪
         可以指定爬虫是否跟随页面上的链接（即爬取链接指向的其他页面）。
      3. 优化SEO
         通过合理设置，帮助搜索引擎更好地理解网站结构，避免重复内容被索引，或者防止低质量页面影响网站排名。
    
      **常用指令**
    
      meta robots标签的基本格式如下：
    
      ```html
      <meta name="robots" content="指令">
      ```
    
      常见的content值包括：（1. 大小写不敏感  2. 多个指令组合用逗号分隔多个指令）
    
      - index: 允许搜索引擎索引该页面（默认行为）。
      - noindex: 禁止搜索引擎索引该页面，页面不会出现在搜索结果中。
      - follow: 允许搜索引擎跟踪页面上的链接（默认行为）。
      - nofollow: 禁止搜索引擎跟踪页面上的链接。
      - noarchive: 禁止搜索引擎缓存页面内容（即搜索结果中不显示“网页快照”）。
      - nosnippet: 禁止搜索引擎在搜索结果中显示页面的摘要。
### 4. **如何优化页面加载速度？**

- **减少 HTTP 请求**：合并 CSS、JavaScript 文件，使用图像 Sprites。

- **使用异步加载脚本**：将非必要的脚本放在页面底部，并使用 `async` 或 `defer` 属性。

  ```html
  <script src="script.js" async></script>
  ```

- **压缩文件**：通过 Gzip 压缩 HTML, CSS, JavaScript 文件，减小文件大小。

- **缓存资源**：使用浏览器缓存策略，设置合理的 `Cache-Control` 头。

- **延迟加载图像**：通过 `lazy-loading` 或 JavaScript 实现懒加载图片。

- **使用 Content Delivery Network (CDN)**：将静态资源托管到 CDN，以提高加载速度。

### 5. **HTML5 的离线存储有哪些实现方式？**

- **localStorage**：用于存储持久化数据，数据不会随浏览器关闭而消失，适合用于保存用户设置等信息。
- **sessionStorage**：数据只在会话期间有效，关闭浏览器后数据丢失，适用于保存会话级别的数据。
- **IndexedDB**：一种更高级的存储方式，适合存储大量结构化数据，支持事务、索引等功能。

### 6. **什么是 shadow DOM？它的作用是什么？**

- **Shadow DOM** 是一种隔离 DOM 的技术，它允许开发者将组件的结构、样式和行为封装起来，避免与页面的其他部分发生冲突。通常用于 Web Components 中。
- 作用：
  - 样式隔离：避免外部样式对组件内部样式的影响。
  - 结构封装：组件的内部 DOM 不会被外部 JavaScript 直接操作。

### 7. **如何使用 `data-\*` 自定义属性？**

- HTML5 引入了 `data-*` 属性，允许开发者在标准属性之外存储自定义数据，适用于前端与后端或前端组件间的通信。

  ```html
  <div id="myDiv" data-user-id="12345" data-role="admin"></div>
  ```

- 在 JavaScript 中，可以通过 `dataset` 访问这些属性：

  ```javascript
  var userId = document.getElementById('myDiv').dataset.userId;
  var role = document.getElementById('myDiv').dataset.role;
  ```

### 8. **什么是 `defer` 和 `async`？它们的区别是什么？**

- `async`和 `defer` 属性用于控制外部 JavaScript 文件的加载和执行顺序：

  - **`async`**：脚本异步加载，并在下载完成后立即执行，不会等待 HTML 文档的解析。多个 `async` 脚本的执行顺序无法保证，适用于独立模块的脚本。
  - **`defer`**：脚本异步加载，但会在 HTML 文档完全解析完毕后按顺序执行，适合依赖 HTML 结构或其他脚本的场景。

  ```html
  <script src="script.js" async></script>
  <script src="script.js" defer></script>
  ```

### 9. **HTML 中的 SVG 与 Canvas 有何区别？**

- **SVG (Scalable Vector Graphics)**：基于 XML，描述的是矢量图形，每个图形元素都是 DOM 元素，具备事件处理能力。适合复杂图形和具有交互性的场景（如地图、图标）。
- **Canvas**：基于像素的绘制，使用 JavaScript 绘制图形，无法像 SVG 一样直接操作 DOM，适合实时更新或大量像素操作的场景（如游戏、数据可视化）。

### 10. **如何在 HTML 中实现响应式图片？**

- 使用 `<img>`的 `srcset`属性和 `<picture>`元素，可以根据不同屏幕大小加载不同分辨率的图片。

  ```html
  <img src="default.jpg" 
       srcset="small.jpg 500w, medium.jpg 1000w, large.jpg 2000w"
       sizes="(max-width: 600px) 480px, 
              (max-width: 1200px) 960px, 
              2000px"
       alt="Responsive Image">
  
  <picture>
    <source media="(min-width: 650px)" srcset="large.jpg">
    <source media="(min-width: 465px)" srcset="medium.jpg">
    <img src="small.jpg" alt="Sample Image">
  </picture>
  ```

- `srcset` 指定多个图像资源，`sizes` 决定在不同视口宽度下使用的图像宽度。

- `<picture>` 元素允许开发者提供不同的图像资源，以适应不同的屏幕尺寸或设备。

### 11. **`<link>` 和 `<@import>` 的区别是什么？**

- **`<link>`** 标签用于在 HTML 中加载外部资源，通常用于加载 CSS 文件，属于 HTML 级别的加载。

  ```html
  <link rel="stylesheet" href="style.css">
  ```

- **`@import`** 是在 CSS 中使用的语法，用于导入其他 CSS 文件，属于 CSS 级别加载：

  ```css
  @import url("style.css");
  ```

- **区别**：

  - `<link>` 会在页面加载时并行加载资源，而 `@import` 会在 CSS 文件被加载和解析后才加载资源，延迟了外部文件的加载，性能较差。
  - `<link>` 更符合现代开发习惯，因为它能被浏览器并行下载处理。

### 12. **HTML 中的 `<iframe>` 标签的作用是什么？它的优缺点是什么？**

- `<iframe>` 标签用于在当前页面中嵌入另一个 HTML 页面。

  <iframe src="https://www.example.com" width="600" height="400"></iframe>

- **优点**：

  - 可嵌入第三方内容，如广告、视频、地图等。
  - 允许加载跨域内容而不破坏当前页面的样式和布局。

  **缺点**：

  - 性能问题：`<iframe>` 可能增加页面加载时间。
  - 安全风险：可能引入跨域脚本攻击（XSS），因此应注意设置适当的安全策略。
  - SEO 问题：`<iframe>` 中的内容对搜索引擎优化 (SEO) 不友好，无法被抓取。

### 13. **HTML 中的 `rel="noopener noreferrer"` 是什么？为什么使用它？**

- 在 `<a>` 标签中使用 `target="_blank"` 会在新窗口中打开链接，但这可能会带来安全风险，因为新页面可以通过 `window.opener` 获取对原页面的引用并执行恶意操作。

- 防止这种行为：(两种方式)

  ```js
  window.open(url, '_blank', 'noopener');
  ```

  ```html
  <a href="https://www.example.com" target="_blank" rel="noopener noreferrer">Example</a>
  ```

  **作用**：

  - `noopener`：防止新窗口获取对原窗口的控制。
  - `noreferrer`：不发送 `Referer` 头信息。

  **扩展**

  window.opener 是 JavaScript 中的一个全局属性，用于引用打开当前窗口的父窗口。它通常在新窗口（通过 window.open() 方法打开）中起作用，帮助新窗口与原始窗口进行交互。

  **window.opener 主要用于以下场景：**

  1. 跨窗口通信  
     - 新窗口可以通过 window.opener 调用父窗口的函数、访问其全局变量或修改其 DOM。
     - 父窗口也可以通过返回的窗口引用（如 newWindow）操作新窗口。
  2. 数据传递  ：在新窗口和父窗口之间传递数据，例如表单提交结果。
  3. 关闭新窗口后通知父窗口  ：新窗口可以在关闭前通过 window.opener 通知父窗口刷新或执行某些操作。

  **注意事项**

  (1) 跨域限制

  - 如果父窗口和新窗口的域名不同（违反同源策略），window.opener 的访问会受到限制。

  - 可以通过 postMessage API 实现跨域通信：

    ```javascript
    // 子窗口
    window.opener.postMessage('消息', 'https://parent-domain.com');
    
    // 父窗口
    window.addEventListener('message', (event) => {
      if (event.origin === 'https://child-domain.com') {
        console.log(event.data);
      }
    });
    ```

  (2) 安全性问题

  - 恶意操作: 如果新窗口是不可信的第三方页面，它可能通过 window.opener 修改父窗口的内容。

  - 防护措施: 

    - 在父窗口中设置 rel="noopener"，阻止新窗口获取 window.opener：

      ```html
      <a href="https://example.com" target="_blank" rel="noopener">打开链接</a>
      ```

    - 使用 window.open() 时添加 noopener 参数

      ```javascript
      window.open('https://example.com', '_blank', 'noopener');
      ```

    - 这会使新窗口的 window.opener 为 null，增强安全性。

  (3) 浏览器行为

  - 某些浏览器可能限制 window.close()（仅允许关闭通过脚本打开的窗口）。
  - 如果用户手动打开新标签页，window.opener 始终为 null。

  (4) 现代替代方案

  - 对于复杂的跨窗口交互，推荐使用 postMessage 或 Web Storage（如 localStorage）替代直接操作 window.opener，因为它们更安全且跨域支持更好。

### 14. **什么是 Progressive Web App (PWA)，它的 HTML 实现方式是什么？**

- **PWA** 是一种结合了 Web 和 Native 应用特性的网站应用，提供了类似原生应用的用户体验。PWA 通过 Service Worker 实现离线支持、推送通知等功能，并可以通过 `manifest.json` 文件进行配置。
- HTML 实现方式：
  - 使用 `<link rel="manifest" href="manifest.json">` 引入应用清单文件。
  - `manifest.json` 定义了应用的图标、启动页面等元数据。
  - 通过 Service Worker 实现离线缓存功能。

### 15. **`<template>` 标签的作用是什么？**

- `<template>` 标签用于定义可重用的 HTML 模板，但不会在页面上立即渲染。它常与 JavaScript 配合使用，用于动态插入内容。

  ```html
  <template id="my-template">
    <p>Hello, this is a template!</p>
  </template>
  
  <script>
    var template = document.getElementById('my-template');
    var clone = template.content.cloneNode(true);
    document.body.appendChild(clone);
  </script>
  
  ```

### 16. **HTML5 中如何处理媒体资源的兼容性问题？**

- 使用多种编码格式以适应不同浏览器的支持。

  ```html
  <video controls>
    <source src="movie.mp4" type="video/mp4">
    <source src="movie.ogv" type="video/ogg">
    <source src="movie.webm" type="video/webm">
    Your browser does not support the video tag.
  </video>
  ```


### 17. **`<progress>` 和 `<meter>` 标签的区别是什么？**

- **`<progress>`** 表示任务的进度，通常用于显示已完成的部分和总的工作量。

  ```html
  <progress value="30" max="100"></progress>
  ```

- **`<meter>`** 表示某个数值在已知范围内的比例，常用于表示数值等级、评分等。

  ```html
  <meter value="0.6" min="0" max="1"></meter>
  ```

### 18. **HTML5 的 `<details>` 和 `<summary>` 标签有什么作用？**

- **`<details>`** 标签用于创建==可展开/折叠==的内容，用户可以点击展开或折叠部分内容。

- **`<summary>`** 标签用来定义可点击的标题。

  ```html
  <details>
    <summary>更多信息</summary>
    <p>这是展开后的内容。</p>
  </details>
  ```


### 19. **HTML5 中的 `accesskey` 属性的作用是什么？**

- `accesskey` 属性允许开发者为某些 HTML 元素指定快捷键，用户可以通过按下组合键快速聚焦或激活该元素。

- 使用 `Alt + AccessKey`（Windows）或 `Ctrl + Alt + AccessKey`（Mac）组合键来触发该按钮。

  ```html
  <button accesskey="s">保存</button>
  ```


### 20. **如何防止表单中的 CSRF（跨站请求伪造）攻击？**

- CSRF 是一种通过冒充用户身份执行未经授权的请求的攻击。

- 防护措施：

  - 在表单中引入 CSRF 令牌：

    ```html
    <input type="hidden" name="csrf_token" value="随机生成的令牌">
    ```

  - 在服务器端验证令牌是否有效。

  - 使用 `SameSite` cookie 属性，限制跨站点的请求。

    ```http
    Set-Cokie: sessionId=abc123; SameSite=Strict
    ```

### 21. **HTML5 中的 `sandbox` 属性如何增强安全性？**

- **`sandbox`** 属性用于 `<iframe>` 标签，限制其中的内容执行权限，增强安全性。

- **可选值**：

  - `allow-same-origin`：允许 iframe 内容与父页面同源。
  - `allow-scripts`：允许脚本运行，但禁止创建新的窗口。
  - `allow-forms`：允许表单提交。

  ```html
  <iframe src="https://example.com" sandbox="allow-scripts"></iframe>
  ```

### 21. **什么是 ARIA，如何使用它提升网页的可访问性？**

- **ARIA (Accessible Rich Internet Applications)** 是一套 HTML 属性，用于提高 Web 应用对屏幕阅读器和其他辅助技术的可访问性。

- 例如，给一个按钮添加 `role` 属性以确保其被识别为按钮：

  ```html
  <div role="button" aria-pressed="false">点击我</div>
  ```

- ARIA 属性帮助描述页面的交互性内容，特别是当原生 HTML 标签无法胜任时，如自定义组件中的动态内容。

### 22. HTML5 中如何处理多语言支持？

使用 `lang` 属性指定文档或部分内容的语言。可以在不同的部分使用不同语言，方便 SEO 和可访问性工具正确解析页面内容。

```html
<html lang="en">
  <p lang="es">Este es un texto en español.</p>
</html>
```

### 23. **HTML 中的 `rel="preload"` 和 `rel="prefetch"` 有何区别？**

- **`rel="preload"`**：预先加载重要资源，帮助提高页面初次渲染速度，通常用于关键的 CSS、JavaScript 或字体文件。

- **`rel="prefetch"`**：提前加载用户可能在未来需要的资源，适合较低优先级的资源，例如下一个页面的图片或脚本。

  ```html
  <link rel="preload" href="style.css" as="style">
  <link rel="prefetch" href="next-page.js">
  ```

### 24. **HTML5 中如何处理 `<script>` 的阻塞问题？**

- 将 `<script>` 标签放在文档底部（`</body>` 前），避免在页面解析过程中阻塞渲染。
- 使用 `defer` 或 `async`
  - `defer`：脚本会在页面解析完毕后按顺序执行。
  - `async`：脚本会在下载完毕后立即执行，执行顺序无法保证。

### 25. **HTML5 中的 `<output>` 标签有什么用途？**

- **`<output>`** 标签用于显示计算结果，常与表单和 JavaScript 联动。

  ```html
  <form oninput="result.value=parseInt(a.value)+parseInt(b.value)">
    <input type="range" id="a" value="50"> +
    <input type="number" id="b" value="10">
    = <output name="result">60</output>
  </form>
  ```

### 26. **HTML5 的 `<mark>` 标签有什么作用？**

- **`<mark>`** 标签用于高亮文本，通常用于搜索结果或需要强调的部分内容。它有助于在页面上突出显示某些特定内容。

  ```html
  <p>这里是<mark>高亮显示</mark>的文本。</p>
  ```

### 27. **如何在 HTML 中嵌入数学公式？**

- 可以使用 MathML来嵌入数学表达式，MathML 是一种标记语言，用于显示数学公式。一些浏览器原生支持 MathML，也可以使用 JavaScript 库如 MathJax 来兼容不支持的浏览器。

  ```html
  <math>
    <msup>
      <mi>x</mi>
      <mn>2</mn>
    </msup>
    <mo>+</mo>
    <msup>
      <mi>y</mi>
      <mn>2</mn>
    </msup>
    <mo>=</mo>
    <mi>z</mi>
  </math>
  
  ```

### 28. **HTML5 中的 `autocomplete` 属性如何增强用户体验？**

- **`autocomplete`** 属性用于提示表单中的输入内容。浏览器会根据用户的历史记录和预定义数据提供输入建议。

- 使用 `autocomplete` 可以提高表单填写效率和用户体验，特别是在移动设备上。

  ```html
  <input type="text" name="email" autocomplete="email">
  ```

### 29. **什么是 Intersection Observer API？如何用它来实现懒加载？**

- **Intersection Observer** 是一种异步 API，用于检测目标元素何时进入或离开可视区域。
- 它可以用于懒加载图片或触发其他操作：

```js
let observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      let img = entry.target;
      img.src = img.dataset.src;
      observer.unobserve(img);
    }
  });
});

document.querySelectorAll('img').forEach(img => {
  observer.observe(img);
});

```

- 这样，图片只有在进入视口时才会被加载，优化了页面的性能。

### 30. **HTML5 中如何使用 `<time>` 标签？**

- **`<time>`** 标签用于标记时间或日期，常用于时间数据的展示。

  ```html
  <time datetime="2024-10-21"></time>
  ```

- 它为机器和人类提供了标准化的时间格式，有助于搜索引擎解析日期信息。