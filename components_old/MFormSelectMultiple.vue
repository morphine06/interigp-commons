<template>
  <div>
    <m-form-text
      ref="myinput"
      data-toggle="dropdown"
      :id="id"
      v-model="rawvalue"
      type="text"
      :name="name"
      :autocomplete="autocomplete"
      :readonly="readonly"
      :disabled="disabled"
      :autoSelectOnFocus="autoSelectOnFocus"
      :label="label"
      :tooltip="tooltip"
      :tooltipPosition="tooltipPosition"
      :rules="rules"
      :placeholder="placeholder"
      appendIcon="arrow-down"
      :prependIcon="prependIcon"
      :appendText="appendText"
      :subText="subText"
      :prependText="prependText"
      :ariaHaspopup="true"
      :ariaExpanded="showDropdown"
      @click="onclick"
      @input="oninput"
      @focus="onfocus"
      @keyup="onkeyup"
      @ShowDropdownMenu="ShowDropdownMenu"
      :selectMultiple="true"
    ></m-form-text>
    <ul
      role="menu"
      class="dropdown-menu"
      ref="mydropdown"
      v-show="showDropdown"
      style="display:block;"
      @focus="onfocus"
    >
      <li
        v-for="(item, i) in rawitems"
        :key="item.value"
        :class="{
          'dropdown-item': true,
          focusDropdown: focusDropdown && focusDropdown.index === i
        }"
        role="menuitem"
        @click.stop="setValue2($event, item)"
      >
        <input type="checkbox" v-model="item.checked" />
        <label> {{ item.text }}</label>
      </li>
    </ul>
  </div>
</template>

<script>
// import MFormBase from "./MFormBase";
// import { createPopper } from "@popperjs/core";

export default {
  name: "mformselectmultiple",
  // extends: MFormBase,
  props: {
    value: Array,
    id: {
      default: "",
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
    placeholder: String,
    prependIcon: String,
    appendIcon: String,
    prependText: String,
    appendText: String,
    subText: String,
    items: {
      default: function() {
        return [];
      },
      type: Array
    }
  },
  mounted() {},

  data() {
    let rawvalue = "";
    // console.log("this.items", this.items);
    //console.log("this.value", this.value);
    if (this.value) {
      this.focus = true;
      for (let iVal = 0; iVal < this.value.length; iVal++) {
        const value = this.value[iVal];
        rawvalue += " / " + value.text;
      }
    }
    let rawitems = [];
    for (let i = 0; i < this.items.length; i++) {
      const item = this.items[i];
      let f = this.$_.find(this.items, { value: this.value });
      if (f) {
        item.checked = true;
      } else {
        item.checked = false;
      }
    }
    rawitems = this.items;
    //console.log("this.items", this.items);
    return {
      errormsg2: "",
      showDropdown: false,
      focus: null,
      focusDropdown: null,
      rawvalue,
      rawitems,
      multipleValues: []
    };
  },
  watch: {},
  components: {},
  methods: {
    ShowDropdownMenu(showDropdown) {
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
    oninput(val) {
      this.rawvalue = val;
    },
    onkeyup(evt) {
      switch (evt.keyCode) {
        case 40: // flèche du bas
          evt.preventDefault();
          if (!this.focusDropdown) {
            this.focusDropdown = { item: this.items[0], index: 0 };
          } else {
            let newIndex = this.focusDropdown.index + 1;
            if (this.items[newIndex]) {
              this.focusDropdown = {
                item: this.items[newIndex],
                index: newIndex
              };
            } else {
              this.focusDropdown = { item: this.items[0], index: 0 };
            }
          }
          break;
        case 38: // flèche du haut
          evt.preventDefault();
          if (!this.focusDropdown) {
            this.focusDropdown = {
              item: this.items[this.items.length - 1],
              index: this.items.length - 1
            };
          } else {
            let newIndex = this.focusDropdown.index - 1;
            if (this.items[newIndex]) {
              this.focusDropdown = {
                item: this.items[newIndex],
                index: newIndex
              };
            } else {
              this.focusDropdown = {
                item: this.items[this.items.length - 1],
                index: this.items.length - 1
              };
            }
          }
          break;
        case 13: // entrer
          evt.preventDefault();
          this.setValue2(evt, this.focusDropdown.item);
          this.ShowDropdownMenu(false);
          break;
      }
      this.$emit("onkeyup", evt);
    },
    onfocus(evt) {
      let ref = this.$refs.myinput ? this.$refs.myinput : "noInput";
      this.focus = ref;
      this.$emit("focus", evt);
    },
    onclick(evt) {
      this.$emit("click", evt);
    },
    findItem(val) {
      let f = this.$_.find(this.items, { value: this.value });
      if (!f) f = { value: "", text: "" };
      return f;
    },
    setValue2($event, item) {
      let i = this.$_.findIndex(this.multipleValues, { value: item.value });
      if (i < 0) {
        item.checked = true;
        this.multipleValues.push(item);
        this.setRawValue();
      } else {
        item.checked = false;
        this.multipleValues.splice(i, 1);
        this.setRawValue();
      }
      this.$forceUpdate();
    },
    setRawValue() {
      let rawvalue = [];
      for (let iVal = 0; iVal < this.multipleValues.length; iVal++) {
        const value = this.multipleValues[iVal];
        rawvalue.push(value.text);
      }
      this.rawvalue = rawvalue.join(" / ");
    },
    clikInput() {
      this.focus = true;
      this.showDropdown = true;
    }
  }
};
</script>
<style lang="scss" scoped>
.focusDropdown {
  background-color: #f8f9fa;
}
.input-group-text {
  border: 0;
  padding: 0;
  background-color: #f8f9fa00;
}
.label {
  position: absolute;
  top: 0;
  transition: all 0.3s ease;
}
.label_focus {
  top: -20px;
  font-size: 12px;
  color: gray;
}
.input-multiple {
  width: 100%;
  height: 30px;
  position: relative;
  border-bottom: solid 1px gray;
}
</style>
