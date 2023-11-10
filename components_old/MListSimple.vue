<template>
  <div class="mlistsimple-container hscroll">
    <!-- @click="showOrHideTableMenu = false" -->
    <table class="table relative" :class="tableClass">
      <thead class="thead-light">
        <tr class="tr-head" style="">
          <slot name="ths"></slot>
          <div
            class="dropdown ellipsis"
            v-if="this.urlExportCSV || this.urlExportPDF"
          >
            <div
              class="pointer"
              @click.stop="showOrHideTableMenu = !showOrHideTableMenu"
            >
              <icon name="ellipsis-v"></icon>
            </div>
            <ul class="dropdown-menu menu-table" v-if="showOrHideTableMenu">
              <li v-if="this.urlExportCSV">
                <div @click="exportTable('csv')" class="dropdown-item">
                  Export CSV
                </div>
              </li>
              <li v-if="this.urlExportPDF">
                <div @click="exportTable('pdf')" class="dropdown-item">
                  Export PDF
                </div>
              </li>
            </ul>
          </div>
        </tr>
      </thead>
      <draggable v-if="dragable" tag="tbody" :list="items" handle=".handle">
        <tr
          v-for="(item, indexItem) in items"
          :key="item[itemValue]"
          :class="getTrClass(item)"
          @click="onitemclick(item, indexItem, $event)"
        >
          <slot name="tds" :item="item"></slot>
        </tr>
      </draggable>

      <tbody v-if="!dragable">
        <tr
          v-for="(item, indexItem) in items"
          :key="item[itemValue]"
          :class="getTrClass(item)"
          @click="onitemclick(item, indexItem, $event)"
        >
          <slot name="tds" :item="item"></slot>
        </tr>
      </tbody>
    </table>
    <paginate
      v-if="pagging && nbPage > 1"
      v-model="currentPage2"
      :page-count="nbPage"
      :page-range="3"
      :margin-pages="2"
      :first-last-button="true"
      first-button-text="<img style='height:12px' src='/images/icons/angle-double-left.png' alt='page suivante'> </img>"
      last-button-text="<img style='height:12px' src='/images/icons/angle-double-right.png' alt='page suivante'> </img>"
      :click-handler="clickCallback"
      next-text="<img style='height:12px' src='/images/icons/angle-right.png' alt='page suivante'> </img>"
      prev-text="<img style='height:12px' src='/images/icons/angle-left.png' alt='page suivante'> </img>"
      :container-class="'pagination'"
      :page-class="'page-item'"
      prev-class="page-item"
      next-class="page-item"
      page-link-class="page-link"
      prev-link-class="page-link"
      next-link-class="page-link"
    >
    </paginate>
  </div>
</template>

<script>
// différent de MList, MList a beaucoup de complexité ! Il faudra peut-être par contre rajouter ici un système de paging...
export default {
  name: "mlistsimple",
  // components: { draggable },
  props: {
    items: {
      default: function() {
        return [];
      },
      type: Array
    },
    /*     small: {
      default: false,
      type: Boolean
    }, */
    dragable: {
      default: false,
      type: Boolean
    },
    cursorOnRows: {
      default: false,
      type: Boolean
    },
    itemValue: {
      default: "",
      type: String
    },
    pagging: {
      default: false,
      type: Boolean
    },
    nbPage: {
      default: 10,
      type: Number
    },
    currentPage: {
      default: 1,
      type: Number
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
    itemSelected: {
      default: "",
      type: [String, Number]
    },
    urlExportCSV: {
      default: "",
      type: String
    },
    urlExportPDF: {
      default: "",
      type: String
    },
    tableClass: String,
    backgroundColorTrConditionKey: {
      default: "",
      type: String
    },
    backgroundColorTrConditionValue: {
      default: "",
      type: String
    }
  },
  mounted() {
    this.update(0);
    window.addEventListener("click", this.onClickWindow);
  },
  destroyed() {
    window.removeEventListener("click", this.onClickWindow);
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
      dragging: false,
      showOrHideTableMenu: false,
      currentPage2: this.currentPage
    };
  },
  watch: {
    items(val) {
      this.currentPage2 = this.currentPage;
      this.$nextTick(() => {});
    },
    total(val) {
      // this.calcPadding();
    },
    currentPage2(pageNum) {
      this.$emit("changePage", pageNum);
    }
  },
  computed: {
    draggingInfo() {
      return this.dragging ? "under drag" : "";
    }
  },
  components: {},
  methods: {
    clickCallback: function(pageNum) {
      // this.$emit("changePage", pageNum);
    },
    onClickWindow() {
      this.showOrHideTableMenu = false;
    },
    tableMenu() {
      this.showOrHideTableMenu = !this.showOrHideTableMenu;
    },
    async exportTable(type) {
      if (type === "pdf") {
        /*  this.$router.push({
          name: "printtablepdf",
          params: { table: "users" }
        }); */
        window.open(this.urlExportPDF, "_blank");
      } else {
        window.open(this.urlExportCSV, "_blank");
      }
    },
    getTrClass(item) {
      let cls = "";
      if (item[this.itemValue] === this.itemSelected) cls += " table-active";
      if (item.mListSimpleTrClass) cls += " " + item.mListSimpleTrClass;
      if (this.cursorOnRows) cls += " pointer";
      if (
        item[this.backgroundColorTrConditionKey] ===
        this.backgroundColorTrConditionValue
      )
        cls += " backgroundColor";

      return cls;
    },
    update(scrollTop) {
      this.$nextTick(() => {});
    },
    // scrollToTop() {
    //   this.$refs.container.scrollTop = 0;
    // },
    // onscroll(evt, arg2) {
    //   // console.log("evt", evt.target.scrollTop);
    //   if (this.timerScroll) window.clearTimeout(this.timerScroll);
    //   this.timerScroll = window.setTimeout(() => {
    //     this.scrollTop = evt.target.scrollTop;
    //     let skip = Math.floor(this.scrollTop / this.itemHeight);
    //     this.$emit("changerange", skip, this.scrollTop);
    //   }, 100);
    // },
    onitemclick(item, indexItem, evt) {
      this.$emit("itemclick", item, indexItem, evt);
    }
  }
};
</script>

<style lang="scss">
.table .thead-light th {
  background-color: #fff;
}
.table {
  thead th {
    text-transform: uppercase;
  }
  tr.table-active {
    background-color: lighten(#0c6efd, 10%);
    color: #fff !important;
  }
}
.handle {
  float: left;
  padding-top: 8px;
  padding-bottom: 8px;
}
/* input {
  display: inline-block;
  width: 50%;
} */
.ellipsis {
  position: absolute;
  right: 0px;
  top: 0px;
  padding: 10px;
}
.menu-table {
  display: inline-table !important;
  top: 40px !important;
  right: 0 !important;
  left: auto !important;
  li {
    width: 100%;
  }
}
.backgroundColor {
  background-color: #d6f1fb;
}
</style>
