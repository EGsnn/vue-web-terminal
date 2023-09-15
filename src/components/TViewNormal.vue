<template>
  <span class="t-content-normal">
    <span v-if="_nonEmpty(item.tag == null ? item.class : item.tag)"
          :class="item.class"
          style="margin-right: 10px">{{ item.tag == null ? item.class : item.tag }}</span>
    <span :style="item.contentStyle" v-html="content"></span>
  </span>
</template>

<script>
import {_nonEmpty} from "@/js/Util";
import {terminalViewerProps} from "@/js/TerminalAttribute";

export default {
  name: "TViewNormal",
  data(){
    return {
      content: ''
    }
  },
  props: terminalViewerProps(),
  created() {
    let content = this.item.content
    if(this.searchText) {
      let reg = new RegExp(`(${this.searchText})`, 'ig')
      content = content.replace(reg, '<em style="background-color: yellow">$1</em>');
    }
    this.content = content

  },
  methods: {
    _nonEmpty(value) {
      return _nonEmpty(value)
    }
  }
}
</script>

<style scoped>

</style>