<template>
  <div>
    <m-form-text
      :class="{ badNumber: badNumber }"
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
      :placeholder="placeholder"
      :appendIcon="appendIcon"
      :prependIcon="prependIcon"
      :appendText="appendText"
      :prependText="prependText"
      :ariaHaspopup="true"
      @click="onclick"
      @input="oninput"
      @focus="onfocus"
      @keyup="onkeyup"
      @keydown="onkeydown"
      @hideOrShowDropdown="hideOrShowDropdown"
    ></m-form-text>
    <div
      class="dropdown-menu"
      ref="mydropdown"
      v-show="showDropdown"
      style="display: block"
      @click="onclickDropdown"
    >
      <m-hour :value="value" @setTime="oninputhour"></m-hour>
    </div>
  </div>
</template>

<script>
import moment from "moment";
// import MFormBase from "./MFormBase";
// import { createPopper } from "@popperjs/core";

export default {
  name: "m-form-hour",
  // extends: MFormBase,
  props: {
    value: [String, Object],
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

    formatInput: {
      default: "YYYY-MM-DD",
      type: String,
    },
    formatValue: {
      default: "HH:mm",
      type: String,
    },
  },
  mounted() {
    if (this.value) {
      this.setEnterValue(this.value);
      this.displayValue();
    }
    if (!this.id2) this.id2 = this.$Utils.randomstring();
    this.$nextTick(() => {
      new this.$Popper(this.$refs.myinput.$el, this.$refs.mydropdown, {
        placement: "bottom-start",
      });
    });
  },

  data() {
    let name2 = this.name ? this.name : "";
    let value2 = this.value ? moment(this.value, this.formatValue) : moment();
    // let rawvalue = this.value;

    return {
      id2: this.id,
      name2,
      value2,
      rawvalue: "",
      errormsg2: "",
      showDropdown: false,
      hourTemp: "00",
      minuteTemp: "00",
      badNumber: false,
      minutesSelected: false,
    };
  },
  watch: {
    rawvalue: function (val) {},
    value: function (val) {
      this.setEnterValue(val);
      /* this.value2 = this.value
        ? moment(this.value, this.formatValue)
        : moment();
      this.rawvalue = this.value2.format(this.formatInput); */
    },
  },
  components: {},
  methods: {
    displayValue() {
      this.rawvalue = this.hourTemp + ":" + this.minuteTemp;
    },
    setEnterValue(val) {
      this.hourTemp = val.substring(0, 2);
      this.minuteTemp = val.substring(3, 6);
    },
    hideOrShowDropdown(showDropdown) {
      this.showDropdown = showDropdown;
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
        let ok = rule(this.value2 ? this.value2.value : null);
        if (this.$_.isString(ok)) oks.push(ok);
      }
      if (oks.length == 0) {
        this.informValid();
        return true;
      }
      this.informInvalid(oks.join(","));
      return false;
    },
    oninput(val, evt) {
      let tabTime = val.split(":");
      if (tabTime[0] * 1 > 23) this.badNumber = true;
      if (tabTime[1] * 1 > 59) {
        this.badNumber = true;
        val = val.substring(0, 5);
      }
      if (tabTime[0].length === 2 && !this.minutesSelected) {
        let input = this.$refs.myinput.$refs.myinput;
        input.selectionStart = 3;
        input.selectionEnd = 5;
        this.minutesSelected = true;
      }
      evt.preventDefault();
      this.rawvalue = val;
    },
    oninputhour(val) {
      let time = val.hour + ":" + val.minutes;
      this.setEnterValue(time);
      this.displayValue();
    },
    onkeyup(evt) {
      //if(evt.key === )
      /* this.$emit("onkeyup", evt); */
    },
    onkeydown(evt) {
      if (!Number.isInteger(evt.key * 1) && evt.key !== "Tab")
        evt.preventDefault();
      if (evt.key === "Tab" && !this.minutesSelected) {
        evt.preventDefault();
        let input = this.$refs.myinput.$refs.myinput;
        input.selectionStart = 3;
        input.selectionEnd = 5;
        this.minutesSelected = true;
      }
    },
    onfocus(evt) {
      this.$emit("focus");
      // this.$refs.mydropdown.style.display = "block";
      this.minutesSelected = false;
      this.showDropdown = true;
      let input = this.$refs.myinput.$refs.myinput;
      input.selectionStart = 0;
      input.selectionEnd = 2;
    },
    onclick(evt) {},
    onclickDropdown(evt) {
      evt.stopPropagation();
    },
  },
};
</script>
<style lang="scss">
.badNumber {
  background-color: red;
}
</style>
