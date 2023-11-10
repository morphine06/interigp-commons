<template>
  <div class="m-hour" style="width: 520px">
    <div class="hours d-flex flex-column mb-2">
      <div class="titre">Les heures</div>

      <div class="d-flex flex-row flex-wrap mt-1">
        <div
          :class="hourSelected === hour ? 'selected' : ''"
          @click="setHour(hour)"
          class="form-control btn-time"
          v-for="hour in tabHours1"
          :key="hour"
        >
          {{ hour }}
        </div>
      </div>
    </div>
    <div class="monutes d-flex flex-column">
      <div class="titre">Les minutes</div>
      <div class="d-flex flex-row flex-wrap mt-1">
        <div
          :class="minuteSelected === minute ? 'selected' : ''"
          @click="setMinute(minute)"
          class="form-control btn-time"
          v-for="minute in tabMinutes"
          :key="minute"
        >
          {{ minute }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
//import moment from "moment";

export default {
  name: "m-hour",
  props: {
    value: {
      default: function () {
        return "00 : 00";
      },
      type: String,
    },
    intervalMinute: {
      default: function () {
        return 5;
      },
      type: Number,
    },
  },
  data() {
    let tabMinutes = [];
    for (let i = 0; i < 60; i += this.intervalMinute) {
      tabMinutes.push(i < 10 ? "0" + i : i + "");
    }
    return {
      hourSelected: "00",
      minuteSelected: "00",
      tabHours1: [
        "00",
        "01",
        "02",
        "03",
        "04",
        "05",
        "06",
        "07",
        "08",
        "09",
        "10",
        "11",
        "12",
        "13",
        "14",
        "15",
        "16",
        "17",
        "18",
        "19",
        "20",
        "21",
        "22",
        "23",
      ],
      tabMinutes: tabMinutes,
    };
  },
  watch: {},
  mounted() {
    if (this.value) {
      let tabhour = this.value.split(":");
      this.hourSelected = tabhour[0];
      this.minuteSelected = tabhour[1];
    }
  },
  methods: {
    setHour(hour) {
      this.hourSelected = hour;
      this.setTime();
    },
    setMinute(minute) {
      this.minuteSelected = minute;
      this.setTime();
    },
    setTime() {
      this.$emit("setTime", {
        hour: this.hourSelected,
        minutes: this.minuteSelected,
      });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
// @import "../../node_modules/bootstrap/scss/bootstrap.scss";
.m-hour {
  padding: 15px;
  .titre {
    border-bottom: solid 1px gray;
  }
  .btn-time {
    width: 40px;
    cursor: pointer;
  }
  .selected {
    background-color: green;
  }
}
</style>
