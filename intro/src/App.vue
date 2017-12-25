<template>
  <div id="app">
    <StyleEditor ref="styleEditor" :code="currentStyle"></StyleEditor>
    <ResumeEditor ref="resumeEditor" :markdown="currentMarkdown" :enableHtml="enableHtml"></ResumeEditor>
  </div>
</template>

<script>
  import StyleEditor from './components/StyleEditor'
  import ResumeEditor from './components/ResumeEditor'
  import './assets/reset.css'
  export default {
    name: 'app',
    components: {
      StyleEditor,
      ResumeEditor
    },
    data() {
      return {
        interval: 40,
        currentStyle: '',
        enableHtml: false,
        fullStyle: [
          `
`,
          `

`],
        currentMarkdown: '',
        fullMarkdown: `

Hello，
我是 Bryce (゜-゜)つロ 干杯~
=============
* 来自 广西 | 北海
* 现居 福建 | 厦门
* 追求新奇的技术
* 梦想成为一位伟大的 Web 攻城狮

* * *
---- 兴趣爱好 ----
---------------

- 天生爱跑
- 脚步不停地追逐梦想
- 长板滑行
- 寻求更刺激的生活方式
- 热爱健身
- 更好的体魄拥抱更好的未来

* * *
---- 座右铭 ----
---------------
> Let them keep living in the darkness and we'll keep walking in the sunlight.

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
        // await this.progressivelyShowStyle(2)
      },
      showHtml: function () {
        return new Promise((resolve, reject) => {
          this.enableHtml = true
          resolve()
        })
      },
      progressivelyShowStyle(n) {
        return new Promise((resolve, reject) => {
            let interval = this.interval //定时
            let showStyle = (async function () {
                let style = this.fullStyle[n] //获取当前需要展示的style内容
                if (!style) { return }
                // 计算前 n 个 style 的字符总数
                let length = this.fullStyle.filter((_, index) => index <= n) //过滤出前 N 个
                                            .map((item) => item.length) //分别计算每个 style 的总字数长度
                                            .reduce((p, c) => p + c, 0) //累加长度
                let prefixLength = length - style.length
                if (this.currentStyle.length < length) { //判断当前输出的内容长度是否超过了需要展示内容的总长度
                    let l = this.currentStyle.length - prefixLength
                    //文字逐字增加
                    let char = style.substring(l, l + 1) || ' '
                    this.currentStyle += char
                    if (style.substring(l - 1, l) === '\n' && this.$refs.styleEditor) {   //  \n  换行     this.$refs ：一个对象，持有已注册过 ref 的所有子组件。
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
          let showResume = () => {
            if (this.currentMarkdown.length < length) {
              this.currentMarkdown = this.fullMarkdown.substring(0, this.currentMarkdown.length + 1)
              let lastChar = this.currentMarkdown[this.currentMarkdown.length - 1]
              let prevChar = this.currentMarkdown[this.currentMarkdown.length - 2]
              if (prevChar === '\n' && this.$refs.resumeEditor) {
                this.$nextTick(() => this.$refs.resumeEditor.goBottom())
              }
              setTimeout(showResume, interval)
            } else {
              resolve()
            }
          }
          showResume()
        })
      }
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
