<template>
  <m-form-select
    :items="items"
    v-model="val"
    :name="name"
    :readonly="false"
    :label="label"
    :label-width="labelWidth"
    :autoSelectOnFocus="true"
    @search="search_go"
    @focus="onfocus"
    :labelPosition="labelPosition"
    :disabled="disabled"
    :appendToBody="appendToBody"
  ></m-form-select>
</template>

<script>
export default {
  name: "mformcombobox",
  props: [
    "value",
    "label",
    "labelWidth",
    "prependIcon",
    "prefix",
    "readonly",
    "rules",
    "name",
    "labelPosition",
    "storeUrl",
    "storeParams",
    "itemText",
    "itemTextParams",
    "itemValue",
    "disabled",
    "appendToBody"
  ],
  data() {
    let val = "",
      items = [],
      row = {};
    if (this.value) {
      this._setTextAndValue(this.value);
      items = [this.value];
      row = this.value;
      val = this.value.value;
    }
    return {
      row,
      val,
      items,
      isLoading: false,
      search: null
    };
  },
  watch: {
    val(val) {
      let f = this.$_.find(this.items, { value: val });
      if (f) this.$emit("input", f);
      else if (val == "") this.$emit("input", null);
    },

    value: function(val) {
      if (!val) {
        this.items = [];
        this.row = null;
        this.val = 0;
        return;
      }
      this._setTextAndValue(val);
      this.items = [val];
      this.row = val;
      this.val = val.value;
    }
  },
  components: {},
  methods: {
    _setTextAndValue(val) {
      if (this.$_.isFunction(this.itemValue)) val.value = this.itemValue(val);
      if (this.$_.isString(this.itemValue)) val.value = val[this.itemValue];
      let params = this.itemTextParams ? this.itemTextParams : "";
      if (this.$_.isFunction(this.itemText))
        val.text = this.itemText(val, params);
      if (this.$_.isString(this.itemText)) val.text = val[this.itemText];
    },
    onfocus(evt) {
      this.search_go("");
      this.$emit("focus", evt);
    },
    async search_go(val) {
      // console.log("search_go", val);
      if (this.disabled) return;
      let storeParams = this.storeParams;
      if (!storeParams) storeParams = {};
      this.isLoading = true;
      let params = { text: val, limit: 100 };
      Object.assign(params, storeParams);
      // console.log("params", params);
      let response = await this.$axios.get(this.storeUrl, {
        params
      });
      this.isLoading = false;
      response.data.data.map(v => {
        this._setTextAndValue(v);
      });
      this.items = response.data.data;
    },

    acceptAll(item, queryText, itemText) {
      return true;
    }
  }
};
</script>
