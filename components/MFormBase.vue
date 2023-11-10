<template>
  <div>
    <div :class="classContainer">
      <div :class="classContainerPrepend" v-if="prepend">
        <slot name="prepend"></slot>
      </div>
      <div :class="classContainerPrepend" v-if="prependIcon">
        <v-icon>{{ prependIcon }}</v-icon>
      </div>
      <div :class="classContainerLabel" v-if="label">
        <label :for="id">{{ label }}</label>
      </div>
      <div :class="classContainerInput">
        <slot></slot>
      </div>
      <div :class="classContainerAppend" v-if="append">
        <slot name="append"></slot>
      </div>
      <div :class="classContainerAppend" v-if="appendIcon">
        <v-icon>{{ appendIcon }}</v-icon>
      </div>
    </div>
    <div v-if="errormsg" class="red--text">{{ errormsg }}</div>
  </div>
</template>

<script>
// import Utils from "../plugins/utils";

export default {
  name: "m-form-base",
  props: {
    //  value: [String, Number],
    id: {
      default: "",
      type: String,
    },
    picker: {
      default: "",
      type: String,
    },
    placeholder: String,
    //  type: {
    //    default: "text",
    //    type: String
    //  },
    //  name: {
    //    default: "",
    //    type: String
    //  },
    label: {
      default: "",
      type: String,
    },
    labelWidth: {
      default: 4,
      type: Number,
    },
    labelPosition: {
      default: "left",
      type: String,
    },
    labelFontSize: {
      default: "medium",
      type: String,
    },
    errormsg: {
      default: "",
      type: String,
    },
    //  autocomplete: {
    //    default: "new-password",
    //    type: String
    //  },
    //  readonly: {
    //    default: false,
    //    type: Boolean
    //  },
    //  disabled: {
    //    default: false,
    //    type: Boolean
    //  },
    /* autoSelectOnFocus: {
      default: true,
      type: Boolean
    }, */
    prependIcon: String,
    appendIcon: String,
    // prepend: String,
    // append: String
  },
  mounted() {
    if (this.$slots.prepend) this.prepend = true;
    if (this.$slots.append) this.append = true;
    // if (!this.id2) this.id2 = this.$Utils.randomstring();
    // console.log("this.labelWidth", this.labelWidth);
  },
  data() {
    let classContainer = { "m-form-base": true };
    let classContainerLabel = { "m-form-base__container-label": true };
    let classContainerInput = { "m-form-base__container-input": true };
    let classContainerPrepend = { "m-form-base__container-prepend": true };
    let classContainerAppend = { "m-form-base__container-append": true };
    if (this.labelWidth) {
      classContainerLabel["w" + this.labelWidth] = true;
      classContainerInput["w" + (12 - this.labelWidth)] = true;
    } else {
      classContainerLabel.w4 = true;
      classContainerInput.w8 = true;
    }
    if (!this.label) classContainerInput.w12 = true;
    if (this.labelPosition == "top") {
      classContainer["m-form-base__labeltop"] = true;
    }
    if (this.labelPosition == "right") {
      classContainer["m-form-base__labelright"] = true;
    }
    return {
      // id2: "",
      prepend: false,
      append: false,
      classContainer,
      classContainerLabel,
      classContainerInput,
      classContainerPrepend,
      classContainerAppend,
    };
  },
  watch: {
    value: function (val) {},
  },
  components: {},
  methods: {
    onclick(evt) {
      // evt.stopPropagation();
      this.$emit("click", evt);
    },
    onfocus(evt) {
      if (this.autoSelectOnFocus) evt.target.select();
      // evt.stopPropagation();
      // this.$emit("click", evt);
    },
  },
};
</script>

<style lang="scss">
.m-form-checkbox {
  white-space: nowrap;
}

.m-form-base {
  display: flex;
  label {
    display: block;
    line-height: 1rem;
  }
  .m-form-base__container-input {
    display: flex;
    margin-bottom: 3px;
    input {
      background-color: white;
      color: black;
      width: 100%;
      padding: 1px 5px;
      border: 1px solid #cfd8dc;

      &[disabled] {
        background-color: #cfd8dc;
      }
    }
    textarea {
      background-color: white;
      color: black;
      width: 100%;
      padding: 1px 5px;
      border: 1px solid #cfd8dc;
      // height: 100px;
      &[disabled] {
        background-color: #cfd8dc;
      }
    }
    .m-form-base__picker {
      i {
        background-color: #4e4e4e;
        color: white;
        // border: 1px solid #cfd8dc;
        // border-left: 0px solid #cfd8dc;
        height: 100%;
        border-radius: 2px;
      }
    }
    .m-form-radio {
      margin-right: 20px;
      input {
        width: auto;
      }
    }
    .m-form-checkbox {
      margin-right: 20px;
      input {
        width: auto;
      }
    }
  }
  .m-form-base__container-label {
    padding: 6px 5px 0 0;
    font-size: small;
  }
  .m-form-base__container-prepend {
    padding: 4px 5px 0 0;
  }
  .m-form-base__container-append {
    padding: 4px 0 0 5px;
  }
  @for $i from 1 through 12 {
    .w#{$i} {
      width: (100% * $i / 12);
    }
  }
  &.m-form-base__labeltop {
    flex-direction: column;
    .m-form-base__container-label {
      width: 100%;
      padding-bottom: 4px;
    }
    .m-form-base__container-input {
      width: 100%;
    }
  }
  &.m-form-base__labelright {
    flex-direction: row-reverse;
    .m-form-base__container-label {
      padding-left: 5px;
      padding-right: 0;
    }
    // .m-form-base__container-input {
    // }
  }
}
</style>
