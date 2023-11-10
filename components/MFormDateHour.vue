<template>
  <m-form-base
    :id="id2"
    :label="label"
    :label-width="labelWidth"
    :label-position="labelPosition"
    :errormsg="errormsg2"
  >
    <v-menu
      v-model="menudate"
      :close-on-content-click="false"
      offset-y
      min-width="290px"
    >
      <template v-slot:activator="{ on }">
        <div v-on="on" class="m-form-select">
          <input
            v-if="!hidedate"
            type="text"
            ref="myinput"
            :readonly="readonly"
            :autocomplete="autocomplete"
            :disabled="disabled"
            :value="rawdate"
            @focus="onfocus"
            @input="rawdate = $event.target.value"
            :id="id2"
          />
          <div class="m-form-base__picker" v-if="!hidedate">
            <v-icon>mdi-calendar</v-icon>
          </div>
        </div>
      </template>
      <v-date-picker
        :first-day-of-week="1"
        locale="fr-fr"
        v-model="date"
        no-title
        @input="menudate = false"
      ></v-date-picker>
    </v-menu>
    <v-menu
      v-model="menutime"
      :close-on-content-click="false"
      offset-y
      min-width="290px"
    >
      <template v-slot:activator="{ on }">
        <div v-on="on" class="m-form-select">
          <input
            type="text"
            ref="inputtime"
            :readonly="readonly"
            :autocomplete="autocomplete"
            :disabled="disabled"
            :value="rawtime"
            @focus="onfocus2"
            @input="rawtime = $event.target.value"
          />
          <div class="m-form-base__picker">
            <v-icon>mdi-timer</v-icon>
          </div>
        </div>
      </template>
      <v-time-picker
        locale="fr-fr"
        v-if="menutime"
        v-model="time"
        @click:minute="menutime = false"
      ></v-time-picker>
    </v-menu>
  </m-form-base>
</template>

<script>
import MFormBase from "./MFormBase";

export default {
  name: "m-form-date-hour",
  extends: MFormBase,
  props: {
    id: {
      default: "",
      type: String,
    },
    name: {
      default: "",
      type: String,
    },
    value: [String, Object],
    prefix: String,
    autocomplete: {
      default: "new-password",
      type: String,
    },
    readonly: {
      default: true,
      type: Boolean,
    },
    hidedate: {
      default: false,
      type: Boolean,
    },
    usemoment: {
      default: false,
      type: Boolean,
    },
    disabled: {
      default: false,
      type: Boolean,
    },
    rules: {
      default: function () {
        return [];
      },
      type: Array,
    },
  },
  mounted() {
    if (!this.id2) this.id2 = this.$Utils.randomstring();
    if (this.value) this.setValue(this.value);
  },
  data() {
    let date = this.$moment().format("YYYY-MM-DD");
    let time = "00:00:00";
    return {
      id2: this.id,
      errormsg2: "",
      rawdate: "",
      rawtime: "",
      date,
      time,
      menudate: false,
      menutime: false,
    };
  },
  watch: {
    date: function (val) {
      let d = this.date + " " + this.time;
      // console.warn("d1", d);
      console.warn("d", d);
      if (this.usemoment) d = this.$moment(d);
      this.$emit("input", d);
    },
    time: function (val) {
      let d = this.date + " " + this.time;
      if (this.usemoment) d = this.$moment(d);
      // console.log("d2", d);
      this.$emit("input", d);
    },
    value: function (val) {
      // console.log("val", val, this.name);
      this.setValue(val);
    },
  },
  components: {},
  methods: {
    forceDate(val) {
      this.setValue(val);
    },
    setValue(val) {
      if (this.$_.isString(val) && val == "0000-00-00") {
        this.rawdate = "";
        this.rawtime = "";
        this.date = this.$moment().format("YYYY-MM-DD");
        this.time = this.$moment().format("HH:mm:[00]");
      } else {
        let d = this.$moment(val, "YYYY-MM-DD HH:mm:ss");
        this.rawdate = d.format("DD/MM/YYYY");
        // console.log("this.rawdate", this.name, this.rawdate);
        this.rawtime = d.format("HH:mm");
        this.date = d.format("YYYY-MM-DD");
        this.time = d.format("HH:mm:[00]");
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
        let ok = rule(this.value2);
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
    },
    onfocus2(evt) {
      this.$emit("focus", evt);
    },
  },
};
</script>
