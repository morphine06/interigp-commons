<template>
  <div class="relative">
    <m-form-text
      :class="dateColor + ' ' + classInput"
      ref="myinput"
      :id="id"
      :value="rawvalue"
      type="text"
      :name="name"
      :autocomplete="autocomplete"
      :readonly="readonly"
      :disabled="disabled"
      :autoSelectOnFocus="false"
      :label="label"
      :tooltip="tooltip"
      :tooltipPosition="tooltipPosition"
      :rules="rules"
      placeholder=" "
      :appendIcon="appendIcon"
      :prependIcon="prependIcon"
      :appendText="appendText"
      :prependText="prependText"
      :ariaHaspopup="true"
      :subText="subText"
      @click="onclick"
      @input="oninput"
      @focus="onfocus"
      @keyup="onkeyup"
      @keydown="onkeydown"
      @ShowDropdownMenu="hideOrShowDropdown"
    ></m-form-text>
    <div
      class="dropdown-menu"
      ref="mydropdown"
      v-show="showDropdown"
      style="display: block"
      @click="onclickDropdown"
    >
      <m-calendar
        :year="year"
        :value="valueInterne"
        @input="oninputcalendar"
      ></m-calendar>
    </div>
  </div>
</template>

<script>
import moment from "moment";
// import MFormBase from "./MFormBase";
// import { createPopper } from "@popperjs/core";

export default {
  name: "mformdate",
  // extends: MFormBase,
  props: {
    value: [String, Object, Number],
    id: {
      default: "",
      type: String,
    },
    name: {
      default: "",
      type: String,
    },
    autocomplete: {
      default: "new-password",
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
    autoSelectOnFocus: {
      default: true,
      type: Boolean,
    },
    label: {
      default: "",
      type: String,
    },
    tooltip: {
      default: "",
      type: String,
    },
    tooltipPosition: {
      default: "bottom",
      type: String,
    },
    rules: {
      default: function () {
        return [];
      },
      type: Array,
    },
    placeholder: String,
    prependIcon: String,
    appendIcon: String,
    prependText: String,
    appendText: String,
    subText: String,

    formatInput: {
      default: "DD/MM/YYYY",
      type: String,
    },
    formatValue: {
      default: "YYYY-MM-DD",
      type: String,
    },
    year: {
      default: false,
      type: Boolean,
    },
    /// props projet
    classInput: String,
  },
  mounted() {
    if (!this.id2) this.id2 = this.$Utils.randomstring();
    if (this.value) this.setEnterValue(this.value);
    else {
      this.rawvalue = moment().format("DD/MM/YYYY");
      this.dateColor = "dateColor";
    }
    this.$nextTick(() => {
      // console.log(
      //   "this.$refs.myinput",
      //   this.$refs.myinput.$el,
      //   this.$refs.mydropdown
      // );
      new this.$Popper(this.$refs.myinput.$el, this.$refs.mydropdown, {
        placement: "bottom-start",
      });
    });
  },

  data() {
    // let name2 = this.name ? this.name : "";
    // let valueInterne = this.value ? moment(this.value, this.formatValue) : moment();

    return {
      currentPartFocus: 0,
      id2: this.id,
      // name2,
      valueInterne: moment(),
      rawvalue: moment().format(),
      errormsg2: "",
      showDropdown: false,
      dateColor: "",
    };
  },
  watch: {
    // rawvalue: function(val) {
    //   this.$emit("search", val);
    //   if (val == "") this.$emit("input", "");
    // },
    value: function (val) {
      // console.log("val", val);
      // if (val !== "Invalid date" && val !== "0000-00-00")
      this.setEnterValue(val);
      // this.valueInterne = this.value
      //   ? moment(this.value, this.formatValue)
      //   : moment();
      // this.rawvalue = this.valueInterne.format(this.formatInput);
    },
  },
  components: {},
  methods: {
    _getInput() {
      return this.$refs.myinput.$refs.myinput;
    },
    setEnterValue(v) {
      // console.log("v", v);
      let m = moment(v);
      if (!m.isValid()) {
        this.dateColor = "";
        this.valueInterne = moment();
        this.rawvalue = "";
        this.$emit("input", "0000-00-00");
        return;
      }

      // console.log("v", v);
      this.dateColor = "";
      this.valueInterne = moment.isMoment(v) ? v : moment(v, this.formatValue);
      this.rawvalue = this.valueInterne.format(this.formatInput);
      this.$emit("input", this.valueInterne.format(this.formatValue));
    },
    hideOrShowDropdown(showDropdown) {
      this.showDropdown = showDropdown;
    },
    /* onWindowClick() {
      this.showDropdown = false;
    }, */
    oninput(val) {
      let input = this._getInput();
      let m = moment(val, this.formatInput, true);
      // if (m.isValid()) {
      this.setEnterValue(m);
      // }
      let pos = input.selectionStart;
      if (pos == 2) this.whatFocus(1);
      if (pos == 5) this.whatFocus(2);
      // console.log("pos", pos);
      // this.rawvalue = val;
    },
    oninputcalendar(val, what) {
      this.setEnterValue(moment(val));
      this.showDropdown = false;
      // this.valueInterne = moment(val);
      // this.rawvalue = val.format(this.formatInput);
      // this.$emit("input", val.format(this.formatValue));
    },
    onkeyup(evt) {
      this.$emit("keyup", evt);
    },
    onkeydown(evt) {
      if (
        evt.key == "ArrowRight" ||
        evt.key == "ArrowLeft" ||
        evt.key == "Backspace"
      ) {
      } else if (evt.key === "Tab") {
        evt.preventDefault();
        this.whatFocus(-1);
      } else if (!Number.isInteger(evt.key * 1)) evt.preventDefault();
      this.$emit("keydown", evt);
    },
    whatFocus(part) {
      //part=-1 alors prochain focus
      let input = this._getInput();
      if (part == -1) {
        this.currentPartFocus++;
        if (this.currentPartFocus >= 3) this.currentPartFocus = 0;
        part = this.currentPartFocus;
      }
      if (part == 0) {
        input.selectionStart = 0;
        input.selectionEnd = 2;
      } else if (part == 1) {
        input.selectionStart = 3;
        input.selectionEnd = 5;
      } else if (part == 2) {
        input.selectionStart = 6;
        input.selectionEnd = 10;
      }
    },
    onfocus(evt) {
      this.$emit("focus", evt);
      // this.$refs.mydropdown.style.display = "block";
      // let input = this._getInput();
      this.whatFocus(0);
      this.showDropdown = true;
    },
    onclick(evt) {
      // evt.stopPropagation();
    },
    onclickDropdown(evt) {
      evt.stopPropagation();
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
        let ok = rule(this.valueInterne ? this.valueInterne.value : null);
        if (this.$_.isString(ok)) oks.push(ok);
      }
      if (oks.length == 0) {
        this.informValid();
        return true;
      }
      this.informInvalid(oks.join(","));
      return false;
    },
  },
};
</script>
<style lang="scss">
.dateColor input {
  color: #b7b7b7;
}
.year-concours .input-group .form-control {
  height: 40px;
  background-color: #fff0;
  border: 0;
  border-radius: 0;
  border-bottom: 1px solid gray;
  font-size: 26px;
}
</style>
