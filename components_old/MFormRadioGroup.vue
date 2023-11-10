<template>
  <m-form-base
    :id="id2"
    :label="label"
    :label-width="labelWidth"
    :label-position="labelPosition"
    :prepend-icon="prependIcon"
    :append-icon="appendIcon"
  >
    <slot></slot>
  </m-form-base>
</template>

<script>
import MFormBase from "./MFormBase";

export default {
  name: "mformradiogroup",
  extends: MFormBase,
  props: {
    value: [String, Number],
    id: {
      default: "",
      type: String
    },
    type: {
      default: "text",
      type: String
    },
    name: {
      default: "",
      type: String
    },
    autocomplete: {
      default: "new-password",
      type: String
    },
    readonly: {
      default: false,
      type: Boolean
    },
    disabled: {
      default: false,
      type: Boolean
    },
    autoSelectOnFocus: {
      default: true,
      type: Boolean
    },
    prependIcon: String,
    appendIcon: String
  },
  mounted() {
    if (this.name) this.name2 = this.name;
    else this.name2 = this.$Utils.randomstring();
    for (let iSlot = 0; iSlot < this.$slots.default.length; iSlot++) {
      const slot = this.$slots.default[iSlot];
      slot.componentInstance.setName(this.name2);
    }
    if (!this.id2) this.id2 = this.$Utils.randomstring();
    if (this.value) this.setValue(this.value);
  },
  data() {
    return {
      id2: "",
      name2: ""
    };
  },
  watch: {
    value: function(val) {
      this.$nextTick(() => {
        this.setValue(val);
      });
    }
  },
  components: {},
  methods: {
    setValue(val) {
      for (let iSlot = 0; iSlot < this.$slots.default.length; iSlot++) {
        const slot = this.$slots.default[iSlot];
        slot.componentInstance.setChecked(val == slot.componentInstance.value);
      }
      this.$emit("input", val);
    },
    onclick(evt) {
      // evt.stopPropagation();
      this.$emit("click", evt);
    },
    onfocus(evt) {
      if (this.autoSelectOnFocus) evt.target.select();
      // evt.stopPropagation();
      // this.$emit("click", evt);
    }
  }
};
</script>

<style lang="scss"></style>
