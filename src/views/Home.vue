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
        <span class="span-block" @click="disableEditor">禁用编辑器</span>
        <span class="span-block" @click="enableEditor">解除禁用</span>
         <span class="span-block" @click="insetHtml">插入html</span>
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
import HeadListMenu from "../utils/HeadListMenu";

import i18next from "i18next"; // 多语言  'zh-CN'(简体中文) 和 'en'(英文)

export default {
  name: "Home",
  components: {
    // HelloWorld
  },
  data() {
    return {
      editor: null,
    };
  },
  mounted() {
    this.setEditor();
  },
  methods: {
    setEditor() {
      this.editor = new E("#text-container"); // 传入两个元素就是菜单和编辑区域分离

      this.editor.menus.extend("alertMenu", AlertMenu); // 配置自定义菜单
      this.editor.menus.extend("HeadList", HeadListMenu);

      // //将菜单加入到editor. config . menus 中 const menuKey = ' aLertMenuKey
      //也可以通过配置menus 调整菜单的顺序，参考[配置菜单]部分的文档 editor.config.menus.push(menuKey)
      this.editor.config.menus = this.editor.config.menus.concat([
        "alertMenu",
        "HeadList",
      ]);

      // 挂载highlight插件
      this.editor.highlight = hljs;

      // 引入 i18next 插件
      this.editor.i18next = i18next;

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
          return pasteStr + "这是自定义加的文字哦";
        },

        // scroll-to-head 滚动到某个标题
        onCatalogChange: function (headList) {
          /*
              headList 格式
              [
                  { 
                      id: "eold9", // 标题 id
                      tag: "H1",
                      text: "标题文字"
                  },
                  { ... },
                  { ... }
              ]
          */
          console.log(headList);
        },

        // // 选择语言
        lang: "japan",
      };

      this.editor.config.languages["japan"] = {
        wangEditor: {
          请输入正文: "本文を入力してください",
        },
      };

      this.editor.config = {
        ...this.editor.config, // 自身的配置也要加上
        ...editorConfig,
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
    // disableEditor  禁用编辑器之后，内容只读，不可编辑。
    disableEditor() {
      this.editor.disable();
    },
    // enableEditor  解禁之后，恢复可编辑的能力。
    enableEditor() {
      this.editor.enable();
    },
    // 插入html
    insetHtml() {
      this.editor.txt.html(
        `<p><div style="text-align: center;"><span style="color: inherit;">用户注册及使用隐私协议</span></div><div style="text-align: center;"><br></div><br>在此特别提醒您（用户）在注册成为用户之前，请认真阅读本《用户协议》（以下简称“协议”），确保您充分理解本协议中各条款。请您审慎阅读并选择接受或不接受本协议。您的注册、登录、使用等行为将视为对本协议的接受，并同意接受本协议各项条款的约束。本协议约定北京依辈科技科技发展有限公司（以下简称“依辈通”）与用户之间关于“ 依辈通”软件服务（以下简称“服务“）的权利义务。“用户”是指注册、登录、使用本服务的个人。本协议可由 依辈通随时更新，更新后的协议条款一旦公布即代替原来的协议条款，恕不再另行通知，用户可在本平台中查阅最新版协议条款。在修改协议条款后，如果用户不接受修改后的条款，请立即停止使用依辈通提供的服务，用户继续使用服务将被视为接受修改后的协议。<br> <br>一、账号注册<br>1、用户在使用本服务前需要注册一个“依辈通”账号。“依辈通”账号应当使用手机号码绑定注册，请用户使用尚未与“依辈通”账号绑定的手机号码，以及未被服务根据本协议封禁的手机号码注册“依辈通”账号。服务可以根据用户需求或产品需要对账号注册和绑定的方式进行变更，而无须事先通知用户。<br><br>2、“依辈通”系基于“依辈通“的产品，用户注册时应当授权依辈通及使用其个人信息方可成功注册“依辈通”账号。故用户完成注册即表明用户同意服务提取、公开及使用用户的信息。<br><br>3、鉴于“依辈通”账号的绑定注册方式，您同意服务在注册时将允许您的手机号码及手机设备识别码等信息用于注册。<br><br>4、在用户注册及使用本服务时，依辈通需要搜集能识别用户身份的个人信息以便服务可以在必要时联系用户，或为用户提供更好的使用体验。依辈通搜集的信息包括但不限于用户的姓名、地址；依辈通同意对这些信息的使用将受限于第三条用户个人隐私信息保护的约束。<br><br> <br>二、用户个人隐私信息保护<br>1、如果依辈通发现或收到他人举报或投诉用户违反本协议约定的，依辈通有权不经通知随时对相关内容，包括但不限于用户资料、发贴记录进行审查、删除，并视情节轻重对违规账号处以包括但不限于警告、账号封禁 、设备封禁 、功能封禁 的处罚，且通知用户处理结果。<br>2、因违反用户协议被封禁的用户，可以自行与依辈通联系。其中，被实施功能封禁的用户会在封禁期届满后自动恢复被封禁功能。被封禁用户可提交申诉，依辈通将对申诉进行审查，并自行合理判断决定是否变更处罚措施。<br>3、用户理解并同意，依辈通有权依合理判断对违反有关法律法规或本协议规定的行为进行处罚，对违法违规的任何用户采取适当的法律行动，并依据法律法规保存有关信息向有关部门报告等，用户应承担由此而产生的一切法律责任。<br>4、用户理解并同意，因用户违反本协议约定，导致或产生的任何第三方主张的任何索赔、要求或损失，包括合理的律师费，用户应当赔偿依辈通与合作公司、关联公司，并使之免受损害。<br> <br>三、用户发布内容规范<br>1、本条所述内容是指用户使用服务的过程中所制作、上载、复制、发布、传播的任何内容，包括但不限于账号头像、名称、用户说明等注册信息及认证资料，或文字、语音、图片、视频、图文等发送、回复或自动回复消息和相关链接页面，以及其他使用账号或本服务所产生的内容。<br>2、用户不得利用“依辈通”账号或本服务制作、上载、复制、发布、传播如下法律、法规和政策禁止的内容：<br>(1) 反对宪法所确定的基本原则的；<br>(2) 危害国家安全，泄露国家秘密，颠覆国家政权，破坏国家统一的；<br>(3) 损害国家荣誉和利益的；<br>(4) 煽动民族仇恨、民族歧视，破坏民族团结的；<br>(5) 破坏国家宗教政策，宣扬邪教和封建迷信的；<br>(6) 散布谣言，扰乱社会秩序，破坏社会稳定的；<br>(7) 散布淫秽、色情、赌博、暴力、凶杀、恐怖或者教唆犯罪的；<br>(8) 侮辱或者诽谤他人，侵害他人合法权益的；<br>(9) 含有法律、行政法规禁止的其他内容的信息。<br>3、用户不得利用“依辈通”账号或本服务制作、上载、复制、发布、传播如下干扰“服务”正常运营，以及侵犯其他用户或第三方合法权益的内容：<br>(1) 含有任何性或性暗示的；<br>(2) 含有辱骂、恐吓、威胁内容的；<br>(3) 含有骚扰、垃圾广告、恶意信息、诱骗信息的；<br>(4) 涉及他人隐私、个人信息或资料的；<br>(5) 侵害他人名誉权、肖像权、知识产权、商业秘密等合法权利的；<br>(6) 含有其他干扰本服务正常运营和侵犯其他用户或第三方合法权益内容的信息。<br> <br>四、使用规则<br>1、用户在本服务中或通过本服务所传送、发布的任何内容并不反映或代表，也不得被视为反映或代表依辈通的观点、立场或政策，依辈通对此不承担任何责任。<br>2、用户不得利用“依辈通”账号或本服务进行如下行为：<br>(1) 提交、发布虚假信息，或盗用他人头像或资料，冒充、利用他人名义的；<br>(2) 强制、诱导其他用户关注、点击链接页面或分享信息的；<br>(3) 虚构事实、隐瞒真相以误导、欺骗他人的；<br>(4) 利用技术手段批量建立虚假账号的；<br>(5) 利用“依辈通”账号或本服务从事任何违法犯罪活动的；<br>(6) 制作、发布与以上行为相关的方法、工具，或对此类方法、工具进行运营或传播，无论这些行为是否为商业目的；<br>(7) 其他违反法律法规规定、侵犯其他用户合法权益、干扰“依辈通”正常运营或服务未明示授权的行为。<br>3、用户须对利用“依辈通”账号或本服务传送信息的真实性、合法性、无害性、准确性、有效性等全权负责，与用户所传播的信息相关的任何法律责任由用户自行承担，与依辈通无关。<br>如因此给依辈通或第三方造成损害的，用户应当依法予以赔偿。<br>4、依辈通提供的服务中可能包括广告，用户同意在使用过程中显示依辈通和第三方供应商、合作伙伴提供的广告。除法律法规明确规定外，用户应自行对依该广告信息进行的交易负责，<br>对用户因依该广告信息进行的交易或前述广告商提供的内容而遭受的损失或损害，依辈通不承担任何责任。<br> <br>五、其他<br>1、依辈通郑重提醒用户注意本协议中免除依辈通责任和限制用户权利的条款，请用户仔细阅读，自主考虑风险。未成年人应在法定监护人的陪同下阅读本协议。<br>2、本协议的效力、解释及纠纷的解决，适用于中华人民共和国法律。若用户和依辈通之间发生任何纠纷或争议，首先应友好协商解决，协商不成的，用户同意将纠纷或争议提交依辈通住所地有管辖权的人民法院管辖。<br>3、本协议的任何条款无论因何种原因无效或不具可执行性，其余条款仍有效，对双方具有约束力。<br><br><br><br>本《协议》版权由依辈通所有，依辈通保留一切对本《协议》解释的权利。<br><br>北京依辈科技科技发展有限公司</p><p><br></p>`
      );
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
