<template>
  <div class="form-group textarea-markdown">
    <label
      :class="{
        label: true,
      }"
      :for="id2"
      v-if="label"
      >{{ label }}</label
    >
    <div
      class="
        container-btn-markdown
        d-flex
        justify-content-between
        align-items-center
      "
    >
      <div class="d-flex ms-4">
        <div
          @click="write"
          class="pointer container-icon"
          :class="writeOrShow === 'write' ? 'bis' : ''"
        >
          Écrire
        </div>
        <div
          @click="showResult"
          class="pointer container-icon ms-2"
          :class="writeOrShow === 'show' ? 'bis' : ''"
        >
          Visualiser
        </div>
      </div>
      <div class="d-flex me-4">
        <div @click="insertAtCursor('**', 2)" class="container-icon">
          <icon name="bold"></icon>
        </div>
        <div @click="insertAtCursor('*', 2)" class="container-icon">
          <icon name="italic"></icon>
        </div>
        <div @click="insertAtCursor('### ', 1)" class="container-icon">
          <img style="width: 27px" src="/images/icons/h1.png" alt="icon H1" />
        </div>
        <div @click="insertAtCursor('#### ', 1)" class="container-icon">
          <img style="width: 27px" src="/images/icons/h2.png" alt="icon H2" />
        </div>
        <div @click="insertAtCursor('##### ', 1)" class="container-icon">
          <img style="width: 27px" src="/images/icons/h3.png" alt="icon H3" />
        </div>
      </div>
    </div>
    <div class="input-group" v-if="writeOrShow === 'write'">
      <!--  <div class="input-group-prepend" v-if="prependText">
        <span class="input-group-text" v-html="prependText"></span>
      </div> -->
      <textarea
        ref="myinput"
        style="border-radius: 0"
        :aria-haspopup="ariaHaspopup"
        :aria-expanded="ariaExpanded"
        :rows="rows"
        :name="name"
        class="textarea"
        :class="{
          'form-control': true,
          'is-invalid': errormsg2,
        }"
        :id="id2"
        :disabled="disabled"
        aria-describedby
        :placeholder="placeholder"
        :value="value2"
        :autocomplete="autocomplete"
        @click="onclick($event)"
        @input="oninput($event)"
        @focus="onfocus($event)"
        @blur="onblur($event)"
        @keyup="onkeyup($event)"
        @keydown="onkeydown($event)"
      />
      <div :id="id2" class="invalid-feedback" v-if="errormsg2">
        {{ errormsg2 }}
      </div>
    </div>
    <div
      class="show-result"
      v-if="writeOrShow === 'show'"
      v-html="markdownResult"
    ></div>
    <div class="btn-help small-text">
      <div v-if="subText">
        {{ subText }}
        <hr class="m-1" />
      </div>
      Vous pouvez mettre en page votre texte à l'aide du langage markdown.
      <br />
      <button
        @click="helpMarkdown = !helpMarkdown"
        class="btn btn-primary btn-sm"
      >
        Afficher l'aide markdown
      </button>
      <div v-if="helpMarkdown" class="m-2">
        <table>
          <thead>
            <tr>
              <th style="width: 80px">Élement</th>
              <th style="width: 150px">Écrire</th>
              <th style="width: 150px">Résultat</th>
              <th>Explication</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(tr, index) in tableHelpMarkdown" :key="index">
              <td class="text-start">{{ tr.element }}</td>
              <td class="text-start" v-html="tr.write"></td>
              <td class="text-start" v-html="tr.show"></td>
              <td class="text-start">{{ tr.explication }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
const MarkdownIt = require("markdown-it");
const markdown = new MarkdownIt();
export default {
  name: "mformtextmarkdown",
  props: {
    value: [String, Number],
    id: {
      default: "",
      type: String,
    },
    name: {
      default: "",
      type: String,
    },
    rows: {
      default: 4,
      type: Number,
    },
    autocomplete: {
      default: "new-password",
      type: String,
    },
    readonly: {
      default: false,
      type: Boolean,
    },
    disabled: {
      default: false,
      type: Boolean,
    },
    autoSelectOnFocus: {
      default: true,
      type: Boolean,
    },
    label: {
      default: "",
      type: String,
    },
    tooltip: {
      default: "",
      type: String,
    },
    tooltipPosition: {
      default: "bottom",
      type: String,
    },
    ariaHaspopup: {
      default: false,
      type: Boolean,
    },
    ariaExpanded: {
      default: false,
      type: Boolean,
    },
    rules: {
      default: function () {
        return [];
      },
      type: Array,
    },
    placeholder: String,
    appendText: String,
    subText: String,
  },
  mounted() {
    if (this.id2) this.id2 = this.id;
    else this.id2 = this.$Utils.randomstring();
    // if (this.value2) this.id2 = this.id;
    // else this.id2 = this.$Utils.randomstring();
    window.addEventListener("click", this.onWindowClick);
  },
  destroyed() {
    window.removeEventListener("click", this.onWindowClick);
  },
  data() {
    return {
      id2: "",
      value2: this.value,
      errormsg2: "",
      focus: false,
      helpMarkdown: false,
      writeOrShow: "write",
      markdownResult: "",
      tableHelpMarkdown: [
        {
          element: "Titre",
          write: `###•Titre <br /> #####•Petit titre`,
          show: this.tableResult(`### Titre \n ##### Petit titre`),
          explication: `Ajouter des dièzes et un espace devant le titre. plus vous ajoutez de dièze, plus le titre sera petit`,
        },
        {
          element: "Paragraphe",
          write: `texte texte texte¶<br />¶<br />texte texte¶`,
          show: this.tableResult(`texte texte texte\n\ntexte texte`),
          explication: `Pour faire un nouveau paragraphe, sauter deux lignes, c'est à
                dire laisser une ligne vide entre les deux paragraphes. En
                effet, ne sauter qu'une seule ligne n'aura aucun effet et le
                texte de résultat sera en continu.`,
        },
        {
          element: "Retour ligne",
          write: `texte texte texte••¶<br />texte texte¶`,
          show: this.tableResult(`texte texte texte  \ntexte texte`),
          explication: `Pour faire un simple retour à la ligne, mettre deux espaces en
                fin de ligne.`,
        },
        {
          element: "Ligne de séparation",
          write: `texte texte texte¶<br />¶<br />----------¶<br />texte texte¶`,
          show: this.tableResult(
            `texte texte texte\n\n----------\ntexte texte`
          ),
          explication: `sauté une ligne puis mettre une série de tirets.`,
        },
        {
          element: "Texte gras",
          write: `**texte gras**`,
          show: this.tableResult(`**texte gras**`),
          explication: `encadrez le texte avec deux étoiles sans espaces.`,
        },
        {
          element: "Texte italique",
          write: `*texte italique*`,
          show: this.tableResult(`*texte italique*`),
          explication: `encadrez le texte avec une étoile sans espaces.`,
        },
      ],
    };
  },
  watch: {
    value: function (val) {
      // console.log("val", val);
      // if (val) this.focus = true;
      this.value2 = val;
    },
  },
  components: {},
  computed: {},
  methods: {
    onWindowClick(evt) {
      // si je click sur le document
      if (!evt.target.name) {
        //this.showDropdown = false;
        this.$emit("hideOrShowDropdown", false);
      }
      let input = this.$store.getters.get_inputOpened;
      // si je click un imput
      if (input && input.name !== this.name) {
        //this.showDropdown = false;
        this.$emit("hideOrShowDropdown", false);
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
        let ok = rule(this.value2);
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
    oninput(evt) {
      let v = evt.target.value;
      this.value2 = v;
      this.validate();
      this.$emit("input", this.value2, evt);
    },
    onclick(evt) {
      this.$emit("hideOrShowDropdown", true);
      let input = this.$store.getters.get_inputOpened;
      if (!input || (input && input.name !== evt.target.name)) {
        this.$store.commit("set_inputOpened", this);
      }
      this.$emit("click", evt);
    },
    onfocus(evt) {
      this.$emit("hideOrShowDropdown", true);
      if (this.autoSelectOnFocus) evt.target.select();
      let input = this.$store.getters.get_inputOpened;
      if (!input || (input && input.name !== evt.target.name)) {
        this.$store.commit("set_inputOpened", this);
      }
      this.$emit("focus", evt);
    },
    onblur() {
      if (!this.value2 || !this.value) {
        this.focus = false;
      }
    },
    hideDropdown() {
      this.$emit("hideOrShowDropdown", false);
    },
    insertAtCursor(myValue, number) {
      let myField = this.$refs.myinput;
      //IE support
      if (document.selection) {
        myField.focus();
        let sel = document.selection.createRange();
        sel.text = myValue;
      }
      //MOZILLA and others
      else if (myField.selectionStart || myField.selectionStart == "0") {
        var startPos = myField.selectionStart;
        var endPos = myField.selectionEnd;
        this.value2 =
          myField.value.substring(0, startPos) +
          myValue +
          myField.value.substring(startPos, endPos);
        if (number === 2) this.value2 += myValue;
        this.value2 += myField.value.substring(endPos, myField.value.length);
      } else {
        myField.value += myValue;
      }
      this.$emit("input", this.value2);
    },
    write() {
      this.writeOrShow = "write";
    },
    showResult() {
      this.writeOrShow = "show";
      this.markdownResult = markdown.render(this.value2);
    },
    tableResult(val) {
      return markdown.render(val);
    },
  },
};
</script>

<style lang="scss" scoped>
.input-group > .form-control-plaintext,
.input-group > .custom-select,
.input-group > .custom-file {
  margin-bottom: 0px;
}
.textarea {
  background-color: #ffffff;
  border: 1px solid #eaeaea;
}
.textarea-markdown .label {
  position: relative;
}
.m-form-base {
  .m-form-base__container-input {
    textarea {
      border: 1px solid #eaeaea;
    }
  }
}
.sub-text {
  color: #4950579c;
}
.container-btn-markdown {
  width: 100%;
  border: 1px solid #eaeaea;
}
.container-icon {
  cursor: pointer;
  padding: 8px;
}
.container-icon:hover {
  background-color: #e5cf95;
  border-radius: 2px;
}
.btn-help {
  width: 100%;
  border: 1px solid #eaeaea;
  padding: 5px;
  cursor: pointer;
}
.show-result {
  border: 1px solid #eaeaea;
  height: 302px;
  width: 100%;
  padding: 6px 12px;
}
tr {
  font-size: 14px;
  border-bottom: 0.5px solid #ccc9c9;
}
td,
th {
  border-right: 0.5px solid #ccc9c9;
  border-left: 0.5px solid #ccc9c9;
  padding: 3px 10px;
  text-align: center;
}
th {
  background-color: #5a5a5a;
  color: #fff;
  text-transform: uppercase;
}
</style>
