<template>
  <date-picker v-model="value" :config="options"></date-picker>
</template>

<script>
export default {
  name: "mformdate",
  props: {
    options: Object,
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
    rows: {
      default: 4,
      type: Number
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
    label: {
      default: "",
      type: String
    },
    tooltip: {
      default: "",
      type: String
    },
    tooltipPosition: {
      default: "bottom",
      type: String
    },
    rules: {
      default: function() {
        return [];
      },
      type: Array
    },
    prependIcon: String,
    appendIcon: String,
    prependText: String,
    appendText: String,
    placeholder: String
  },
  mounted() {
    // if (!this.id2) this.id2 = this.$Utils.randomstring();
    if (this.id2) this.id2 = this.id;
    else this.id2 = this.$Utils.randomstring();
    if (this.value) this.setValue(this.value);
  },
  data() {
    let date = this.$moment().format("YYYY-MM-DD");
    if (this.value) date = this.value;
    if (date == "0000-00-00") date = "";
    return {
      id2: this.id,
      errormsg2: "",
      rawdate: "",
      date,
      menu: false
    };
  },
  watch: {
    date: function(val) {
      this.$emit("input", val);
    },
    value: function(val) {
      this.setValue(val);
    }
  },
  components: {},
  methods: {
    setValue(val) {
      if (val == "0000-00-00") {
        this.rawdate = "";
        this.date = this.$moment().format("YYYY-MM-DD");
      } else {
        this.rawdate = this.$moment(val, "YYYY-MM-DD").format("DD/MM/YYYY");
        this.date = this.$moment(val, "YYYY-MM-DD").format("YYYY-MM-DD");
      }
    },
    informValid() {
      this.errormsg2 = "";
    },
    informInvalid(txt) {
      this.errormsg2 = txt;
    },
    validate() {
      let oks = [];
      for (let iRule = 0; iRule < this.rules.length; iRule++) {
        const rule = this.rules[iRule];
        let ok = rule(this.date);
        if (this.$_.isString(ok)) oks.push(ok);
      }
      if (oks.length == 0) {
        this.informValid();
        return true;
      }
      this.informInvalid(oks.join(","));
      return false;
    },
    onfocus(evt) {
      this.$emit("focus", evt);
    }
  }
};
</script>
