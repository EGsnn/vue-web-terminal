<template>
  <div :class="'t-container ' + (_isActive() ? '' : 't-disable-select')"
       :style="_getContainerStyle()"
       ref="terminalContainer">
    <div v-if="_draggable()">
      <div class="t-point t-point-lt" ref="resizeLT"></div>
      <div class="t-point t-point-rt" ref="resizeRT"></div>
      <div class="t-point t-point-lb" ref="resizeLB"></div>
      <div class="t-point t-point-rb" ref="resizeRB"></div>
    </div>

    <div class="terminal">
      <div class="t-header-container" ref="terminalHeader" v-if="showHeader"
           :style="_draggable() ? 'cursor: move;' : ''" @dblclick="_fullscreen">
        <slot name="header">
          <t-header :title="title"></t-header>
        </slot>
      </div>

      <div class="terminal-tools blur-terminal">
        <slot name="tools">
          <div class="tools-box">
            <button class="blur-terminal" @click="execute('clear')">
              <svg t="1694153833185" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="2605" width="16" height="16"><path d="M96 320a32 32 0 1 1 0-64h832a32 32 0 0 1 0 64H96z m736 0h64v448a160 160 0 0 1-160 160H288a160 160 0 0 1-160-160V320h64v96H128v-96h64v448a96 96 0 0 0 96 96h448a96 96 0 0 0 96-96V320z m-512 112a32 32 0 0 1 64 0v320a32 32 0 0 1-64 0v-320z m320 0a32 32 0 0 1 64 0v320a32 32 0 0 1-64 0v-320zM288 256H224V192a96 96 0 0 1 96-96h384a96 96 0 0 1 96 96v64h-64V224h64v32h-64V192a32 32 0 0 0-32-32H320a32 32 0 0 0-32 32v64z" p-id="2606" fill="#cccccc"></path></svg>
              <!-- <svg t="1694153482535" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="4031" width="24" height="24"><path d="M768 384c-19.2 0-32 12.8-32 32l0 377.6c0 25.6-19.2 38.4-38.4 38.4L326.4 832c-25.6 0-38.4-19.2-38.4-38.4L288 416C288 396.8 275.2 384 256 384S224 396.8 224 416l0 377.6c0 57.6 44.8 102.4 102.4 102.4l364.8 0c57.6 0 102.4-44.8 102.4-102.4L793.6 416C800 396.8 787.2 384 768 384z" fill="#cccccc" p-id="4032"></path><path d="M460.8 736l0-320C460.8 396.8 448 384 435.2 384S396.8 396.8 396.8 416l0 320c0 19.2 12.8 32 32 32S460.8 755.2 460.8 736z" fill="#cccccc" p-id="4033"></path><path d="M627.2 736l0-320C627.2 396.8 608 384 588.8 384S563.2 396.8 563.2 416l0 320C563.2 755.2 576 768 588.8 768S627.2 755.2 627.2 736z" fill="#cccccc" p-id="4034"></path><path d="M832 256l-160 0L672 211.2C672 166.4 633.6 128 588.8 128L435.2 128C390.4 128 352 166.4 352 211.2L352 256 192 256C172.8 256 160 268.8 160 288S172.8 320 192 320l640 0c19.2 0 32-12.8 32-32S851.2 256 832 256zM416 211.2C416 198.4 422.4 192 435.2 192l153.6 0c12.8 0 19.2 6.4 19.2 19.2L608 256l-192 0L416 211.2z" fill="#cccccc" p-id="4035"></path></svg> -->
            </button>
            <div class="serach-box">
              <input class="blur-terminal" v-model="searchText" @input="search" type="text">
              <span class="find-text-num">{{(indexNum + (findTextNum > 0 ? 1 : 0))+'/'+ findTextNum}}</span>
              <div class="group">
                <button @click="prev">
                  <svg t="1694153803532" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="2149" width="16" height="16"><path d="M910.222222 796.444444c-17.066667 0-34.133333-5.688889-45.511111-17.066666L551.822222 409.6c-11.377778-5.688889-17.066667-11.377778-34.133333-11.377778-5.688889 0-22.755556 5.688889-28.444445 11.377778l-329.955555 364.088889c-22.755556 22.755556-56.888889 22.755556-79.644445 5.688889-22.755556-22.755556-22.755556-56.888889-5.688888-79.644445l329.955555-364.088889c28.444444-34.133333 73.955556-51.2 119.466667-51.2s85.333333 22.755556 119.466666 56.888889l312.888889 364.088889c22.755556 22.755556 17.066667 56.888889-5.688889 79.644445-11.377778 5.688889-28.444444 11.377778-39.822222 11.377777z" fill="#cccccc" p-id="2150"></path></svg>
                </button>
                <button @click="next">
                  <svg t="1694153741928" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="1923" width="16" height="16"><path d="M517.688889 796.444444c-45.511111 0-85.333333-17.066667-119.466667-51.2L73.955556 381.155556c-22.755556-22.755556-17.066667-56.888889 5.688888-79.644445 22.755556-22.755556 56.888889-17.066667 79.644445 5.688889l329.955555 364.088889c5.688889 5.688889 17.066667 11.377778 28.444445 11.377778s22.755556-5.688889 34.133333-17.066667l312.888889-364.088889c22.755556-22.755556 56.888889-28.444444 79.644445-5.688889 22.755556 22.755556 28.444444 56.888889 5.688888 79.644445L637.155556 739.555556c-28.444444 39.822222-68.266667 56.888889-119.466667 56.888888 5.688889 0 0 0 0 0z" fill="#cccccc" p-id="1924"></path></svg>
                </button>
                <button @click="searchText='';search()">
                  <svg t="1694154171415" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="2849" width="12" height="12"><path d="M511.232 438.8352L112.9984 40.6016A51.2 51.2 0 0 0 40.6016 112.9984L438.784 511.232 40.6016 909.4656a51.2 51.2 0 1 0 72.3968 72.448l398.2336-398.2848 398.2336 398.2848a51.2 51.2 0 1 0 72.448-72.448l-398.2848-398.2336 398.2848-398.2336A51.2 51.2 0 0 0 909.4656 40.6016L511.232 438.784z" fill="#cccccc" p-id="2850"></path></svg>
                </button>
                </div>
            </div>
            <div class="group cmd-btn-box">
              <button v-for="(item, index) in toolsCmdList" :key="item + index" @click="execute(item)">F{{index+1}}</button>
            </div>
            <span class="terminal-log-count">共{{terminalLog.length}}条</span>

          </div>
        </slot>
      </div>
