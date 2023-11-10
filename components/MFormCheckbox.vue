<template>
  <label class="m-form-checkbox mw-100">
    <input
      type="checkbox"
      :disabled="disabled"
      v-model="value2"
      :name="name2"
      @change="onchange($event)"
    />
    <span
      :class="{
        'ms-2': true,
        'mw-100': true,
        whitespacecanceled: labelwhitespace,
      }"
      v-html="label"
    ></span>
  </label>
</template>

<script>
export default {
  name: "m-form-checkbox",
  props: {
    value: [String, Number, Boolean],
    label: String,
    id: {
      default: "",
      type: String,
    },
    name: {
      default: "",
      type: String,
    },
    readonly: {
      default: false,
      type: Boolean,
    },
    disabled: {
      default: false,
      type: Boolean,
    },
    labelwhitespace: {
      default: false,
      type: Boolean,
    },
  },
  mounted() {
    this.value2 = this.value;
    // console.log("this.value", this.value);
    //   if(this.value)
    //  if (this.name) this.name2 = this.name;
    //  else this.name2 = this.$Utils.randomstring();
  },
  data() {
    return {
      id2: "",
      value2: false,
      name2: "",
      checked: false,
      checked2: false,
    };
  },
  watch: {
    checked2: function (val) {},
    value: function (val) {
      this.value2 = val;
    },
    value2: function (val) {
      // this.$emit("input", val);
      this.setValue(val);
    },
  },
  components: {},
  methods: {
    setValue(val) {
      // this.$parent.$parent.setValue(val);
      // console.log("val", val);
      this.$emit("input", val);
    },
    setName(name) {
      this.name2 = name;
    },
    setChecked(checked) {
      this.checked = !!checked;
    },
    onclick(evt) {
      // evt.stopPropagation();
      // this.$emit("click", evt);
    },
    onfocus(evt) {
      if (this.autoSelectOnFocus) evt.target.select();
      // evt.stopPropagation();
      // this.$emit("click", evt);
    },
    onchange(evt) {
      let v = evt.target.checked;
      this.$emit("change", v, evt);
    },
  },
};
</script>

<style lang="scss" scoped>
// input[type="checkbox"] {
//   width: auto;
//   margin-right: 8px;
// }
.whitespacecanceled {
  white-space: break-spaces;
}
</style>
