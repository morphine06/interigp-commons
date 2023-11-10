<template>
  <div
    class="container-alert d-flex justify-content-center"
    :class="container !== 'app' ? 'w-100' : ''"
    v-if="value"
  >
    <div class="alert" role="alert" :class="'alert-' + alertColor">
      {{ text }}
      <button
        @click="close"
        type="button"
        class="btn-close ms-3"
        aria-label="Close"
      ></button>
    </div>
  </div>
</template>

<script>
export default {
  name: "malertdialog",
  props: {
    value: Boolean,
    text: String,
    container: String,
    width: {
      type: String,
      default: "1000px"
    },
    alertColor: String
  },

  data() {
    return {};
  },
  mounted() {},
  watch: {},
  components: {},
  methods: {
    close() {
      this.$emit("close");
    }
  }
};
</script>
<style lang="scss">
.container-alert {
  position: absolute;
  top: 30px;
  width: 80%;
}
.container-alert {
  .alert {
    width: 65%;
  }
}
</style>
