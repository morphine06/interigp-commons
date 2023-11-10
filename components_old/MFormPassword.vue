<template>
  <div id="password">
    <div class="row">
      <div :class="display === 'col' ? 'col-md-12' : 'col-md-6'">
        <div class="relative">
          <m-form-text
            label="Mot de passe"
            :type="showPassword ? 'text' : 'password'"
            v-model="password1"
            :name="$Utils.randomstring('pass')"
            autocomplete="new-password"
            :class="
              valid === 'shadow' && valid_password ? 'input-shadow-ok' : ''
            "
            @input="checkStrongPassword"
          ></m-form-text>
          <div class="icon-eyes pointer" @click="showPassword = !showPassword">
            <icon v-if="!showPassword" name="eye"></icon>
            <icon v-if="showPassword" name="eye-slash"></icon>
          </div>
          <div
            v-if="valid === 'checkmark'"
            class="checkmark_container"
            :class="{ show_checkmark: valid_password }"
          >
            <icon width="20" color="#fff" name="check"></icon>
          </div>
        </div>
      </div>
      <div :class="display === 'col' ? 'col-md-12' : 'col-md-6'">
        <div class="relative">
          <m-form-text
            label="Répétez le mot de passe"
            :type="showPassword2 ? 'text' : 'password'"
            v-model="password2"
            :name="$Utils.randomstring('pass2')"
            autocomplete="new-password"
            :class="
              valid === 'shadow' && same_password ? 'input-shadow-ok' : ''
            "
            @input="checkSamePassword"
          ></m-form-text>
          <div
            class="icon-eyes pointer"
            @click="showPassword2 = !showPassword2"
          >
            <icon v-if="!showPassword2" name="eye"></icon>
            <icon v-if="showPassword2" name="eye-slash"></icon>
          </div>
          <div
            v-if="valid === 'checkmark'"
            class="checkmark_container"
            :class="{ show_checkmark2: same_password }"
          >
            <icon width="20" color="#fff" name="check"></icon>
          </div>
        </div>
      </div>
      <div
        class="alert alert-danger mt-2"
        :class="display === 'col' ? 'col-md-12' : 'col-md-6'"
        v-if="alert_password"
      >
        {{ alert_password }}
      </div>
    </div>
    <div class="row mt-2" v-if="showRules">
      <div class="col-md-12">
        <p class="mb-0">Le mot de passe doit contenir au moins :</p>
        <ul>
          <li :class="{ is_valid: password_length_ok }">
            {{ rules.nbCharacteres }} caractères
          </li>
          <li v-if="rules.uppercase" :class="{ is_valid: contains_uppercase }">
            1 majuscule
          </li>
          <li v-if="rules.number" :class="{ is_valid: contains_number }">
            1 nombre
          </li>
          <li
            v-if="rules.specialCharactere"
            :class="{ is_valid: contains_special_character }"
          >
            1 caractère spécial
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "mformpassword",
  components: {},
  props: {
    /* rules définit les règle à respecter pour le mot de passe, objet attendu :
      {
        nbCharacteres:Number,
        specialCharactere:Boolean,
        uppercase:Boolean,
        number:Boolean
      } 
      */
    rules: Object,
    // "checkmark" |  "shadow"  comment s'affiche la validation de l'input
    valid: String,
    // "col" | "row" affiche les champs en ligne ou en colonne
    display: String,
    // si on affiche les regles ou non
    showRules: Boolean,
    // si le mot de passe  est requi
    required: Boolean
  },
  data() {
    return {
      password1: "",
      password2: "",
      password_length: 0,
      password_length_ok: false,
      contains_number: false,
      contains_uppercase: false,
      contains_special_character: false,
      valid_password: false,
      same_password: false,
      showPassword: false,
      showPassword2: false,
      alert_password: ""
    };
  },
  computed: {},
  watch: {},
  async mounted() {},
  destroyed() {},
  methods: {
    checkStrongPassword() {
      this.password1 = this.password1.trim();
      this.password2 = this.password2.trim();
      this.password_length = this.password1.length;
      // test pour le caractère special
      const format = /[ !@#$%^&*()_+\-=\[\]{};':"\\|,.<>\/?]/;
      if (this.password_length >= this.rules.nbCharacteres) {
        this.password_length_ok = true;
      } else {
        this.password_length_ok = false;
      }

      this.contains_number = this.rules.number
        ? /\d/.test(this.password1)
        : true;
      this.contains_uppercase = this.rules.uppercase
        ? /[A-Z]/.test(this.password1)
        : true;
      this.contains_special_character = this.rules.specialCharactere
        ? format.test(this.password1)
        : true;

      if (
        this.password_length_ok === true &&
        this.contains_special_character === true &&
        this.contains_uppercase === true &&
        this.contains_number === true
      ) {
        this.valid_password = true;
      } else {
        this.valid_password = false;
        // this.err_password = ["le mot de passe n'est pas assez sécurisé"];
      }
      this.checkSamePassword();
    },
    checkSamePassword() {
      if (!this.required && this.password1 === "" && this.password2 === "") {
        this.$emit("validPassword", true);
        return;
      }
      this.same_password =
        this.password1 === this.password2 && this.valid_password ? true : false;

      // calcule erreurs
      let err_password = [];
      if (!this.valid_password)
        err_password.push("le mot de passe n'est pas assez sécurisé");
      if (this.password1 !== this.password2)
        err_password.push("les mots de passe ne sont pas identiques");
      this.$emit(
        "validPassword",
        this.same_password,
        err_password,
        this.password1
      );
    }
  }
};
</script>
<style lang="scss">
.checkmark_container {
  border-radius: 50%;
  position: absolute;
  bottom: 0;
  right: -40px;
  background: #6fccb2;
  width: 30px;
  height: 30px;
  visibility: hidden;
  opacity: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: opacity 0.4s ease;
}

.show_checkmark,
.show_checkmark2 {
  visibility: visible;
  opacity: 1;
}
.input-shadow-ok .input-group {
  box-shadow: 0px 0px 4px 4px #4cb366;
}
li {
  color: #525f7f;
  position: relative;
  width: fit-content;
}

li:before {
  content: "";
  width: 0%;
  height: 2px;
  background: #2ecc71;
  position: absolute;
  left: 0;
  top: 50%;
  display: block;
  transition: all 0.6s ease;
}
// .is_valid {
//   //color: rgba(136, 152, 170, 0.8);
//   //text-decoration: line-through;
// }
.is_valid:before {
  width: 100%;
}

.icon-eyes {
  position: absolute;
  top: 0;
  right: 0;
  width: 20px;
  height: 20px;
}
</style>