<!-- 
      <div class="t-window" id="terminal-window" :style="`${showHeader ? 'height:calc(100% - 74px);margin-top: 74px;' : 'height:100%'}`"
           ref="terminalWindow" @click="_focus" @dblclick="_focus(true)"> -->
      <div class="t-window" id="terminal-window"  ref="terminalWindow" @click="_focus" @dblclick="_focus(true)">
        <div class="t-log-box" v-for="(item,idx) in terminalLog" v-bind:key="idx">
          <div class="t-log-msg-box">
            <span v-if="item.type === 'cmdLine'" class="t-crude-font t-cmd-line right-box">
                <span class="prompt t-cmd-line-content"><span v-html="item.content"></span></span>
            </span>
            <div class="msg-list-box" :ref="item.name" v-else>
              <div class="left-box "  v-for="(childItem, idx1) in item.msgList" :key="idx1">
                <span v-if="childItem.type === 'normal'">
                  <slot name="normal" :message="childItem">
                    <t-view-normal :item="childItem" :searchText="searchText"></t-view-normal>
                  </slot>
                </span>
                <div v-else-if="childItem.type === 'json'">
                  <slot name="json" :message="childItem">
                    <t-view-json :item="childItem" :idx="idx"></t-view-json>
                  </slot>
                </div>
                <div v-else-if="childItem.type === 'code'">
                  <slot name="code" :message="childItem">
                    <t-view-code :item="childItem" :idx="idx"></t-view-code>
                  </slot>
                </div>
                <div v-else-if="childItem.type === 'table'">
                  <slot name="table" :message="childItem">
                    <t-view-table :item="childItem" :idx="idx"></t-view-table>
                  </slot>
                </div>
                <div v-else-if="childItem.type === 'html'">
                  <slot name="html" :message="childItem">
                    <div v-html="childItem.content"></div>
                  </slot>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div v-if="flash.open && flash.content">
          <slot name="flash" :content="flash.content">
            <div v-html="flash.content"></div>
          </slot>
        </div>
        <div v-if="ask.open && ask.question">
          <div v-html="ask.question" style="display: inline-block"></div>
          <input :type="ask.isPassword ? 'password' : 'text'"
                 ref="terminalAskInput"
                 v-model="ask.input"
                 class="t-ask-input"
                 autocomplete="off"
                 auto-complete="new-password"
                 @keyup.enter="_onAskInput">
        </div>
        <slot name="helpCmd" :item="searchCmdResult.item">
          <p class="t-help-msg">
            {{ searchCmdResult.item ? searchCmdResult.item.usage : '' }}
          </p>
        </slot>
      </div>
      <div class="cmd-input-box">
        <!-- <p class="t-last-line t-crude-font t-cmd-line" ref="terminalInputBox" v-show="showInputLine"> -->
        <p class="t-last-line t-crude-font t-cmd-line" ref="terminalInputBox">
          <!-- <span class="prompt t-cmd-line-content t-disable-select" ref="terminalInputPrompt">
            <span>{{ context }}</span>
            <span> > </span>
          </span> -->
          <span class="t-cmd-line-content" v-html="_commandFormatter(command)"></span>
          <span
            v-show="cursorConf.show" class="cursor t-disable-select" ref="terminalCursor"
            :style="`width:${cursorConf.width}px;left:${cursorConf.left};top:${cursorConf.top};`">&nbsp;</span>
          <input type="text" autofocus="autofocus" v-model="command"
                  class="t-cmd-input"
                  ref="terminalCmdInput"
                  autocomplete="off"
                  auto-complete="new-password"
                  @keydown="_onInputKeydown"
                  @keyup="_onInputKeyup"
                  @input="_onInput"
                  @focusin="cursorConf.show = true"
                  @keyup.up.exact="_switchPreCmd"
                  @keyup.down.exact="_switchNextCmd"
                  @keyup.enter="_execute">
          <span class="t-flag t-cmd-line t-disable-select">
            <span class="t-cmd-line-content" ref="terminalEnFlag">a</span>
            <span class="t-cmd-line-content" ref="terminalCnFlag">你</span>
          </span>
        </p>
      </div>

    </div>
    <div v-if="enableExampleHint">
      <slot name="helpBox" :showHeader="showHeader" :item="searchCmdResult.item">
        <t-help-box ref="terminalHelpBox" :show-header="showHeader" :result="searchCmdResult" v-show="searchCmdResult.show"></t-help-box>
      </slot>
    </div>
    <div class="t-text-editor-container" v-if="textEditor.open"
         :style="`${showHeader ? 'height:calc(100% - 34px);margin-top: 34px;' : 'height:100%'}`">
      <slot name="textEditor" :data="textEditor">
        <t-editor :config="textEditor" @close="_textEditorClose" ref="terminalTextEditor"></t-editor>
      </slot>
    </div>
  </div>
