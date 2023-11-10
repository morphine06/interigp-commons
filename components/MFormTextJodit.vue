<template>
  <div class="form-group textarea-markdown">
    <label class="label" v-if="label">{{ label }}</label>
    <div class="input-group" ref="jodit"></div>
  </div>
</template>

<script>
// import "jodit/build/jodit.min.js";
export default {
  name: "joditeEditor",
  components: {},
  props: {
    value: { type: String },
    extraButtons: { type: Array, default: null },
    config: { type: Object, default: () => ({}) },
    label: {
      default: "",
      type: String,
    },
  },
  data: () => ({
    editor: null,
    buttons: ["bold", "underline", "italic", "|", "brush", "paragraph"],
  }),
  computed: {
    editorConfig() {
      const config = { ...this.config };
      if (this.buttons) {
        config.buttons = this.buttons;
        config.buttonsMD = this.buttons;
        config.buttonsSM = this.buttons;
        config.buttonsXS = this.buttons;
      }
      if (this.extraButtons) config.extraButtons = this.extraButtons;
      return config;
    },
  },
  watch: {
    value(newValue) {
      if (this.editor.value !== newValue) {
        this.editor.value = newValue;
        this.$emit("input", newValue);
      }
    },
  },
  mounted() {
    this.editor = new this.$Jodit(this.$refs.jodit, this.editorConfig);
    this.editor.value = this.value;
    this.editor.events.on("change", (newValue) =>
      this.$emit("input", newValue)
    );
  },
  beforeDestroy() {
    this.editor.destruct();
  },
};
</script>

<style lang="scss" scoped>
.input-group > .form-control-plaintext,
.input-group > .custom-select,
.input-group > .custom-file {
  margin-bottom: 0px;
}
.textarea {
  background-color: #ffffff;
  border: 1px solid #eaeaea;
}
.textarea-markdown .label {
  position: relative;
}
/* 
.label_focus {
  top: -20px;
  font-size: 12px;
  color: gray;
}
.textarea_label {
  position: relative;
} */
.m-form-base {
  .m-form-base__container-input {
    textarea {
      border-radius: 5px;
      border: 1px solid #eaeaea;
    }
  }
}
.sub-text {
  color: #4950579c;
}
.container-btn-markdown {
  width: 100%;
  border: 1px solid #eaeaea;
  padding: 8px;
}
.container-icon {
  cursor: pointer;
}
.btn-help {
  width: 100%;
  border: 1px solid #eaeaea;
  padding: 5px;
  cursor: pointer;
}
</style>
