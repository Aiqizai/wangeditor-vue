<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <!-- <div id="editorWrapper">
      <p>欢迎使用 <b>wangEditor</b> 富文本编辑器</p>
    </div> -->
    <div>
      <!-- <div id="toolbar-container" class="toolbar"></div> -->
      <p style="padding: 0 10px; color: #999">
        <span>中间隔离带</span>
        <span class="span-block" @click="getText">获取Text</span>
        <span class="span-block" @click="clearContain">清空内容</span>
      </p>
      <div id="text-container" class="text">
        <!-- <p>这是初始化的内容</p> -->
      </div>
      <!-- <textarea id="text1" style="width:100%; height:200px;"></textarea>  textarea -->
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue'
import E from "wangeditor";

// 挂载 highlight
import hljs from "highlight.js";
// highlight css样式文件
import "highlight.js/styles/monokai-sublime.css";

import AlertMenu from "../utils/AlertMenu";

export default {
  name: "Home",
  components: {
    // HelloWorld
  },
  data() {
    return {
      editor: "",
    };
  },
  mounted() {
    this.setEditor();
  },
  methods: {
    setEditor() {
      this.editor = new E("#text-container"); // 传入两个元素就是菜单和编辑区域分离

      this.editor.menus.extend("alertMenu", AlertMenu); // 配置自定义菜单

      // //将菜单加入到editor. config . menus 中 const menuKey = ' aLertMenuKey
      //也可以通过配置menus 调整菜单的顺序，参考[配置菜单]部分的文档 editor.config.menus.push(menuKey)
      this.editor.config.menus = this.editor.config.menus.concat("alertMenu");

      // 挂载highlight插件
      this.editor.highlight = hljs;

      console.log("editor ==> ", this.editor);

      let editorConfig = {
        // 设置编辑区域高度为 500px
        height: 500,
        // 修改placeholder内容
        placeholder: "请输入内容...",

        // 配置历史记录
        // 默认情况下，只有 IE 和 旧版 Edge 会使用兼容模式，如果需要在其它浏览器上也使用兼容模式，可以在函数内进行判定
        compatibleMode: function () {
          // 返回 true 表示使用兼容模式；返回 false 使用标准模式
          return true;
        },

        // 当我们使用兼容模式的时候，可以自定义记录的频率，默认 200 ms
        onchangeTimeout: 500, // 修改为 500 ms

        // 还可以修改历史记录的步数。默认 30 步
        historyMaxSize: 50, // 修改为 50 步
        // 配置历史记录 end

        // 配置 server 接口地址
        // this.editor.config.uploadImgServer = '/upload-img';

        // 可使用 base64 格式保存图片。即，可选择本地图片，编辑器用 base64 格式显示图片。
        // 【注意】uploadImgShowBase64（base64 格式）和 uploadImgServer（上传图片到服务器）两者不能同时使用！！！
        uploadImgShowBase64: true,

        uploadImgHooks: {
          // 上传图片之前
          before: function (xhr) {
            console.log(xhr);

            // 可阻止图片上传
            // return {
            //   prevent: true,
            //   msg: "需要提示给用户的错误信息",
            // };
          },
          // 图片上传并返回了结果，图片插入已成功
          success: function (xhr) {
            console.log("success", xhr);
          },
          // 图片上传并返回了结果，但图片插入时出错了
          fail: function (resData) {
            console.log("fail", resData);
          },
          // 上传图片出错，一般为 http 请求的错误
          error: function (xhr, resData) {
            console.log("error", xhr, resData);
          },
          // 上传图片超时
          timeout: function () {
            console.log("timeout");
          },
          // 图片上传并返回了结果，想要自己把图片插入到编辑器中
          // 例如服务器端返回的不是 { errno: 0, data: [...] } 这种格式，可使用 customInsert
          customInsert: function (insertImgFn, result) {
            // result 即服务端返回的接口
            console.log("customInsert", result);

            // insertImgFn 可把图片插入到编辑器，传入图片 src ，执行函数即可
            insertImgFn(result.data[0]);
          },
        },

        // 粘贴样式的过滤 默认打开 true / 可选 false
        pasteFilterStyle: true,

        // 自定义处理粘贴的文本内容
        pasteTextHandle: function (pasteStr) {
            // 对粘贴的文本进行处理，然后返回处理后的结果
            return pasteStr + '这是自定义加的文字哦'
        },

      };

      this.editor.config = {
        ...this.editor.config,  // 自身的配置也要加上
        ...editorConfig
      };
      // 注意，先配置，再执行 create()
      this.editor.create();
    },
    // 获取text
    getText() {
      let text = this.editor.txt.text();
      console.log("text ==>", text);
    },
    // 清空编辑器内容
    clearContain() {
      this.editor.txt.clear();
    },
  },
  beforeDestroy() {
    // 调用销毁 API 对当前编辑器实例进行销毁
    this.editor.destroy();
    this.editor = null;
  },
};
</script>

<style scoped>
.toolbar {
  border: 1px solid #ccc;
}

.text {
  border: 1px solid #ccc;
  min-height: 400px;
}

.span-block {
  display: inline-block;
  margin-left: 10px;
  padding: 4px;
  font-size: 12px;
  background-color: #eee;
  /* color: #fff; */
  border: 1px solid #eee;
  border-radius: 4px;
  cursor: pointer;
}

.span-block:hover {
  background-color: #ddd;
}
</style>
