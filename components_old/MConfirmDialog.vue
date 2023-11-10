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
      <div class="modal-content" :style="'width:' + width">
        <div class="modal-header">
          <h3>{{ title }}</h3>
        </div>
        <div class="modal-body">
          <p v-html="text"></p>
        </div>
        <div class="modal-footer">
          <button class="btn btn-secondary" @click="cancelWin">
            Annuler
          </button>
          <button class="btn btn-primary" @click="deleteWin">Ok</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "mconfirmdialog",
  props: {
    value: Boolean,
    title: String,
    text: String,
    sousText: String,
    width: {
      type: String,
      default: "500px"
    }
  },
  data() {
    return {
      dialog: false
    };
  },
  mounted() {},
  watch: {
    value: function(val) {
      this.dialog = val;
    }
  },
  components: {},
  methods: {
    cancelWin() {
      this.$emit("canceled");
    },
    deleteWin() {
      this.$emit("confirmed");
    }
  }
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
