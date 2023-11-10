<template>
  <label class="m-form-checkbox">
    <input
      type="checkbox"
      :disabled="disabled"
      :checked="value"
      :name="name2"
      @input="setValue($event.target.checked)"
    />
    <span class="ms-2" v-html="label"></span>
  </label>
</template>

<script>
export default {
  name: "mformcheckbox",
  props: {
    value: [String, Number, Boolean],
    label: String,
    id: {
      default: "",
      type: String
    },
    name: {
      default: "",
      type: String
    },
    readonly: {
      default: false,
      type: Boolean
    },
    disabled: {
      default: false,
      type: Boolean
    }
  },
  mounted() {
    // console.log("this.value", this.value);
    //   if(this.value)
    //  if (this.name) this.name2 = this.name;
    //  else this.name2 = this.$Utils.randomstring();
  },
  data() {
    return {
      id2: "",
      name2: "",
      checked: false,
      checked2: false
    };
  },
  watch: {
    checked2: function(val) {}
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

<style lang="scss" scoped>
// input[type="checkbox"] {
//   width: auto;
//   margin-right: 8px;
// }
</style>
