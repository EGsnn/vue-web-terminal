<template>
  <div id="app">
    <em>1111</em>
    <em>2222</em>
    <em>3333</em>
<!-- 
    <div style="width: 700px;height: 400px;margin-left: 150px;margin-top: 300px">
      <terminal
          name="test"
          show-header
          @exec-cmd="onExecCmd">
      </terminal>
    </div> -->

    <div v-for="(item,i) in terminals" :key="i">
      <terminal
          v-show="item.show"
          :name="item.name"
          :title="item.name"
          :context="item.context"
          :warn-log-count-limit="200"
          :drag-conf="item.dragConf"
          :toolsCmdList="['clear', 'loop', 'code', 'json', 'code', 'code']"
          show-header
          :push-message-before="_pushMessageBefore"
          @exec-cmd="onExecCmd"
          @on-active="onActive"
          @on-inactive="onInactive"
          style="position: fixed">
      </terminal>
    </div>
  </div>

</template>

<script>
import {api as TerminalApi, Ask as TerminalAsk, Terminal} from '../src/index.js'

export default {
  name: "App",
  components: {Terminal},
  data() {
    return {
      terminals: [
        {
          show: true,
          name: 'terminal-test',
          context: '/vue-web-terminal/test',
          dragConf: {
            width: "60%",
            height: "50%",
            zIndex: 100,
            init: {
              x: 100,
              y: 70
            },
            pinned: false
          }
        }
      ]
    }
  },
  created(){

    // setTimeout(()=>{
    //   TerminalApi.execute('terminal-test', 'clear')
    // }, 5000)

    // let message = {
    //     // tag: 'output',
    //     class: 'system',
    //     tag: ' car output',
    //     // color: '#f0f0f0',
    //     // tag: '车端返回：',
    //     contentStyle: {'color': '#ff0000'},
    //     content: 'code'
    // }
    // setTimeout(()=>{
    //   TerminalApi.pushMessage('terminal-test', message)
    // }, 3000)
  },
  methods: {
    onExecCmd(key, command, success, failed, name) {
      if (key === 'list') {
        success("hello")
      } else if (key === 'code') {
        success({
          type: 'code',
          content: "<template>\n" +
              "  <div id=\"app\">\n" +
              "    <div v-for=\"(item,i) in terminals\" :key=\"i\">\n" +
              "      <terminal\n" +
              "          v-show=\"item.show\"\n" +
              "          :name=\"item.name\"\n" +
              "          :title=\"item.name\"\n" +
              "          :context=\"item.context\"\n" +
              "          :warn-log-count-limit=\"200\"\n" +
              "          :drag-conf=\"item.dragConf\"\n" +
              "          show-header\n" +
              "          :push-message-before=\"_pushMessageBefore\"\n" +
              "          @exec-cmd=\"onExecCmd\"\n" +
              "          @on-active=\"onActive\"\n" +
              "          @on-inactive=\"onInactive\"\n" +
              "          style=\"position: fixed\">\n" +
              "      </terminal>\n" +
              "    </div>\n" +
              "  </div>\n" +
              "\n" +
              "</template>"
        })
      } else if (key === 'close') {
        let activeNext
        this.terminals.forEach(o => {
          if (o.name === name) {
            o.show = false
          }
          if (o.show) {
            activeNext = o.name
          }
        })
        if (activeNext) {
          TerminalApi.focus(activeNext, true)
        }
        success()
      } else if (key === 'loop') {
        let count = 0;
        let interval = setInterval(() => {
          if (count++ > 20) {
            clearInterval(interval)
            success()
          }
          for (let i = 0; i < 20; i++) {
            TerminalApi.pushMessage(name, {
              type: 'normal',
              class: 'system',
              tag: 'output',
              content: "random content: " + Math.random()
            });
          }
        }, 100)
      } else if (key === 'new') {
        let seq = this.terminals.length
        this.terminals.push({
          show: true,
          name: 'terminal-test-' + seq,
          context: '/vue-web-terminal/test/' + seq,
          dragConf: {
            width: "60%",
            height: "50%",
            zIndex: 100,
            init: {
              x: 100 + seq * 50,
              y: 70 + seq * 20
            }
          }
        })
        success()
      } else if (key === 'ansi') {
        let ansiContent = 'vue-wen-terminal 支持 ANSI 码的着色解码功能，但暂不支持其他的光标、设备、窗口控制等，默认会将不支持的 ANSI 码过滤。\n\n\x1B[1;34mThis are some blue text.\x1B[0m\n\x1B[30;43mThis is a line of text with a background color.\x1B[0m\n\x1B[92;5mThis is blink text.\x1B[0m\n'

        ansiContent += '\nThis is xterm-256-color content:\n'
        for (let i = 0; i < 256; i++) {
          ansiContent += ('\x1B[38;5;' + i + 'mV\x1B[0m')
        }

        ansiContent += '\n\nThis is xterm-256-color background content:\n'
        for (let i = 0; i < 256; i++) {
          ansiContent += ('\x1B[48;5;' + i + 'm \x1B[0m')
        }

        success({
          type: 'ansi',
          content: ansiContent
        })
      } else if (key === 'edit') {
        TerminalApi.textEditorOpen(name, {
          content: 'hello world!',
          onClose: (value, option) => {
            console.log("text close, value:", value, "option:", option)
          }
        })
        success()
      } else if (key === 'ask') {
        let asker = new TerminalAsk()
        success(asker)
        asker.ask({
          question: 'Ask? (y/n): ',
          autoReview: true,
          callback: value => {
            console.log("input: ", value)
            asker.finish()
          }
        })
        TerminalApi.focus()
      } else {
        failed("Unknown command: " + key)
      }
    },
    onActive(name) {
      this.terminals.forEach(o => {
        if (o.name === name) {
          o.dragConf.zIndex = 101
        }
      })
    },
    onInactive(name) {
      this.terminals.forEach(o => {
        if (o.name === name) {
          o.dragConf.zIndex = 100
        }
      })
    },
    _pushMessageBefore() {
      // console.log(message, name)
    }
  }
}
</script>

<style>
body, html, #app {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  background-color: #ffffff;
  font-family: Menlo, Consolas, monospace;
  overflow: auto;
}
</style>
