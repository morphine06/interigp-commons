<template>
  <div
    class="modal"
    v-if="value"
    :style="
      value ? 'display:block;background-color:#3333337a;' : 'display:none;'
    "
    tabindex="-1"
    role="dialog"
    aria-hidden="true"
  >
    <div class="modal-dialog modal-dialog-scrollable" role="document">
      <div class="modal-content" :style="getWidth()">
        <div class="modal-header" :style="`border-top: 4px solid ${color};`">
          <h3>{{ title }}</h3>
        </div>
        <div class="modal-body" v-html="text"></div>
        <div class="modal-footer">
          <button class="btn btn-primary" @click="deleteWin">Ok</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "m-message-dialog",
  props: {
    value: Boolean,
    title: String,
    text: String,
    width: {
      type: Number,
      default: 500,
    },
    redirect: String,
    color: {
      type: String,
      default: "red",
    },
  },

  data() {
    return {
      dialog: false,
      windowWidth: window.innerWidth,
    };
  },
  mounted() {
    this.$nextTick(() => {
      window.addEventListener("resize", this.onResize);
    });
  },
  destroyed() {
    window.removeEventListener("resize", this.onResize);
  },
  watch: {
    value: function (val) {
      this.dialog = val;
      // if (!this.width) this.width = "500px";
    },
  },
  components: {},
  methods: {
    onResize() {
      // console.log("window.innerWidth", window.innerWidth);
      this.windowWidth = window.innerWidth;
    },
    getWidth() {
      let style = "";
      let width = this.width ? this.width : 500;
      style = this.windowWidth < width ? "width:100%" : "width:" + width + "px";
      // console.log("style", style);
      return style;
    },
    deleteWin() {
      this.dialog = false;
      if (this.redirect) {
        this.$router.push("/" + this.redirect).catch((err) => {});
      }
      this.$emit("input", false);
      this.$emit("close");
    },
  },
};
</script>
<style lang="scss">
.modal-content {
  margin: auto;
}
</style>
