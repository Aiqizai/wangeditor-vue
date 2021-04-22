# wang-editor-vue
```
git clone https://github.com/Aiqizai/wangeditor-vue.git

cd wangeditor-vue

npm run serve
```
## wangEditor —— 轻量级 web 富文本编辑器，配置方便，使用简单。
```
官网：www.wangEditor.com
文档：doc.wangEditor.com
源码：github.com/wangeditor-team/wangEditor （欢迎 star）
```
## 基本使用
### NPM
```
npm i wangeditor --save
安装后几行代码即可创建一个编辑器：

import E from "wangeditor"
const editor = new E("#div1")
editor.create()
```
### CDN
```
<script
  type="text/javascript"
  src="https://cdn.jsdelivr.net/npm/wangeditor@latest/dist/wangEditor.min.js"></script>
<script type="text/javascript">
  const E = window.wangEditor
  const editor = new E("#div1")
  // 或者 const editor = new E(document.getElementById('div1'))
  editor.create()
</script>
```
### 使用 js 外链引入
```
<div id="div1">
    <p>欢迎使用 <b>wangEditor</b> 富文本编辑器</p>
</div>

<!-- 引入 wangEditor.min.js -->
<script type="text/javascript">
    const E = window.wangEditor
    const editor = new E('#div1')
    // 或者 const editor = new E( document.getElementById('div1') )
    editor.create()
</script>
```
