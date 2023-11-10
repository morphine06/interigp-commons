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
        <div class="modal-header">
          <h3>{{ title }}</h3>
        </div>
        <div class="modal-body">
          <p v-html="text"></p>
        </div>
        <div class="modal-footer">
          <button class="btn btn-secondary" @click="cancelWin">
            {{ btnCancelTxt ? btnCancelTxt : "Annuler" }}
          </button>
          <button class="btn btn-primary" @click="deleteWin">
            {{ btnOkTxt ? btnOkTxt : "Ok" }}
          </button>
          <button v-if="threeBtn" class="btn btn-primary" @click="btn3Action">
            {{ btn3Txt }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "m-confirm-dialog",
  props: {
    value: Boolean,
    title: String,
    text: String,
    sousText: String,
    width: {
      type: Number,
      default: 500,
    },
    threeBtn: Boolean,
    btnOkTxt: String,
    btnCancelTxt: String,
    btn3Txt: String,
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
    cancelWin() {
      this.$emit("canceled");
    },
    deleteWin() {
      this.$emit("confirmed");
    },
    btn3Action() {
      this.$emit("btn3Action");
    },
  },
};
</script>
<style lang="scss">
.cadre-soustext {
  background-color: #bfbebe;
  padding: 10px;
}
.text {
  padding-left: 8opx;
}
</style>
