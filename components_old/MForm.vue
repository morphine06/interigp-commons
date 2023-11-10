<template>
  <form action="return false">
    <slot></slot>
  </form>
</template>

<script>
export default {
  name: "mform",
  props: {
    rules: {
      default: function() {
        return [];
      },
      type: Array
    }
  },
  mounted() {},
  data() {
    return {};
  },
  watch: {},
  components: {},
  methods: {
    _getChildren() {
      let children = [];
      function getchildren(obj) {
        if (
          obj.$options.name == "mformtext" ||
          obj.$options.name == "mformselect" ||
          obj.$options.name == "mformdate" ||
          obj.$options.name == "mformdatehour"
        ) {
          children.push(obj);
        }
        for (let iChild = 0; iChild < obj.$children.length; iChild++) {
          getchildren(obj.$children[iChild]);
        }
      }
      getchildren(this);
      return children;
    },
    async validate() {
      let oks = true,
        children = this._getChildren();
      for (let iChild = 0; iChild < children.length; iChild++) {
        const child = children[iChild];
        let ok = await child.validate();
        if (!ok) oks = false;
      }

      for (let iRule = 0; iRule < this.rules.length; iRule++) {
        const rule = this.rules[iRule];
        let ok = await rule(children);
        if (this.$_.isString(ok) || ok === false) oks = false;
      }
      return oks;
    }
  }
};
</script>
