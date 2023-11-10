<template>
  <div class="form-group">
    <label class="label" :class="labelClass" :for="id2" v-if="label">{{
      label
    }}</label>
    <div class="input-group">
      <v-select
        ref="myinput"
        v-model="value2"
        label="text"
        :options="items"
        style="width: 100%"
        :disabled="disabled"
        :clearable="clearable"
        @search:focus="onfocus"
        @search="onsearch"
        :appendToBody="appendToBody"
      ></v-select>

      <div :id="id2" class="invalid-feedback" v-if="errormsg2">
        {{ errormsg2 }}
      </div>
    </div>
    <small
      class="sub-text form-text text-muted"
      v-if="subText"
      v-html="subText"
    ></small>
  </div>
</template>

<script>
// import { createPopper } from "@popperjs/core";
// import MFormBase from "./MFormBase";
// import { createPopper } from "@popperjs/core";

export default {
  name: "mformselect",
  // extends: MFormBase,
  props: {
    value: [String, Number, Object, Boolean],
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
    clearable: {
      default: true,
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
    appendToBody: {
      default: false,
      type: Boolean
    },
    items: {
      default: function() {
        return [];
      },
      type: Array
    }
  },
  mounted() {
    if (!this.id2) this.id2 = this.$Utils.randomstring();
    this.$nextTick(() => {
      // this.popper = createPopper(
      //   this.$refs.myinput.$el,
      //   this.$refs.mydropdown,
      //   {
      //     placement: "bottom-start"
      //   }
      // );
    });
    //window.addEventListener("click", this.onWindowClick);
  },
  /*  destroyed() {
    window.removeEventListener("click", this.onWindowClick);
  }, */

  data() {
    // let name2 = this.name ? this.name : "";
    let rawvalue = this.findItem(this.value).text;
    let subTextValue;
    if (this.findItem(this.value).subtext) {
      subTextValue = this.findItem(this.value).subtext;
    } else {
      subTextValue = this.subText;
    }
    this.findItem(this.value).subtext;
    let value2 = this.findItem(this.value);
    return {
      id2: this.id,
      // name2,
      value2,
      rawvalue,
      subTextValue,
      errormsg2: "",
      showDropdown: false,
      focus: null,
      focusDropdown: null
    };
  },
  computed: {
    labelClass() {
      let classTxt = "";
      if (
        this.$_.isPlainObject(this.value2) &&
        ((!this.value2.value && this.value2.value === 0) || !this.value2.value)
      ) {
        return this.value2.value === 0 ? "label_focus" : classTxt;
      }
      classTxt +=
        this.focus ||
        (!this.value2.value && this.value2.value === 0) ||
        this.value2.value
          ? "label_focus"
          : "";
      return classTxt;
    }
  },
  watch: {
    rawvalue: function(val) {
      // console.log("rawvalue", val);
      this.$emit("search", val);
      if (val == "") this.$emit("input", "");
    },
    value2: function(val) {
      // console.log("value2", val);
      let v = null;
      if (val) v = val.value;
      this.$emit("input", v);
    },
    value: function(val) {
      // console.log("value", val);
      this.rawvalue = this.findItem(val).text;
      this.value2 = this.findItem(val);
      this.subTextValue = this.findItem(this.value).subtext;
    },
    focusDropdown: function(val) {
      this.rawvalue = val.item.text;
    },
    items: function(val) {
      // this.$nextTick(() => {
      //   this.popper.update();
      // });
    }
  },
  components: {},
  methods: {
    ShowDropdownMenu(showDropdown) {
      this.showDropdown = showDropdown;
      // this.popper.update();
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
          this.setValue(this.focusDropdown.item);
          this.ShowDropdownMenu(false);
          break;
      }
      this.$emit("onkeyup", evt);
    },
    onsearch(evt) {
      // console.log("onsearch", evt);
      this.$emit("search", evt);
    },
    onfocus(evt) {
      // console.log("ofcus");
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
    setValue(item) {
      this.value2 = item;
      this.validate();
      this.$emit("input", item.value);
    }
  }
};
</script>
<style lang="scss">
// .focusDropdown {
//   background-color: #f8f9fa;
// }
// .input-group-text {
//   border: 0;
//   padding: 0;
//   background-color: #f8f9fa00;
// }
// .vs__dropdown-toggle {
//   border: 0 !important;
//   border-bottom: 1px solid #999999 !important;
//   border-radius: 0 !important;
// }
.vs__dropdown-menu li {
  width: 100%;
}
.vs__selected {
  padding: 2px 2px 0 !important;
  margin: 0 2px 0 !important;
}
.vs__actions {
  padding: 0px 6px 0 3px !important;
}
.vs__dropdown-toggle {
  padding: 0 !important;
}
</style>
