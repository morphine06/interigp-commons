<!--
Modiifer également le projet XXX
-->
<template>
  <div>
    <div class="frame">
      <h4>Édition du vin</h4>
      <div class="row">
        <div class="col-md-6">
          <m-form-text label="Nom du vin *"
            class="mb-2"
            v-model="row_wi.wi_name"
            :name="$Utils.randomstring('wi_name')"></m-form-text>
          <m-form-text label="Complément du nom"
            class="mb-2"
            v-model="row_wi.wi_name2"
            :name="$Utils.randomstring('wi_name2')"></m-form-text>
          <m-form-text label="Autre(s) nom(s) commercial(iaux)"
            class="mb-2"
            v-model="row_wi.wi_name3"
            :name="$Utils.randomstring('wi_name3')"></m-form-text>
          <!-- <m-form-select label="Dénomination *"
            id="selectDenomination"
            class="mb-2"
            :name="$Utils.randomstring('de_id')"
            :items="denominations"
            v-model="denomination"
            :rules="[$Validation.mandatory]"
            @input="changeSelect('denomination')"></m-form-select> -->
          <m-form-combobox label="Dénomination *"
            :name="$Utils.randomstring('denomination')"
            v-model="row_wi.denomination"
            :clearable="false"
            class="mb-2"
            :store-url="$config.server_url + '/candidats/1.0/denominations'"
            item-value="de_id"
            item-text="de_name"
            :store-params="{
              sort: 'de_name ASC',
              onlymine: true,
            }">
          </m-form-combobox>
          <m-form-select label="Couleur *"
            class="mb-2"
            id="selectCouleur"
            :name="$Utils.randomstring('wi_couleur')"
            :items="this.$store.state.items_winesColors"
            item-value="key"
            item-text="text"
            v-model="row_wi.wi_couleur"
            :rules="[$Validation.mandatory]"
            @input="changeSelect('couleur')"></m-form-select>
          <m-form-select label="Millésime *"
            class="mb-2"
            :name="$Utils.randomstring('wi_milesime')"
            :items="millesimes"
            v-model="row_wi.wi_millesime"
            :rules="[$Validation.mandatory]"
            @input="changeSelect('millesime')"></m-form-select>
          <!-- <m-form-text label="Cépages" type="textarea" :rows="3" v-model="row_wi.wi_cepages"
            :name="$Utils.randomstring('wi_cepages')"></m-form-text> -->
          <div class="row">
            <div class="col-md-6">
              <m-form-select label="Catégorie"
                class="mb-2"
                id="selectCategorie"
                :name="$Utils.randomstring('wi_categorie')"
                :items="this.$store.state.items_winesCategories"
                v-model="row_wi.wi_categorie"
                :rules="[$Validation.mandatory]"></m-form-select>
            </div>
            <div class="col-md-6">
              <m-form-select label="Type"
                class="mb-2"
                id="selectType"
                :name="$Utils.randomstring('wi_type')"
                :items="this.$store.state.items_winesTypes"
                v-model="row_wi.wi_type"
                :rules="[$Validation.mandatory]"></m-form-select>
            </div>
          </div>

          <div class="row">
            <div class="col-md-6">
              <m-form-combobox label="Cépage 1"
                :name="$Utils.randomstring('cepage1')"
                v-model="row_wi.cepage1"
                :clearable="false"
                class="mb-2"
                :store-url="$config.server_url + '/candidats/1.0/cepages'"
                item-value="cp_id"
                item-text="cp_name"
                :store-params="{
                  sort: 'cp_name ASC',
                  onlymine: true,
                }">
              </m-form-combobox>

              <!-- <m-form-select label="Cépage 1"
                class="mb-2"
                id="selectCepage1"
                :name="$Utils.randomstring('cepage1')"
                :items="this.$store.state.items_winesCepages"
                v-model="row_wi.cepage1"
                :rules="[$Validation.mandatory]"></m-form-select> -->
            </div>
            <div class="col-md-6">
              <m-form-combobox label="Cépage 2"
                :name="$Utils.randomstring('cepage2')"
                v-model="row_wi.cepage2"
                :clearable="false"
                class="mb-2"
                :store-url="$config.server_url + '/candidats/1.0/cepages'"
                item-value="cp_id"
                item-text="cp_name"
                :store-params="{
                  sort: 'cp_name ASC',
                  onlymine: true,
                }">
              </m-form-combobox>

              <!-- <m-form-select label="Cépage 2"
                class="mb-2"
                id="selectCepage2"
                :name="$Utils.randomstring('cepage2')"
                :items="this.$store.state.items_winesCepages"
                v-model="row_wi.cepage2"
                :rules="[$Validation.mandatory]"></m-form-select> -->
            </div>
          </div>
          <div class="col-md-6">
            <m-form-combobox label="Cépage 3"
              :name="$Utils.randomstring('cepage3')"
              v-model="row_wi.cepage3"
              :clearable="false"
              class="mb-2"
              :store-url="$config.server_url + '/candidats/1.0/cepages'"
              item-value="cp_id"
              item-text="cp_name"
              :store-params="{
                sort: 'cp_name ASC',
                onlymine: true,
              }">
            </m-form-combobox>
            <!-- <m-form-select label="Cépage 3"
              class="mb-2"
              id="selectCepage3"
              :name="$Utils.randomstring('cepage3')"
              :items="this.$store.state.items_winesCepages"
              v-model="row_wi.cepage3"
              :rules="[$Validation.mandatory]"></m-form-select> -->
          </div>
        </div>
        <div class="col-md-6">
          <m-form-text label="Numéro de lot *"
            class="mb-2"
            v-model="row_wi.wi_numlot"
            :name="$Utils.randomstring('wi_numlot')"></m-form-text>
          <m-form-text label="OU Référence(s) du(des) contenant(s) *"
            class="mb-2"
            v-model="row_wi.wi_refcontenant"
            :name="$Utils.randomstring('wi_refcontenant')"></m-form-text>
          <m-form-text label="Volume total du lot (en hl)"
            class="mb-2"
            v-model="row_wi.wi_volume"
            :name="$Utils.randomstring('wi_volume')"></m-form-text>

          <m-form-select class="width200"
            label="Etes-vous le détenteur du lot présenté ?"
            :name="$Utils.randomstring('wi_detenteurlot')"
            v-model="row_wi.wi_detenteurlot"
            :items="$store.state.items_boolean_int"></m-form-select>
          <m-form-text class="mb-2"
            label="Information détenteur"
            :name="$Utils.randomstring('wi_detenteurinfo')"
            v-model="row_wi.wi_detenteurinfo"
            type="textarea"></m-form-text>

          <h4 class="mt-3">Reste à commercialiser</h4>
          <div v-if="contenance_min">
            <div class="row mb-2 d-flex align-items-end"
              v-for="(contenant, index) in contenants"
              :key="index">
              <div class="col-md-5">
                <m-form-select label="Contenant *"
                  :name="$Utils.randomstring('wi_contenant')"
                  :items="$store.state.items_contenants"
                  v-model="contenant.contenant"
                  :rules="[$Validation.mandatory]"
                  @input="calculContenance(contenant)"></m-form-select>
              </div>
              <div class="col-md-5 d-flex flex-no-wrap align-items-end">
                <m-form-text label="Nombre *"
                  type="number"
                  v-model="contenant.nombre"
                  :name="$Utils.randomstring('wi_nombre')"
                  @input="calculContenance(contenant)"></m-form-text>
                <div class="pointer ms-2"
                  @click="deleteContenant(index)">
                  <icon width="22"
                    height="22"
                    color="red"
                    name="times"></icon>
                </div>
              </div>
              <!-- <div class="col-md-1">
                <div class="pointer" @click="deleteContenant(index)">
                  <icon width="22" height="22" color="red" name="times"></icon>
                </div>
              </div> -->
            </div>
          </div>
          <button @click="addContenant"
            :disabled="!contenance_min"
            class="btn btn-primary btn-sm">
            Ajouter
          </button>
          <div class="fw-bold mt-3 ps-4"
            v-if="contenance_min">
            <div v-if="contenance_total > 0">
              {{ contenance_total / 100 }} cl soit
              {{ contenance_total / 10000 }} l soit
              {{ contenance_total / 1000000 }} hl
            </div>
            <div :class="contenance_total >= contenance_min
              ? 'text-success'
              : 'text-danger'
              ">
              le minimum est de : {{ contenance_min / 1000000 }} hl
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="frame">
      <h4>Fichiers</h4>
      <div class="row mb-4 d-flex align-items-center">
        <div class="col-md-7">
          <div class="row">
            <div class="col-md-5">
              <label class="label">Déclaration de revendication 1 *</label>
            </div>
            <div class="col-md-7">
              <m-form-file ref="inputFile1"
                id="file_1"
                @inputfile="fileJusteSelected1" />
            </div>
          </div>
          <div class="row mt-2"
            v-if="$Utils.isAdmin()">
            <div class="col-md-2">
              <div class="d-flex align-items-center">
                <label class="switch">
                  <input v-model="row_wi.switch_wi_revendication_valid"
                    type="checkbox"
                    :name="$Utils.randomstring('switch_wi_revendication_valid')"
                    @change="inputValidFile('wi_revendication_valid')" />
                  <span class="slider round"></span>
                </label>
              </div>
            </div>
            <div class="col-md-10">
              <m-form-text label=""
                class="mb-2"
                v-model="row_wi.wi_revendication_valid"
                :name="$Utils.randomstring('wi_revendication_valid')"></m-form-text>
            </div>
          </div>
        </div>
        <div class="col-md-1">
          <m-form-checkbox label="Effacer"
            :name="$Utils.randomstring('deleteFile1')"
            v-model="deleteFile1"></m-form-checkbox>
        </div>
        <div class="col-md-1">
          <img v-if="row_wi.wi_revendication"
            class="img-fluid"
            :src="getFileUrl(1)"
            alt="appercu fichier" />
        </div>
        <div class="col-md-2 d-flex justify-content-center">
          <button v-if="row_wi.wi_revendication"
            class="btn btn-primary btn-sm"
            @click="downloadFile(1)">
            Télécharger
          </button>
          <div v-else-if="wi_revendication_stop">
            <img style="width: 30px"
              src="/images/icons/icon-stop.png"
              alt="icon stop" />
          </div>
        </div>
        <div class="col-md-1">
          <button class="btn btn-primary bg-blue btn-sm"
            @click="addFile1('file1')">
            Ajouter
          </button>
        </div>
      </div>

      <div class="row mb-4 d-flex align-items-center"
        v-for="(autreFile, index) in filesAutreRevendication"
        :key="index">
        <div class="col-md-7">
          <div class="row">
            <div class="col-md-5">
              <label class="label">Déclaration de revendication {{ index + 2 }}</label>
            </div>
            <div class="col-md-7">
              <input type="file"
                class="form-control"
                :id="'inputFile-' + index"
                :name="'inputFile-' + index"
                accept="*/*"
                @change="fileJusteSelected10($event)" />
            </div>
          </div>
          <div class="row mt-2"
            v-if="$Utils.isAdmin()">
            <div class="col-md-2">
              <div class="d-flex align-items-center">
                <label class="switch">
                  <input v-model="row_wi['switch_wi_revendication' + (index + 2) + '_valid']
                    "
                    type="checkbox"
                    :name="$Utils.randomstring(
                      'switch_wi_revendication' + (index + 2) + '_valid'
                    )
                      "
                    @change="
                      inputValidFile(
                        'wi_revendication' + (index + 2) + '_valid'
                      )
                      " />
                  <span class="slider round"></span>
                </label>
              </div>
            </div>
            <div class="col-md-10">
              <m-form-text label=""
                class="mb-2"
                v-model="row_wi['wi_revendication' + (index + 2) + '_valid']"
                :name="$Utils.randomstring(
                  'wi_revendication' + (index + 2) + '_valid'
                )
                  "></m-form-text>
            </div>
          </div>
        </div>
        <div class="col-md-1">
          <m-form-checkbox label="Effacer"
            :name="$Utils.randomstring('deleteFile10')"
            v-model="autreFile.delete"></m-form-checkbox>
        </div>
        <div class="col-md-1">
          <img v-if="autreFile.download"
            class="img-fluid"
            :src="getFileUrl(index + 10)"
            alt="appercu fichier" />
        </div>
        <div class="col-md-2 d-flex justify-content-center">
          <button v-if="autreFile.download"
            class="btn btn-primary btn-sm"
            @click="downloadFile(index + 10)">
            Télécharger
          </button>
        </div>
      </div>

      <div class="row mb-4 d-flex align-items-center">
        <div class="col-md-7">
          <div class="row">
            <div class="col-md-5">
              <label class="label">Rapport d'analyse *</label>
            </div>
            <div class="col-md-7">
              <m-form-file ref="inputFile2"
                id="file_2"
                @inputfile="fileJusteSelected2" />
            </div>
          </div>
          <div class="row mt-2"
            v-if="$Utils.isAdmin()">
            <div class="col-md-2">
              <div class="d-flex align-items-center">
                <label class="switch">
                  <input v-model="row_wi.switch_wi_analyse_valid"
                    type="checkbox"
                    :name="$Utils.randomstring('switch_wi_analyse_valid')"
                    @change="inputValidFile('wi_analyse_valid')" />
                  <span class="slider round"></span>
                </label>
              </div>
            </div>
            <div class="col-md-10">
              <m-form-text label=""
                class="mb-2"
                v-model="row_wi.wi_analyse_valid"
                :name="$Utils.randomstring('wi_analyse_valid')"></m-form-text>
            </div>
          </div>
        </div>
        <div class="col-md-1">
          <m-form-checkbox label="Effacer"
            :name="$Utils.randomstring('deleteFile2')"
            v-model="deleteFile2"></m-form-checkbox>
        </div>
        <div class="col-md-1">
          <img v-if="row_wi.wi_analyse"
            class="img-fluid"
            :src="getFileUrl(2)"
            alt="appercu fichier" />
        </div>
        <div class="col-md-2 d-flex justify-content-center">
          <button v-if="row_wi.wi_analyse"
            class="btn btn-primary btn-sm"
            @click="downloadFile(2)">
            Télécharger
          </button>
          <div v-else-if="wi_analyse_stop">
            <img style="width: 30px"
              src="/images/icons/icon-stop.png"
              alt="icon stop" />
          </div>
        </div>
      </div>

      <div class="row mb-4 d-flex align-items-center">
        <div class="col-md-7">
          <div class="row">
            <div class="col-md-5">
              <label class="label">Fiche technique</label>
            </div>
            <div class="col-md-7">
              <m-form-file ref="inputFile3"
                id="file_3"
                @inputfile="fileJusteSelected3" />
            </div>
          </div>
          <div class="row mt-2"
            v-if="$Utils.isAdmin()">
            <div class="col-md-2">
              <div class="d-flex align-items-center">
                <label class="switch">
                  <input v-model="row_wi.switch_wi_fichetech_valid"
                    type="checkbox"
                    :name="$Utils.randomstring('switch_wi_fichetech_valid')"
                    @change="inputValidFile('wi_fichetech_valid')" />
                  <span class="slider round"></span>
                </label>
              </div>
            </div>
            <div class="col-md-10">
              <m-form-text label=""
                class="mb-2"
                v-model="row_wi.wi_fichetech_valid"
                :name="$Utils.randomstring('wi_fichetech_valid')"></m-form-text>
            </div>
          </div>
        </div>
        <div class="col-md-1">
          <m-form-checkbox label="Effacer"
            :name="$Utils.randomstring('deleteFile3')"
            v-model="deleteFile3"></m-form-checkbox>
        </div>
        <div class="col-md-1">
          <img v-if="row_wi.wi_fichetech"
            class="img-fluid"
            :src="getFileUrl(3)"
            alt="appercu fichier" />
        </div>
        <div class="col-md-2 d-flex justify-content-center">
          <button v-if="row_wi.wi_fichetech"
            class="btn btn-primary btn-sm"
            @click="downloadFile(3)">
            Télécharger
          </button>
        </div>
      </div>

      <div class="row mb-4 d-flex align-items-center"
        v-if="showphoto">
        <div class="col-md-3">
          <label class="label">Photo du vin (sera affiché sur le site web si le vin est
            médaillé)</label>
        </div>
        <div class="col-md-4">
          <m-form-file ref="inputFile4"
            id="file_4"
            @inputfile="fileJusteSelected4" />
        </div>
        <div class="col-md-1">
          <m-form-checkbox label="Effacer"
            :name="$Utils.randomstring('deleteFile4')"
            v-model="deleteFile4"></m-form-checkbox>
        </div>
        <div class="col-md-1">
          <img v-if="row_wi.wi_photo"
            class="img-fluid"
            :src="getFileUrl(4)"
            alt="appercu fichier" />
        </div>
        <div class="col-md-2 d-flex justify-content-center">
          <button v-if="row_wi.wi_photo"
            class="btn btn-primary btn-sm"
            @click="downloadFile(4)">
            Télécharger
          </button>
        </div>
      </div>
    </div>
    <m-message-dialog v-model="dialogErr"
      title="Erreur"
      :text="dialogErrTxt"></m-message-dialog>
    <div ref="myscript"></div>
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from "@/components/HelloWorld.vue";