</template>

<script>
import './css/scrollbar.css'
import './css/ansi.css'
import './css/style.css'
import 'vue-json-viewer/style.css'
import TerminalJs from './Terminal.js'

export default TerminalJs
</script>

<style scoped lang="scss">
.terminal {
  display: flex;
  flex-direction: column;
  height: 100%;
}
.t-window {
  background: #fff;
  position: relative;
  flex: 1;
  // position: relative;
  .t-log-box {
    width: 100%;
    .t-log-msg-box {
      clear: both;
      overflow: hidden;
      // background: #aaa;
      padding: 12px;
    }
    .msg-list-box {
      background: rgba(59,130,246,.3);
      width: max-content;
      max-width: 80%;
      border-radius: 12px;
      overflow-y: auto;
      max-height: 200px;
      padding: 16px;
    }
    .left-box {
      border-radius: 12px;
      // background: #888;
      // padding: 12px;
      // width: max-content;
      // max-width: 80%;
      // background: red;
      // float: left;
    }
    .right-box {
      border-radius: 12px;
      background: rgba(20, 108, 255, 0.5);
      padding: 12px;
      width: max-content;
      float: right;
    }
  }
  span {
    // background: #000;
    color: #000;
  }
}
.cmd-input-box {
  background: #fff;
  border-top: 1px #eee solid;
  padding: 12px;
}
.t-last-line {
  height: 80px;
  // padding: 12px;
  box-sizing: border-box;
  position: relative;
  .t-cmd-line-content {
    /deep/ .t-cmd-key {
      color: #000;
    }
  }
  .t-cmd-input-box {
    width: 100%;
    height: 100%;
    padding: 0;
    margin: 0;
    border: none;
    outline: none; // 去除选中状态边框
    background-color: rgba(0, 0, 0, 0);// 透明背景
    
    // background: red;
  }
}
.terminal-tools {
  height: 45px;
  background: #555;
  padding: 0 16px;
}
.tools-box {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  height: 100%;
  color: #ccc;
  gap: 16px;
  font-size: 14px;
}

.tools-box input,
.tools-box button{
  background: #444;
  color: #ccc;
  border: none;
  padding: 0 8px;
  height: 30px;
  line-height: 30px;
  border-radius: 5px;
  border: 0;  
  outline: none; 
}

.tools-box input {
  width: 100px;
  color: #ddd;
}

.tools-box button{
  min-width: 30px;
  line-height: 30px;
  padding: 3px 0;
  box-sizing: border-box;
}

.tools-box button:hover {
  background: #333;
}

.group {
  border-radius: 5px;
  background: #333;
  overflow: hidden;
}

.group button{
  border-radius: 0;
  line-height: 30px;
  padding: 3px 0;
  box-sizing: border-box;
}

.serach-box {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  background: #444;
  height: 30px;
  border-radius: 5px;
  position: relative;
}

.serach-box span {
  color: #999;
  border-right: 1px #666 solid;
  padding-right: 12px;
  margin-right: 4px;
}
.find-text-num {
  min-width: 66px;
  text-align: right;
  box-sizing: border-box;
}
::v-deep em {
  color: #000;
  font-style: initial;
}
.cmd-btn-box button {
  line-height: 30px;
  width: 40px;
  padding: 0;
}
.terminal-log-count {
  float: right;
  position: absolute;
  right: 10px;
}
</style>

