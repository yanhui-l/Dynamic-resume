<template>
  <div id="app">
    <StyleEditor ref="styleEditor" :code="currentStyle"></StyleEditor>
    <ResumeEditor ref="resumeEditor" :markdown="currentMarkdown" :enableHtml="enableHtml"></ResumeEditor>
  </div>
</template>

<script>
import StyleEditor from "./components/StyleEditor";
import ResumeEditor from "./components/ResumeEditor";
import "./assets/reset.css";

export default {
  name: "app",
  components: {
    StyleEditor,
    ResumeEditor
  },
  data() {
    return {
      // 动画延时
      interval: 3,
      currentStyle: "",
      enableHtml: false,
      fullStyle: [
        `/*

* 源码地址 https://github.com/yanhui-l/Dynamic-resume
* 你好呀，我是李颜回。
* 我来写一份简历！
*/

/* 给所有元素加上过渡效果 */
* {
  transition: all .1s;
}
/* 设置背景颜色 */
html {
  color: rgb(222,222,222); background: rgb(0,43,54);
}

/* 设置边框 */
.styleEditor {
  padding: .5em;
  border: 1px solid;
  margin: .5em;
  overflow: auto;
  width: 45vw; height: 90vh;
  /* background: rgb(20,20,20); */
}
/* 代码高亮 */
.token.selector{ color: rgb(133,153,0); }
.token.property{ color: rgb(187,137,0); }
.token.punctuation{ color: yellow; }
.token.function{ color: rgb(42,161,152); }

/* 加3D效果 */
html{
  perspective: 1000px;
}
.styleEditor {
  position: fixed; left: 0; top: 0;
  -webkit-transition: none;
  transition: none;
  -webkit-transform: rotateY(10deg) translateZ(-100px) ;
          transform: rotateY(10deg) translateZ(-100px) ;
}

/* 准备一个编辑器 */
.resumeEditor{
  position: fixed; right: 0; top: 0;
  padding: .5em;  margin: .5em;
  width: 48vw; height: 90vh;
  border: 1px solid;
  background: white; color: #222;
  overflow: auto;
}
/* 开始写简历 */
`,
        `
/*将Markdown格式翻译成HTML
 *再对HTML加点样式
*/
.resumeEditor{
  padding: 2em;
}
.resumeEditor h2{
  display: inline-block;
  border-bottom: 1px solid;
  margin: 1em 0 .5em;
}
.resumeEditor ul,.resumeEditor ol{
  list-style: none;
}
.resumeEditor ul> li::before{
  content: '•';
  margin-right: .5em;
}
.resumeEditor ol {
  counter-reset: section;
}
.resumeEditor ol li::before {
  counter-increment: section;
  content: counters(section, ".") " ";
  margin-right: .5em;
}
.resumeEditor blockquote {
  margin: 1em;
  padding: .5em;
  background: #ddd;
}
`
      ],
      currentMarkdown: "",
      fullMarkdown: `
李颜回
----
21岁，Java软件工程师，一年Java EE 从业经验，目前在深圳工作。
有良好的文档编写和代码书写规范，能独立解决问题、百折不挠、细节控
<br/>


技能
----
* 后端开发
* 前端开发
* 权限管理
* 公众号/小程序开发
* API接口开发
* Linux
* 博客系统

技术及语言
----
  - **Java**: SpringBoot、SpringMVC、MyBatis、Shiro、JWT
  - **前端**: VueJs、Bootstrap、LayUI、jQuery UI
  - **数据库**: MySQL、SQLServer、Oracle、redis
  - **web 服务器**: Nginx、Tomcat
  - **OS**: Linux、Windows
  - **Others**: Git、Svn、Maven、Visio、IDEA、Webstorm

工作经历
----
1. [江西仁翔药业](http://pc.jxrxyy.com/)
2. [深圳市路波科技有限公司](https://loobot.com/)

个人项目
----

1. [基于VuePress技术博客网站](https://yanhui.siyuh.com/)
2. [自制抖音去水印小程序](https://github.com/yanhui-l/DouYinDeWater)
2. [一个会动的简历](https://yanhui.ink/)

链接
----
* [技术博客-多放香菜](https://yanhui.siyuh.com/)
* [GitHub](https://github.com/yanhui-l)
* [CSDN](https://blog.csdn.net/weixin_42182713?spm=1000.2115.3001.5343)

联系我
----
* 联系QQ：**2762722103** | 微信：**18370292063**
* 主要涉及技术：**Java后端开发**、**VUE前端开发**、**小程序开发**、**Linux**

> 如果你喜欢这个效果，Fork [github](https://github.com/yanhui-l/Dynamic-resume)，打造你自己的简历！
`
    };
  },
  created() {
    this.makeResume();
  },

  methods: {
    makeResume: async function() {
      await this.progressivelyShowStyle(0);
      await this.progressivelyShowResume();
      await this.progressivelyShowStyle(1);
      await this.showHtml();
      await this.progressivelyShowStyle(2);
    },
    showHtml() {
      return new Promise((resolve, reject) => {
        this.enableHtml = true;
        resolve();
      });
    },
    progressivelyShowStyle(n) {
      return new Promise((resolve, reject) => {
        let interval = this.interval;
        let showStyle = async function() {
          let style = this.fullStyle[n];
          if (!style) {
            return;
          }
          // 计算前 n 个 style 的字符总数
          let length = this.fullStyle
            .filter((_, index) => index <= n)
            .map(item => item.length)
            .reduce((p, c) => p + c, 0);
          let prefixLength = length - style.length;
          if (this.currentStyle.length < length) {
            let l = this.currentStyle.length - prefixLength;
            let char = style.substring(l, l + 1) || " ";
            this.currentStyle += char;
            if (style.substring(l - 1, l) === "\n" && this.$refs.styleEditor) {
              this.$nextTick(() => {
                this.$refs.styleEditor.goBottom();
              });
            }
            setTimeout(showStyle, interval);
          } else {
            resolve();
          }
        }.bind(this);
        showStyle();
      });
    },
    progressivelyShowResume() {
      return new Promise((resolve, reject) => {
        let length = this.fullMarkdown.length;
        let interval = this.interval;
        let showResume = () => {
          if (this.currentMarkdown.length < length) {
            this.currentMarkdown = this.fullMarkdown.substring(
              0,
              this.currentMarkdown.length + 1
            );
            let lastChar = this.currentMarkdown[
              this.currentMarkdown.length - 1
            ];
            let prevChar = this.currentMarkdown[
              this.currentMarkdown.length - 2
            ];
            if (prevChar === "\n" && this.$refs.resumeEditor) {
              this.$nextTick(() => this.$refs.resumeEditor.goBottom());
            }
            setTimeout(showResume, interval);
          } else {
            resolve();
          }
        };
        showResume();
      });
    }
  }
};
</script>

<style scoped>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

html {
  min-height: 100vh;
}

* {
  box-sizing: border-box;
}
</style>
