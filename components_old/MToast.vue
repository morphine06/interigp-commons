<template>
  <div class="toast-container">
    <div
      class="toast show fade"
      :style="`border-left: 4px solid ${toast.color};`"
      role="alert"
      aria-live="assertive"
      aria-atomic="true"
      v-for="(toast, index) in toasts"
      :key="index"
    >
      <div v-if="toast.title">
        <div class="toast-header">
          <strong class="me-auto">{{ toast.title }}</strong>
          <small class="text-muted">{{ toast.date }}</small>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="toast"
            aria-label="Close"
            @click="close(toast.timestamp)"
          ></button>
        </div>
        <div class="toast-body">
          {{ toast.text }}
        </div>
      </div>
      <div v-else>
        <div class="d-flex">
          <div class="toast-body">
            {{ toast.text }}
          </div>
          <button
            type="button"
            class="btn-close me-2 m-auto"
            data-bs-dismiss="toast"
            aria-label="Close"
            @click="close(toast.timestamp)"
          ></button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "mtoast",
  props: {
    toasts: {
      default: function() {
        return [];
      }
    }
  },

  data() {
    return {};
  },
  mounted() {},
  watch: {},
  components: {},
  methods: {
    close(index) {
      this.$emit("close", index);
    }
  }
};
</script>
<style lang="scss">
.toast-container {
  position: fixed;
  top: 20px;
  right: 20px;
  z-index: 100000000;
}
.toast {
  background-color: #fff !important;
}
</style>
