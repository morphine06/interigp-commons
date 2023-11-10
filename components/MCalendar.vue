<template>
  <div class="m-calendar">
    <div v-if="view == 'days'">
      <div class="d-flex">
        <div @click="incrementMonth(-1, $event)">
          <icon class="pointer" name="arrow-left"></icon>
        </div>
        <div class="me-auto ms-auto" @click="changeView('months', $event)">
          {{ currentMonth.format("MMMM YYYY") }}&nbsp;
          <icon class="pointer" name="arrow-down"></icon>
        </div>
        <div @click="incrementMonth(1, $event)">
          <icon class="pointer" name="arrow-right"></icon>
        </div>
      </div>
      <div>
        <table class="m-calendar-days table">
          <tr>
            <td v-for="(dw, indexWe) in tabDaysWeek" :key="indexWe">
              {{ dw }}
            </td>
          </tr>
          <tr v-for="(ds, indexDs) in tabDays" :key="indexDs">
            <td
              v-for="(d, indexD) in ds"
              :key="indexD"
              @click="setDate(d)"
              :class="{
                othermonth: currentMonth.month() != d.month(),
                over: valueInterne.isSame(d),
              }"
            >
              {{ d.format("DD") }}
            </td>
          </tr>
        </table>
      </div>
    </div>
    <div v-if="view == 'months'">
      <div class="d-flex">
        <div @click="incrementYear(-1, $event)">
          <icon class="pointer" name="arrow-left"></icon>
        </div>
        <div class="me-auto ms-auto" @click="changeView('years', $event)">
          {{ currentMonth.format("YYYY") }}&nbsp;
          <icon class="pointer" name="arrow-down"></icon>
        </div>
        <div @click="incrementYear(1, $event)">
          <icon class="pointer" name="arrow-right"></icon>
        </div>
      </div>
      <div>
        <table class="m-calendar-months table">
          <tr v-for="(mos, indexMo) in tabMonths" :key="indexMo">
            <td
              v-for="(m, indexM) in mos"
              :key="indexM"
              @click="setMonth(m, $event)"
              :class="{
                over: $moment(valueInterne).startOf('month').isSame(m),
              }"
            >
              {{ m.format("MMMM") }}
            </td>
          </tr>
        </table>
      </div>
    </div>
    <div v-if="view == 'years'">
      <div class="d-flex">
        <div @click="incrementYear(-12, $event)">
          <icon class="pointer" name="arrow-left"></icon>
        </div>
        <div class="me-auto ms-auto">&nbsp;</div>
        <div @click="incrementYear(12, $event)">
          <icon class="pointer" name="arrow-right"></icon>
        </div>
      </div>
      <div>
        <table class="m-calendar-years table">
          <tr v-for="(yes, indexYe) in tabYears" :key="indexYe">
            <td
              v-for="(y, indexY) in yes"
              :key="indexY"
              @click="setYear(y, $event)"
              :class="{
                over: $moment(valueInterne).startOf('year').isSame(y),
              }"
            >
              {{ y.format("YYYY") }}
            </td>
          </tr>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import moment from "moment";

export default {
  name: "m-calendar",
  props: {
    value: {
      default: function () {
        return moment();
      },
      type: Object,
    },
    year: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      view: this.year ? "years" : "days",
      valueInterne: moment().startOf("day"),
      tabDays: [],
      tabDaysWeek: [],
      tabMonths: [],
      tabYears: [],
      currentMonth: moment().startOf("month"),
    };
  },
  mounted() {
    if (this.value) this.setEnterValue(this.value);
    this.render();
  },
  watch: {
    value: function (v) {
      // this.valueInterne = moment(v).startOf("day");
      this.setEnterValue(v);
      this.render();
    },
  },
  methods: {
    setEnterValue(v) {
      this.valueInterne = moment(v).startOf("day");
      this.currentMonth = moment(this.valueInterne).startOf("month");
    },
    changeView(view, evt) {
      if (evt) evt.stopPropagation();
      this.view = view;
    },
    incrementMonth(v, evt) {
      if (evt) evt.stopPropagation();
      evt.stopPropagation();
      // this.currentMonth = moment(this.currentMonth).add(v, "month");
      this.currentMonth.startOf("month").add(v, "month");
      this.render();
    },
    incrementYear(v, evt) {
      if (evt) evt.stopPropagation();
      evt.stopPropagation();
      // this.currentMonth = moment(this.currentMonth).add(v, "month");
      this.currentMonth.startOf("month").add(v, "year");
      this.render();
    },
    render() {
      this.createTabDays();
      this.createTabMonths();
      this.createTabYears();
    },
    createTabDays() {
      let tabDays = [];
      let currentMonth = moment(this.currentMonth).startOf("month");
      let w = 0;
      let startDay = currentMonth.day() - 1;
      if (startDay == -1) startDay = 6;
      for (let i = startDay * -1; i < 50; i++) {
        let d = moment(currentMonth).add(i, "day");
        if (!tabDays[w]) tabDays[w] = [];
        tabDays[w].push(d);
        if (d.day() == 0) w++;
        if (currentMonth.month() != d.month() && d.day() == 0) break;
      }
      // console.log("tabDays", tabDays);
      this.tabDaysWeek = ["L", "M", "M", "J", "V", "S", "D"];
      this.tabDays = tabDays;
    },
    createTabMonths() {
      let tabMonths = [];
      let m = -1;
      let start = moment(this.currentMonth).startOf("year");
      for (let i = 0; i < 12; i++) {
        if (i % 3 == 0) m++;
        if (!tabMonths[m]) tabMonths[m] = [];
        tabMonths[m].push(moment(start).add(i, "month"));
      }
      this.tabMonths = tabMonths;
    },
    createTabYears() {
      let tabYears = [];
      let y = -1;
      let start = moment(this.currentMonth).startOf("year").add(-6, "year");
      for (let i = 0; i < 12; i++) {
        if (i % 3 == 0) y++;
        if (!tabYears[y]) tabYears[y] = [];
        tabYears[y].push(moment(start).add(i, "year"));
      }
      this.tabYears = tabYears;
    },
    setDate(d) {
      // console.log("d", d.format("YYYY-MM-DD"));
      // this.rawvalue = d.format("YYYY-MM-DD")
      this.valueInterne = moment(d).startOf("day");
      this.$emit("input", this.valueInterne);
    },
    setMonth(m, evt) {
      if (evt) evt.stopPropagation();
      this.view = "days";
      this.currentMonth = m;
      this.render();
      // this.$emit("input", this.valueInterne, "month");
    },
    setYear(m, evt) {
      if (this.year) {
        this.valueInterne = moment(m).startOf("year");
        this.$emit("input", this.valueInterne);
      } else {
        if (evt) evt.stopPropagation();
        this.view = "months";
        this.currentMonth = m;
        this.render();
      }
      // this.$emit("input", this.valueInterne, "year");
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
// @import "../../node_modules/bootstrap/scss/bootstrap.scss";
.m-calendar {
  padding: 15px;
  .m-calendar-days,
  .m-calendar-months,
  .m-calendar-years {
    table-layout: fixed;
    width: 300px;
    td {
      cursor: pointer;
      text-align: center;
      &.othermonth {
        color: #cccccc;
      }
      &.over {
        background-color: green;
        color: white;
      }
    }
  }
}
</style>
