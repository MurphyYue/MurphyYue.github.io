<template>
  <div id="app">
    <StyleEditor ref="styleEditor" :code="currentStyle"></StyleEditor>
    <ResumeEditor :markdown="currentMarkdown" :enableHtml="enableHtml" ref="resumeEditor"></ResumeEditor>
  </div>
</template>

<script>
import StyleEditor from './components/StyleEditor'
import ResumeEditor from './components/ResumeEditor'
import './assets/reset.css'

export default {
  name: 'app',
  data() {
    return {
      interval: 40,
      currentStyle: '',
      enableHtml: false,
      fullStyle: [
        `/* 大家好，我是岳铭飞
* 年底了，想换个新环境
* 先写一份简历吧！
*/

/* 首先给所有元素加上过渡效果 */
* {
  transition: all .3s;
}
/* 白色背景太单调了，我们来点背景 */
html {
  color: rgb(222,222,222); background: rgb(0,43,54);
}
/* 文字离边框太近了 */
.styleEditor {
  padding: .5em;
  border: 1px solid;
  margin: .5em;
  overflow: auto;
  width: 45vw; height: 90vh;
}
/* 代码高亮 */
.token.selector{ color: rgb(133,153,0); }
.token.property{ color: rgb(187,137,0); }
.token.punctuation{ color: yellow; }
.token.function{ color: rgb(42,161,152); }

/* 加点 3D 效果呗 */
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

/* 接下来我给自己准备一个编辑器 */
.resumeEditor{
  position: fixed; right: 0; top: 0;
  padding: .5em;  margin: .5em;
  width: 48vw; height: 90vh;
  border: 1px solid;
  background: white; color: #222;
  overflow: auto;
}
/* 好了，我开始写简历了 */`,
        `
/* 这个简历好像差点什么
 * 对了，这是 Markdown 格式的，我需要变成对 HR 更友好的格式
 * 简单，用开源工具翻译成 HTML 就行了
 */
`,
        `
/* 再对 HTML 加点样式 */
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
`],
      currentMarkdown: '',
      fullMarkdown: `岳铭飞
----
* 年龄 26
* 籍贯 河北
* 电话 15600109959

求职意愿
----
web前端工程师


技能
----

* html+css+javascript(包括es5和es6)
* bootstrap
* jQuery vueJS
* ajax json
* webpack

工作经历
----

中测新图（北京）遥感技术有限责任公司
——航空遥感技术国家测绘地理信息局重点实验室
——地理信息应用研究中心
——web前端开发


> 如果你喜欢这个效果，Fork [我的项目](https://github.com/MurphyYue/MurphyYue.github.io)，打造你自己的简历！

`
    }
  },
  created() {
    this.makeResume()
  },
  methods: {
    makeResume: async function () {
      await this.progressivelyShowStyle(0)
      await this.progressivelyShowResume()
      await this.progressivelyShowStyle(1)
      await this.showHtml()
      await this.progressivelyShowStyle(2)
    },
    showHtml: function () {
      return new Promise((resolve, reject) => {
        this.enableHtml = true
        resolve()
      })
    },
    progressivelyShowStyle(n) {
      return new Promise((resolve, reject) => {
        console.log(n)
        let interval = this.interval
        let showStyle = (async function () {
          let style = this.fullStyle[n]
          if (!style) { return }
          // 计算前 n 个style 的字符总数
          let length = this.fullStyle.filter((_, index) => index <= n).map((item) => item.length).reduce((a, c) => a + c, 0)
          // 计算前 n-1 个style 的字符总数
          let preLength = length - this.fullStyle[n].length
          if (this.currentStyle.length < length) {
            let l = this.currentStyle.length - preLength.length
            let char = style.substring(l, l + 1) || ' '
            this.currentStyle += char
            if (style.substring(l - 1, l) === '\n' && this.refs.styleEditor) {
              this.$nextTick(() => {
                this.$refs.styleEditor.goBottom()
              })
            }
            setTimeout(showStyle, interval)
          } else {
            resolve()
          }
        }).bind(this)
        showStyle()
      })
    },
    progressivelyShowResume() {
      return new Promise((resolve, reject) => {
        let length = this.fullMarkdown.length
        let interval = this.interval
        let showResum = () => {
          if (this.currentMarkdown.length < length) {
            this.currentMarkdown += this.fullMarkdown.substring(0, this.currentMarkdown.length + 1)
            let preLast = this.currentMarkdown[this.currentMarkdown.length - 2]
            if (preLast === '/n' && this.$refs.resumeEditor) {
              this.$nextTick(() => {
                this.$refs.resumeEditor.goBottom()
              })
            }
            setTimeout(showResum, interval)
          } else {
            resolve()
          }
        }
        showResum()
      })
    }
  },
  components: {
    ResumeEditor,
    StyleEditor
  }
}
</script>

<style scoped>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  html {
    min-height: 100vh;
  }
  *{
    box-sizing: border-box;
  }
</style>
