# Helpful Prompts for Tools Website

Below is a collection of prompts designed to enhance your tools website with advanced functionality, focusing on Static Site Generation (SSG) for a Lovable-built website and SEO optimization. These prompts are tailored for developers looking to improve performance, scalability, and search engine visibility.

---

## Static Site Generation (SSG) for Lovable Website

**Prompt:**  
☠️ **USE AT YOUR OWN RISK** ☠️  
In our experiments, this works best with brand-new Lovable builds and tends to break when prompted into existing Lovable projects. But it’s worth a try, just be prepared to roll back!

**Instructions:**  
1. Create your Lovable website with an initial prompt.  
   - Just go ahead and create your website/app as usual.  
2. Paste the prompt below exactly.  
3. Follow the Lovable instructions to copy/paste the new Build script into your `Packages` folder.

### Prompt Content:
-----
#IMPLEMENT SSG (STATIC SITE GENERATION) EXACTLY AS PER THE BELOW WITHOUT DEVIATION

##FOLLOWING IMPLEMENTATION PROVIDE ME WITH INSTRUCTION ON HOW TO MANUALLY REPLACE/UPDATE THE EXISTING SCRIPTS NODE IN THE PACKAGE.JSON  INCLUDING ANY TRAILING COMMAS

#Added placeholder to insert rendered content
diff --git index.html index.html
--- index.html
+++ index.html
@@ -13 +13 @@
-    <div id="root"></div>
+    <div id="root"><!--app-html--></div>

#Added prerender script to generate static HTML
#NOTE THIS MUST BE .JS AS WILL BE RUN VIA NODE
diff --git prerender.js prerender.js
new file mode 100644
--- /dev/null
+++ prerender.js
@@ -0,0 +1,35 @@
+import fs from 'node:fs'
+import path from 'node:path'
+import url from 'node:url'
+
+const __dirname = path.dirname(url.fileURLToPath(import.meta.url))
+const toAbsolute = (p) => path.resolve(__dirname, p)
+
+const template = fs.readFileSync(toAbsolute('dist/index.html'), 'utf-8')
+const { render } = await import('./dist/server/entry-server.js')
+
+const routesToPrerender = fs
+  .readdirSync(toAbsolute('src/pages'))
+  .map((file) => {
+    const name = file.replace(/\.tsx$/, '').toLowerCase()
+    return name === 'index' ? ⁠ / ⁠ : ⁠ /${name} ⁠
+  })
+
+;(async () => {
+  for (const url of routesToPrerender) {
+    const appHtml = render(url);
+    const html = template.replace(⁠ <!--app-html--> ⁠, appHtml)
+
+    const filePath = ⁠ dist${url === '/' ? '/index' : url}.html ⁠
+    fs.writeFileSync(toAbsolute(filePath), html)
+    console.log('pre-rendered:', filePath)
+  }
+})()

#Removed BrowserRouter since SSR requires StaticRouter
diff --git src/App.tsx src/App.tsx
--- src/App.tsx
+++ src/App.tsx
@@ -6 +6 @@
-import { BrowserRouter, Routes, Route } from "react-router-dom";
+import { Routes, Route } from "react-router-dom";

@@ -20,11 +20,9 @@
-      <BrowserRouter>
       <AnimatePresence mode="wait">
         <Routes>
           <Route path="/" element={<Index />} />
           <Route path="/about" element={<About />} />
           <Route path="/varieties" element={<Varieties />} />
           <Route path="*" element={<NotFound />} />
         </Routes>
       </AnimatePresence>
-      </BrowserRouter> # Moved BrowserRouter to main.tsx for conditional usage

#Added entry-server.tsx to handle SSG rendering
diff --git src/entry-server.tsx src/entry-server.tsx
new file mode 100644
--- /dev/null
+++ src/entry-server.tsx
@@ -0,0 +1,14 @@
+import ReactDOMServer from 'react-dom/server';
+import { StaticRouter } from 'react-router-dom/server';
+import App from './App';
+
+export function render(url: string) {
+  const html = ReactDOMServer.renderToString(
+    <StaticRouter location={url}>
+      <App />
+    </StaticRouter>
+  );
+  
+  return html;
+}

#Added hydration for SSG and moved BrowserRouter to main.tsx
diff --git src/main.tsx src/main.tsx
--- src/main.tsx
+++ src/main.tsx
@@ -1 +1,2 @@
-import { createRoot } from 'react-dom/client'
+import { createRoot, hydrateRoot } from 'react-dom/client'
+import { BrowserRouter } from 'react-router-dom'

@@ -5 +6,12 @@
-createRoot(document.getElementById("root")!).render(<App />);
+const root = document.getElementById("root")!;
+const app = (
+  <BrowserRouter>
+    <App />
+  </BrowserRouter>
+);
+
+# Use hydration for SSR in production
+if (import.meta.env.PROD) {
+  hydrateRoot(root, app);
+} else {
+  createRoot(root).render(app);
+}

----
### New!! Full Website SEO Prompt

After you have performed the above prompt. Deploy your website to Netlify.
Then you can paste this into Lovable:

update prerender.js so that the output html files match the routes defined in App.tsx, ensuring that any sub directories exist before writing the files

When adding new pages and blogs:

Make sure to include these in prerender.js


### Output:Please apply the SEO prompt to your Lovable project after deploying to Netlify. Update prerender.js as shown above to handle subdirectories and ensure new pages/blogs are included in the prerendering process.
