<template>
  <div class="form-group">
    <label class="label" :for="id2" v-if="label">{{ label }}</label>
    <div class="input-group" :class="inputGroupClass">
      <div class="input-group-prepend" v-if="prependText">
        <span class="input-group-text" v-html="prependText"></span>
      </div>
      <input
        v-if="type !== 'textarea'"
        ref="myinput"
        :aria-haspopup="ariaHaspopup"
        :aria-expanded="ariaExpanded"
        :type="type"
        :name="name"
        :class="{
          'form-control': true,
          'is-invalid': errormsg2
        }"
        :maxlength="maxlength"
        :id="id2"
        :disabled="disabled"
        aria-describedby
        :placeholder="placeholder"
        :value="rawvalue"
        :autocomplete="autocomplete"
        v-tooltip
        :data-bs-placement="tooltipPosition"
        :title="tooltip"
        @click="onclick($event)"
        @input="oninput($event)"
        @focus="onfocus($event)"
        @blur="onblur($event)"
        @keyup="onkeyup($event)"
        @keydown="onkeydown($event)"
      />
      <textarea
        v-if="type === 'textarea'"
        ref="myinput"
        :aria-haspopup="ariaHaspopup"
        :aria-expanded="ariaExpanded"
        :name="name"
        :class="{
          textarea: true,
          'form-control': true,
          'is-invalid': errormsg2
        }"
        :id="id2"
        :disabled="disabled"
        aria-describedby
        :value="rawvalue"
        :autocomplete="autocomplete"
        :placeholder="placeholder"
        :rows="rows"
        v-tooltip
        :data-bs-placement="tooltipPosition"
        :title="tooltip"
        @click="onclick($event)"
        @input="oninput($event)"
        @focus="onfocus($event)"
        @blur="onblur($event)"
        @keyup="onkeyup($event)"
        @keydown="onkeydown($event)"
      >
      </textarea>
      <div class="input-group-prepend" v-if="appendText || appendIcon">
        <span
          v-if="appendText"
          class="input-group-text"
          v-html="appendText"
        ></span>
        <span v-if="appendIcon" class="input-group-text">
          <icon :name="appendIcon"></icon>
        </span>
      </div>
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
export default {
  name: "mformtext",
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
    rows: {
      default: 4,
      type: Number
    },
    maxlength: {
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
    ariaHaspopup: {
      default: false,
      type: Boolean
    },
    ariaExpanded: {
      default: false,
      type: Boolean
    },
    rules: {
      default: function() {
        return [];
      },
      type: Array
    },
    inputGroupClass: String,
    placeholder: String,
    prependIcon: String,
    appendIcon: String,
    prependText: String,
    appendText: String,
    subText: String
  },
  mounted() {
    if (this.id2) this.id2 = this.id;
    else this.id2 = this.$Utils.randomstring();
    // if (this.rawvalue) this.id2 = this.id;
    // else this.id2 = this.$Utils.randomstring();
    this.setRawValue(this.value);

    window.addEventListener("click", this.onWindowClick);
  },
  destroyed() {
    window.removeEventListener("click", this.onWindowClick);
  },
  data() {
    return {
      id2: "",
      rawvalue: "",
      errormsg2: "",
      focus: false
    };
  },
  watch: {
    value: function(val) {
      this.setRawValue(val);
      // if (val) this.focus = true;
    }
  },
  computed: {},
  components: {},
  methods: {
    onWindowClick(evt) {
      // si je click sur le document
      if (!evt.target.name) {
        //this.showDropdown = false;
        // console.log("onWindowClick 1");
        this.$emit("ShowDropdownMenu", false);
      }
      let input = this.$store.getters.get_inputOpened;
      // si je click un imput
      if (input && input.name !== this.name) {
        //this.showDropdown = false;
        // console.log("onWindowClick 2");
        this.$emit("ShowDropdownMenu", false);
      }
    },
    informValid() {
      this.errormsg2 = "";
    },
    informInvalid(txt) {
      this.errormsg2 = txt;
    },
    validate() {
      let kos = [];
      for (let iRule = 0; iRule < this.rules.length; iRule++) {
        const rule = this.rules[iRule];
        let ok = rule(this.rawvalue);
        if (this.$_.isString(ok)) kos.push(ok);
      }
      if (kos.length == 0) {
        this.informValid();
        return true;
      }
      this.informInvalid(kos.join(", "));
      return false;
    },
    onkeyup(evt) {
      this.$emit("keyup", evt);
    },
    onkeydown(evt) {
      this.$emit("keydown", evt);
    },
    setRawValue(v, KeepZero = false) {
      if (this.type == "number") {
        if ((v + "").indexOf(",")) {
          v = (v + "").replace(/\,/, ".");
          v = v * 1;
          this.rawvalue = v;
        }
        if (v === "0" || v === 0) {
          if (!KeepZero) this.rawvalue = "";
          v = 0;
        }
        if (v === "") {
          v = 0;
        }
      } else {
        this.rawvalue = v;
      }
      return v;
    },
    oninput(evt) {
      let v = evt.target.value;
      v = this.setRawValue(v, true);
      //this.value3 = v;
      // console.log("this.rawvalue", this.rawvalue);
      this.validate();
      this.$emit("input", v, evt);
    },
    onclick(evt) {
      this.$emit("ShowDropdownMenu", true);
      let input = this.$store.getters.get_inputOpened;
      if (!input || (input && input.name !== evt.target.name)) {
        this.$store.commit("set_inputOpened", this);
      }
      this.$emit("click", evt);
    },
    onfocus(evt) {
      if (this.type !== "textarea") this.focus = true;
      this.$emit("ShowDropdownMenu", true);
      if (this.autoSelectOnFocus) evt.target.select();
      let input = this.$store.getters.get_inputOpened;
      if (!input || (input && input.name !== evt.target.name)) {
        this.$store.commit("set_inputOpened", this);
      }
      this.$emit("focus", evt);
    },
    onblur() {
      if (!this.rawvalue || !this.value) {
        this.focus = false;
      }
    },
    hideDropdown() {
      // console.log("hideDropdown");
      this.$emit("ShowDropdownMenu", false);
    }
  }
};
</script>

<style lang="scss" scoped>
.input-group > .form-control-plaintext,
.input-group > .custom-select,
.input-group > .custom-file {
  margin-bottom: 10px;
}
.textarea {
  background-color: #ffffff;
  border: 1px solid #eaeaea;
}
.m-form-base {
  .m-form-base__container-input {
    textarea {
      border-radius: 5px;
      border: 1px solid gray;
    }
  }
}
.input-group-xs .form-control {
  height: 26px;
  font-size: 13px;
}
// .sub-text {
//   color: #4950579c;
// }
</style>