export default {
  name: "FormWine",
  components: {},
  props: {
    wi_id: Number,
    from: String,
    showphoto: { default: false, type: Boolean },
  },
  data() {
    let millesimes = [];
    for (let i = 2010; i <= this.$dayjs().format("YYYY") * 1; i++) {
      millesimes.push({ text: i, value: i });
    }
    return {
      wi_revendication_stop: true,
      wi_analyse_stop: true,
      dialogErr: false,
      dialogErrTxt: "",
      confirmdelete: false,
      row_wi: {},
      row_pa: {},
      // denominations: [],
      // couleurs: [],
      millesimes: millesimes,
      // categories: [],
      // types: [],
      // cepages: [],
      ignoreSelects: false,
      denomination: "",
      couleur: "",
      millesime: "",
      contenants: [],
      contenance_total: 0,
      contenance_min: 0,
      //file
      filesSelected: {
        file1: null,
        file2: null,
        file3: null,
      },
      deleteFile1: false,
      deleteFile2: false,
      deleteFile3: false,
      deleteFile4: false,
      filesAutreRevendication: [],
      forcereload: this.$dayjs().format("YYYYMMDDHHmmss"),
    };
  },
  watch: {
    keyload: function (v) {},
  },
  async mounted() {
    this.contenants = [];
    let me = this;
    async function waitPrefLoaded() {
      return new Promise((accept, reject) => {
        function goHear() {
          window.setTimeout(() => {
            if (me.$store.state.yearObj.yp_script_contenance_min)
              return accept();
            goHear();
          }, 10);
        }
        goHear();
      });
    }
    await waitPrefLoaded();
    var script = document.createElement("script");
    script.innerHTML = this.$store.state.yearObj.yp_script_contenance_min;
    this.$refs.myscript.appendChild(script);
    // await this.loadDenominations();
    await this.loadWine();
  },
  methods: {
    getFileUrl(num) {
      let res = "";
      if (this.from === "backoffice")
        res = `${this.$config.server_url}/backoffice/1.0/wines/thumb/${this.row_wi.wi_id}/files/${num}/${this.row_wi.wi_year}?icon=true&forcereload=${this.forcereload}&token=${this.$store.state.accesstoken}&origin=${this.$config.backoffice_url}`;
      if (this.from === "candidats")
        res = `${this.$config.server_url}/candidats/1.0/wines/thumb/${this.row_wi.wi_id}/files/${num}/${this.row_wi.wi_year}?icon=true&forcereload=${this.forcereload}&token=${this.$store.state.accesstoken}&origin=${this.$config.candidats_url}`;
      return res;
    },

    async loadWine() {
      this.wi_revendication_stop = true;
      this.wi_analyse_stop = true;
      let route = this.from === "candidats" ? "candidats" : "backoffice";
      let params = {};
      if (this.wi_id == -1) params = {};
      let response = await this.$axios.get(
        this.$config.server_url + "/" + route + "/1.0/wines/" + this.wi_id,
        { params }
      );
      let row_wi = response.data.data;
      // contenance
      if (row_wi.wi_contenances) {
        let tabC = row_wi.wi_contenances.split(";;");
        for (let i = 0; i < tabC.length; i++) {
          const el = tabC[i];
          if (el.length) {
            let tab2 = el.split(":");
            this.contenants.push({
              contenant: parseInt(tab2[0]),
              nombre: parseInt(tab2[1]),
            });
          }
        }
        this.calculContenance();
      } else {
        this.contenants = [{ contenant: "", nombre: "" }];
      }
      this.ignoreSelects = true;
      setTimeout(() => {
        this.ignoreSelects = false;
      }, 100);

      row_wi.switch_wi_revendication_valid =
        row_wi.wi_revendication_valid.indexOf("Validé") == 0;
      row_wi.switch_wi_revendication2_valid =
        row_wi.wi_revendication2_valid.indexOf("Validé") == 0;
      row_wi.switch_wi_revendication3_valid =
        row_wi.wi_revendication3_valid.indexOf("Validé") == 0;
      row_wi.switch_wi_revendication4_valid =
        row_wi.wi_revendication4_valid.indexOf("Validé") == 0;
      row_wi.switch_wi_revendication5_valid =
        row_wi.wi_revendication5_valid.indexOf("Validé") == 0;
      row_wi.switch_wi_analyse_valid =
        row_wi.wi_analyse_valid.indexOf("Validé") == 0;
      row_wi.switch_wi_fichetech_valid =
        row_wi.wi_fichetech_valid.indexOf("Validé") == 0;
      this.row_wi = row_wi;

      let millesimes = [];
      for (let i = row_wi.wi_year; i > row_wi.wi_year - 4; i--) {
        millesimes.push({ text: i, value: i });
      }
      this.millesimes = millesimes;

      this.changeSelect();
      // fichiers autre revendication
      for (let i = 2; i < 6; i++) {
        if (this.row_wi["wi_revendication" + i]) {
          this.filesAutreRevendication.push({
            delete: false,
            file: null,
            download: true,
          });
        }
      }
    },
    defineSelectCouleur() {},
    defineSelectMillesimes() {},
    async changeSelect(what) {
      if (window.calculContenanceMin)
        this.contenance_min = window.calculContenanceMin(
          // this.denominations,
          [],
          this.row_wi
        );
    },

    calculContenance() {
      let total = 0;
      for (let i = 0; i < this.contenants.length; i++) {
        const el = this.contenants[i];
        total += el.contenant * el.nombre;
      }
      this.contenance_total = total;
    },
    inputValidFile(what) {
      this.row_wi[what] =
        "Validé le " + this.$dayjs().format("DD/MM/YYYY [à] HH[H]mm");
    },
    addContenant() {
      this.contenants.push({ contenant: "", nombre: "" });
    },
    deleteContenant(index) {
      this.contenants.splice(index, 1);
      this.calculContenance();
    },
    getFiles() {
      let files = [];
      if (this.filesSelected.file1) files.push("wi_revendication");
      if (this.filesSelected.file2) files.push("wi_analyse");
      return files;
    },
    fileJusteSelected1(files) {
      this.filesSelected.file1 = files[0];
      this.wi_revendication_stop = false;
      this.$emit("fileJustSelected", this.getFiles());
    },
    fileJusteSelected2(files) {
      this.filesSelected.file2 = files[0];
      this.wi_analyse_stop = false;
      this.$emit("fileJustSelected", this.getFiles());
    },
    fileJusteSelected3(files) {
      this.filesSelected.file3 = files[0];
    },
    fileJusteSelected4(files) {
      this.filesSelected.file4 = files[0];
    },
    fileJusteSelected10($event) {
      let index = parseInt($event.target.id.split("-")[1]);
      this.filesAutreRevendication[index].file = $event.target.files[0];
    },

    async saveFiles(file, num) {
      let route = this.from === "candidats" ? "candidats" : "backoffice";
      if (!file) return;
      let formData = new FormData();
      formData.append("file", file, file.name);
      await this.$axios.post(
        this.$config.server_url +
        "/" +
        route +
        "/1.0/wines/" +
        this.row_wi.wi_id +
        "/files/" +
        num +
        "/" +
        this.row_wi.wi_year,
        formData,
        {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        }
      );
    },
    async deleteFile(num) {
      let route = this.from === "candidats" ? "candidats" : "backoffice";
      // console.log("num", num);
      await this.$axios.delete(
        this.$config.server_url +
        "/" +
        route +
        "/1.0/wines/" +
        this.row_wi.wi_id +
        "/files/" +
        num +
        "/" +
        this.row_wi.wi_year
      );
      if (num < 10) {
        this["deleteFile" + num] = false;
        this.filesSelected["file" + num] = false;
      }
    },
    addFile1() {
      this.filesAutreRevendication.push({
        delete: false,
        file: null,
        download: false,
      });
    },
    downloadFile(num) {
      let route = this.from === "candidats" ? "candidats" : "backoffice";
      let url =
        this.from === "candidats"
          ? this.$config.candidats_url
          : this.$config.backoffice_url;
      // console.log("this.from, url", this.from, url);
      window.open(
        `${this.$config.server_url}/${route}/1.0/wines/${this.row_wi.wi_id}/files/${num}/${this.row_wi.wi_year}?token=${this.$store.state.accesstoken}&origin=${url}`,
        "_blank"
      );
    },
    wineValidChange(val) {
      this.row_wi.wi_valid = val;
    },
    wineMedailleChange(row_wi) {
      this.row_wi.wi_medaille = row_wi.wi_medaille;
      this.row_wi.wi_medaille_commentaire = row_wi.wi_medaille_commentaire;
      this.row_wi.wi_medaille_commentaire_other =
        row_wi.wi_medaille_commentaire_other;
      this.row_wi.wi_medaille_auteur = row_wi.wi_medaille_auteur;
      this.row_wi.wi_prixexcellence = row_wi.wi_prixexcellence;
    },
    tryToSaveWin(row_pa) {
      // console.log("row_pa", row_pa);
      // console.log("this.row_wi", this.row_wi);
      let err = [];
      let fieldRequired = [
        { field: "wi_name", text: "Nom" },
        // { field: "wi_denomination", text: "dénomination" },
        { field: "wi_couleur", text: "Couleur" },
        { field: "wi_millesime", text: "Millesime" },
      ];
      if (!row_pa.pa_id) err.push({ text: "Candiadat" });
      for (let ifi = 0; ifi < fieldRequired.length; ifi++) {
        const field = fieldRequired[ifi];
        if (!this.row_wi[field.field]) err.push(field);
      }
      if (!this.row_wi.wi_numlot && !this.row_wi.wi_refcontenant) {
        err.push({ text: "Numéro du lot ou référence des contenants" });
      }
      this.contenants = this.contenants.filter((item) => {
        return item.contenant != "" && item.nombre != "";
      });
      if (!this.contenants.length) {
        err.push({ text: "Reste à commercialiser" });
      }
      if (
        this.contenants.length &&
        this.contenance_total < this.contenance_min
      ) {
        err.push({
          text: "Minimum de contenance à commercialiser non atteint",
        });
      }

      if (err.length) {
        this.dialogErrTxt =
          "<span class='bis'>Les champs suivants sont obligatoires : </span><br>";
        for (let ierr = 0; ierr < err.length; ierr++) {
          const error = err[ierr];
          this.dialogErrTxt += "- " + error.text + " <br>";
        }
        this.dialogErr = true;
        return;
      }
      this.row_wi.participation = row_pa;
      this.saveWin();
    },
    trimFields() {
      let tabField = ["wi_name"];
      for (let i = 0; i < tabField.length; i++) {
        const field = tabField[i];
        this.row_wi[field] = this.row_wi[field].trim();
      }
    },
    async saveWin() {
      let route = this.from === "candidats" ? "candidats" : "backoffice";
      let response;
      // console.log("this.row_wi.wi_valid", this.row_wi.wi_valid);
      // delete this.row_wi.denomination;
      //contenance
      let contenanceString = "";
      for (let i = 0; i < this.contenants.length; i++) {
        const el = this.contenants[i];
        contenanceString += el.contenant + ":" + el.nombre + ";;";
      }
      this.row_wi.wi_contenances = contenanceString;
      // volume  et volume restant
      if (this.row_wi.wi_volume) {
        this.row_wi.wi_volumerestant =
          this.row_wi.wi_volume * 1 - this.contenance_total / 1000000;
      }
      if (this.row_wi.wi_id) {
        response = await this.$axios.put(
          this.$config.server_url +
          "/" +
          route +
          "/1.0/wines/" +
          this.row_wi.wi_id,
          this.row_wi
        );
      } else {
        this.row_wi.wi_year = this.$store.state.year;
        response = await this.$axios.post(
          this.$config.server_url + "/" + route + "/1.0/wines",
          this.row_wi
        );
      }
      if (response.data.err) {
        this.$store.dispatch("showDialogError", response.data.err.message);
        return;
      }
      this.row_wi = response.data.data;
      // les fichiers
      // console.log("this.filesSelected", this.filesSelected);
      for (let i = 1; i <= 4; i++) {
        if (this.filesSelected["file" + i] && !this["deleteFile" + i]) {
          await this.saveFiles(this.filesSelected["file" + i], i);
        }
        if (this["deleteFile" + i]) {
          await this.deleteFile(i);
        }
      }
      for (let j = 0; j < this.filesAutreRevendication.length; j++) {
        const file = this.filesAutreRevendication[j];
        if (file.file && !file.delete) {
          await this.saveFiles(file.file, j + 10);
        }
        if (file.delete) {
          await this.deleteFile(j + 10);
        }
      }
      this.forcereload = this.$dayjs().format("YYYYMMDDHHmmss");
      this.$emit("formWineAction", { action: "saved" });
    },
    deleteConfirmWin() {
      this.confirmdelete = true;
    },
    async deleteWin() {
      let route = this.from === "candidats" ? "candidats" : "backoffice";
      await this.$axios.delete(
        this.$config.server_url +
        "/" +
        route +
        "/1.0/wines/" +
        this.row_wi.wi_id
      );
      this.confirmdelete = false;

      this.$emit("formWineAction", { action: "deleted" });
    },
  },
};
</script>

<style lang="scss" scoped>
</style>
