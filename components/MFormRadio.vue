<template>
  <div class="d-flex flex-column">
    <div class="m-form-radio" v-for="(item, index) in items" :key="index">
      <input
        type="radio"
        :value="item.value"
        :name="id"
        :id="id + '_' + item.value"
        :checked="isChecked(item)"
        @input="setValue($event.target.value)"
      />
      <label class="ms-2" :for="id + '_' + item.value">{{ item.text }}</label>
    </div>
  </div>
</template>

<script>
export default {
  name: "m-form-radio",
  props: {
    value: [String, Number, Boolean],
    label: String,
    items: {
      default: function () {
        return [];
      },
      type: Array,
    },
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
  },
  mounted() {},
  data() {
    return {
      id2: "",
      name2: "",
      checked: false,
      checked2: false,
    };
  },
  watch: {
    checked2: function (val) {},
  },
  components: {},
  methods: {
    isChecked(item) {
      let ok = false;
      if (typeof this.value === Boolean) {
        ok = this.value === item.value;
      } else ok = this.value === item.value;

      return ok;
    },
    setValue(val) {
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
    },
  },
};
</script>

<style lang="scss"></style>
