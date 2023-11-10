<template>
  <div class="mlist-container">
    <div class="mlist-container-headers" ref="headers">
      <table>
        <thead>
          <tr>
            <slot name="ths"></slot>
          </tr>
        </thead>
      </table>
    </div>
    <div
      v-if="dragable"
      class="mlist-container-datas"
      v-on:scroll="onscroll"
      ref="container"
    >
      <div :class="{ 'mlist-contains-parent': true, bgstrip1: bgstrip1 }">
        <div class="mlist-contains" ref="contains">
          <table>
            <draggable tag="tbody" :list="items" class="" handle=".handle">
              <tr
                v-for="(item, indexItem) in items"
                :key="item[itemValue]"
                :class="getTrClass(item)"
                :style="`height:${itemHeight2}px;`"
                @click="onitemclick(item, indexItem, $event)"
              >
                <slot name="tds" :item="item"></slot>
              </tr>
            </draggable>
          </table>
          <!--  <vuedraggable
            tag="ul"
            :list="items"
            class="list-group"
            handle=".handle"
          >
            <li
              v-for="(item, indexItem) in list"
              :key="item[itemValue]"
              :class="getTrClass(item)"
              :style="`height:${itemHeight2}px;`"
              @click="onitemclick(item, indexItem, $event)"
            >
              <slot name="tds" :item="item"></slot>
            </li>
          </vuedraggable> -->
        </div>
      </div>
    </div>

    <div
      v-if="!dragable"
      class="mlist-container-datas"
      v-on:scroll="onscroll"
      ref="container"
    >
      <div :class="{ 'mlist-contains-parent': true, bgstrip1: bgstrip1 }">
        <div class="mlist-contains" ref="contains">
          <table>
            <tbody>
              <tr
                v-for="(item, indexItem) in items"
                :key="item[itemValue]"
                :class="getTrClass(item)"
                :style="`height:${itemHeight2}px;`"
                @click="onitemclick(item, indexItem, $event)"
              >
                <slot name="tds" :item="item"></slot>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "mlist",
  // components: { draggable },
  props: {
    items: {
      default: function() {
        return [];
      },
      type: Array
    },
    dragable: {
      default: false,
      type: Boolean
    },
    current: {
      default: "",
      type: [String, Number]
    },
    itemValue: {
      default: "",
      type: String
    },
    dynamic: {
      default: false,
      type: Boolean
    },
    limit: {
      default: 100,
      type: Number
    },
    total: {
      default: 0,
      type: Number
    },
    skip: {
      default: 0,
      type: Number
    },
    itemHeight: {
      default: 30,
      type: Number
    },
    bgstrip1: {
      default: false,
      type: Boolean
    }
  },
  mounted() {
    this.update(0);
  },
  data() {
    return {
      scrollTop: 0,
      // skip: 0,
      timerScroll: null,
      list: [
        { name: "John", text: "", id: 0 },
        { name: "Joao", text: "", id: 1 },
        { name: "Jean", text: "", id: 2 }
      ],
      dragging: false
    };
  },
  watch: {
    items(val) {
      this.$nextTick(() => {
        let trs = this.$refs.contains.querySelectorAll("tbody tr");
        let tds_header = this.$refs.headers.querySelectorAll("thead tr th");
        if (trs.length) {
          let tds = trs[0].querySelectorAll("td");
          for (let iTd = 0; iTd < tds.length; iTd++) {
            const td = tds[iTd];
            // console.log("td.offsetWidth", td.offsetWidth);
            if (tds_header[iTd])
              tds_header[iTd].style.width = td.offsetWidth + "px";
          }
        }
        // console.log("trs", trs);
      });
    },
    total(val) {
      // this.calcPadding();
    }
  },
  computed: {
    itemHeight2() {
      return this.itemHeight - 0;
    },
    draggingInfo() {
      return this.dragging ? "under drag" : "";
    }
  },
  components: {},
  methods: {
    getTrClass(item) {
      let cls = "mlist-item";
      if (this.current == item[this.itemValue]) cls += " active";
      if (item.mListTrClass) cls += " " + item.mListTrClass;
      return cls;
    },
    update(scrollTop) {
      this.$nextTick(() => {
        if (!this.dynamic) {
          this.$refs.contains.style.paddingTop = "0px";
          this.$refs.contains.style.paddingBottom = "0px";
          return;
        }

        let t = 0,
          b = 0;
        //hContainer = this.$refs.container.offsetHeight
        //   console.log("hContainer", hContainer, this.total, this.items.length);
        t = this.skip * this.itemHeight;
        b =
          this.total * this.itemHeight -
          t -
          this.items.length * this.itemHeight;
        if (b < 0) b = 0;
        this.$refs.contains.style.paddingTop = t + "px";
        this.$refs.contains.style.paddingBottom = b + "px";
        if (scrollTop >= 0) this.$refs.container.scrollTop = scrollTop;
      });
    },
    scrollToTop() {
      this.$refs.container.scrollTop = 0;
    },
    onscroll(evt, arg2) {
      // console.log("evt", evt.target.scrollTop);
      if (this.timerScroll) window.clearTimeout(this.timerScroll);
      this.timerScroll = window.setTimeout(() => {
        this.scrollTop = evt.target.scrollTop;
        let skip = Math.floor(this.scrollTop / this.itemHeight);
        this.$emit("changerange", skip, this.scrollTop);
      }, 100);
    },
    onitemclick(item, indexItem, evt) {
      this.$emit("itemclick", item, indexItem, evt);
    }
  }
};
</script>

<style lang="scss" scoped>
.mlist-item {
  cursor: pointer;
}
// @import "../sass/variables.scss";
.mlist-container {
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  .mlist-container-headers {
    flex-grow: 0;
  }
  .mlist-container-datas {
    flex-grow: 1;
    overflow: auto;
    .mlist-contains-parent {
      background-repeat: repeat;
      &.bgstrip1 {
        background-image: url(/img/background-stripped.png);
      }
    }
    .mlist-contains {
      padding: 0;
    }
  }
  table {
    width: 100%;
    border-spacing: 0;
    border-collapse: collapse;
    table-layout: fixed;
    tr {
      td,
      th {
        padding: 0 10px;
        margin: 0;
        cursor: pointer;
        box-sizing: border-box;
        //  -moz-box-sizing: border-box;
        //  -webkit-box-sizing: border-box;
        border-bottom: 1px solid #e0e0e0;
        font-size: 0.813rem;
        line-height: 0.813rem;
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
        font-size: 0.75rem;
        text-align: left;
      }
    }
    thead {
      th {
        padding: 5px 10px;
      }
    }
    tbody {
      tr {
        // &:hover {
        //   // background-color: $mygris;
        // }
        // &.active {
        //   // background-color: $mybleu-clair;
        //   // td {
        //   //   color: $mybleu-fonce;
        //   //   i {
        //   //     color: $mybleu-fonce;
        //   //   }
        //   // }
        // }
        &.contact-bg-black {
          background-color: black;
          color: white;
          i {
            color: white;
          }
        }
        &.contact-bg-grey {
          background-color: #d0cde0;
        }

        &.contact-bg-grey {
          background-color: #d0cde0;
        }
        &.offers-bg-otheragency {
          background-color: #c8e5fb;
        }
        &.offers-bg-confidential {
          background-color: #cdebd1;
        }
      }
    }
  }
}

.handle {
  float: left;
  padding-top: 8px;
  padding-bottom: 8px;
}
input {
  display: inline-block;
  width: 50%;
}
.text {
  margin: 20px;
}
</style>
