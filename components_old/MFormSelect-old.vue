<template>
  <m-form-base
    :id="id2"
    :label="label"
    :label-width="labelWidth"
    :label-position="labelPosition"
    :prepend-icon="prependIcon"
    :errormsg="errormsg2"
  >
    <v-menu offset-y allow-overflow auto offset-overflow>
      <template v-slot:activator="{ on }">
        <div v-on="on" class="m-form-select">
          <input
            type="text"
            ref="myinput"
            :id="id2"
            :name="name2"
            :readonly="readonly"
            :autocomplete="autocomplete"
            :disabled="disabled"
            :value="rawvalue"
            @focus="onfocus"
            @input="oninput($event)"
            outlined
          />
          <div class="m-form-base__picker" @click="onfocus">
            <v-icon>mdi-menu-down</v-icon>
          </div>
        </div>
      </template>
      <v-list>
        <div v-for="(item, index) in items" :key="index">
          <v-list-item @click="setValue(item)" v-if="item.text">
            <v-list-item-title>{{ item.text }}</v-list-item-title>
          </v-list-item>
          <v-divider v-if="!item.text"></v-divider>
        </div>

        <!-- <v-list-item v-for="(item, index) in items" :key="index" @click="setValue(item)">
          <v-list-item-title>{{ item.text }}</v-list-item-title>
        </v-list-item>-->
      </v-list>
    </v-menu>
  </m-form-base>
</template>

<script>
import MFormBase from "./MFormBase";

export default {
  name: "mformselect",
  extends: MFormBase,
  props: {
    value: [String, Number, Object],
    // searchInput: Function,
    items: {
      default: function() {
        return [];
      },
      type: Array
    },
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
      default: true,
      type: Boolean
    },
    autoSelectOnFocus: {
      default: false,
      type: Boolean
    },
    disabled: {
      default: false,
      type: Boolean
    },
    nodatatext: {
      default: "",
      type: String
    },
    rules: {
      default: function() {
        return [];
      }
    }
  },
  mounted() {
    if (!this.id2) this.id2 = this.$Utils.randomstring();
  },
  data() {
    let name2 = this.name ? this.name : "";
    let rawvalue = this.findItem(this.value).text;
    let value2 = this.findItem(this.value);
    return {
      id2: this.id,
      name2,
      value2,
      rawvalue,
      errormsg2: ""
    };
  },
  watch: {
    rawvalue: function(val) {
      this.$emit("search", val);
      if (val == "") this.$emit("input", "");
    },
    value: function(val) {
      this.rawvalue = this.findItem(val).text;
      this.value2 = this.findItem(val);
    }
  },
  components: {},
  methods: {
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
    oninput(evt) {
      this.rawvalue = evt.target.value;
    },
    onfocus(evt) {
      if (this.autoSelectOnFocus) this.$refs.myinput.select();
      this.$emit("focus", evt);
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
.m-form-select {
  display: flex;
  width: 100%;
  input {
    cursor: pointer;
  }
}
.m-form-base {
  .m-form-base__container-input .m-form-select {
    input {
      border-radius: 5px 0 0 5px;
    }
  }
}
</style>
