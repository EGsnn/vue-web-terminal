<template>
  <div class="t-editor">
    <textarea name="editor" ref="textEditor" class="t-text-editor" v-model="config.value"
              @focus="config.onFocus" @blur="config.onBlur"></textarea>
    <div class="t-text-editor-floor" align="center">
      <button class="t-text-editor-floor-btn t-close-btn"
              @click="$emit('close', false)"
              title="Cancel Edit">Cancel</button>
      <button class="t-text-editor-floor-btn t-save-btn"
              @click="$emit('close', true)"
              title="Save And Close">Save & Close</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "TEditor",
  props: {
    config: Object
  },
  methods:{
    focus() {
      this.$refs.textEditor.focus()
    }
  }
}
</script>

<style>
.t-editor {
  width: 100%;
  height: 100%;
}
.t-close-btn {
  color: #bba9a9;
}
.t-close-btn:hover {
  color: #00ffff;
}

.t-save-btn {
  color:#00b10e;
}
.t-save-btn:hover {
  color: #befcff;
}
</style>