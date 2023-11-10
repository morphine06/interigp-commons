<template>
  <v-card-text>
    <div v-if="showAddButton">
      <v-btn color="primary2" @click="showInputs = !showInputs"
        >Ajouter une image</v-btn
      >
    </div>
    <div v-if="showInputs">
      <div
        class="frameDropImage"
        ref="frameDropImage"
        @drop.prevent="dropimages"
        @dragover.prevent="dragoverimages"
        @dragleave.prevent="dragleaveimages"
      >
        <h2>Déposer ici un ou des fichiers de votre bureau</h2>
      </div>
      <h2>Ou choisissez les fichiers à envoyer</h2>
      <v-file-input
        chips
        multiple
        :label="label"
        v-model="filesInput"
        accept="image/*"
        @change="handleChangeFile"
      ></v-file-input>
    </div>
    <v-layout wrap align-center>
      <draggable
        v-model="images2"
        group="people"
        @start="drag = true"
        @end="drag = false"
      >
        <div
          v-for="(row_fi, indexFi) in images2"
          :key="row_fi.fi_id"
          class="item_image"
        >
          <v-tooltip top>
            <template v-slot:activator="{ on }">
              <v-img
                :src="
                  row_fi.binary
                    ? row_fi.binary
                    : `${$config.server_url}/backoffice/1.0/images/${row_fi.fi_id}/75/75?token=${$store.state.accesstoken}`
                "
                height="75px"
                width="75px"
                :ref="`littleimg${row_fi.fi_id}`"
                v-on="on"
                :class="row_fi.active"
              >
                <div class="container-icons">
                  <v-icon
                    :class="
                      !row_fi.fi_subtype || !row_fi.fi_description
                        ? 'text-red'
                        : ''
                    "
                    dark
                    @click="updateImage(row_fi)"
                    >mdi-pencil</v-icon
                  >
                  <v-icon dark @click="deleteImage(indexFi)">mdi-delete</v-icon>
                </div>
              </v-img>
            </template>
            <span>
              Type de l'image :
              {{
                row_fi.fi_subtype
                  | formatFromArray($store.state.items_fi_subtype, "A définir")
              }}.
              <br />
              Description :
              {{ row_fi.fi_description ? row_fi.fi_description : "A définir" }}
            </span>
          </v-tooltip>
        </div>
      </draggable>
    </v-layout>
    <div v-if="showInputsDetails">
      <div class="d-flex">
        <m-form-select
          style="margin-right:20px;"
          :items="$store.state.items_fi_subtype"
          v-model="fi_subtype"
          label="Type d'image"
          label-position="top"
        ></m-form-select>
        <m-form-text
          :name="$Utils.randomstring('fi_description')"
          label="Description de l'image"
          label-position="top"
          :label-width="9"
          style="width:300px;"
          suffix="m2"
          v-model="fi_description"
        ></m-form-text>
      </div>
      <div class="d-flex">
        <v-btn
          style="margin-right:15px;"
          color="warning2"
          @click="cancelDetails"
          >Annuler</v-btn
        >
        <v-btn color="primary2" @click="saveDetails">Enregistrer</v-btn>
      </div>
    </div>
    <m-confirm-dialog
      v-model="confirmDeleteDialog"
      text="Souhaitez-vous effacer cette image ?"
      title="Confirmation"
      @canceled="confirmDeleteDialog = false"
      @confirmed="deleteImageOk()"
    ></m-confirm-dialog>
  </v-card-text>
</template>

<script>
import draggable from "vuedraggable";

export default {
  name: "mselect",
  components: { draggable },
  props: {
    label: String,
    value: Array,
    showAddButton: Boolean
  },
  data() {
    return {
      confirmDeleteDialog: false,
      images2: [],
      fi_subtype: "photo",
      fi_description: "",
      filesInput: [],
      // files: [],
      showInputs: false,
      showInputsDetails: false,
      currentRowFi: null
    };
  },
  mounted() {
    if (this.value) this.images2 = this.value;
  },
  watch: {
    value(val) {
      this.images2 = val;
    },
    images2(val) {
      this.$emit("input", val);
      // console.log("val", val);
    }
  },
  methods: {
    dragleaveimages(e) {
      this.$refs.frameDropImage.style.backgroundColor = "white";
    },
    dragoverimages(e) {
      this.$refs.frameDropImage.style.backgroundColor = "green";
    },
    dropimages(e) {
      this.$refs.frameDropImage.style.backgroundColor = "white";
      let droppedFiles = e.dataTransfer.files;
      if (!droppedFiles) return;
      [...droppedFiles].forEach(f => {
        // this.files.push(f);
        this.createThumbnail(f);
      });
      // this.$emit("inputfile", this.files, "images");
    },
    handleChangeFile(files) {
      for (let iFile = 0; iFile < files.length; iFile++) {
        const f = files[iFile];
        // vérifie si le fichier est déjà présent (à cause d'un bug de vutify qui appel l'événement 2 fois)
        // let alreadyPresent = false;
        // for (let iFile2 = 0; iFile2 < this.files.length; iFile2++) {
        //   const f2 = this.files[iFile2];
        //   if (f2.name == f.name) alreadyPresent = true;
        // }
        // if (!alreadyPresent) {
        // this.files.push(f);
        this.createThumbnail(f);
        // }
      }
      // this.filesInput = [];
      // this.$emit("inputfile", this.files, "images");
    },
    createThumbnail(f) {
      if (f.name.match(/\.(jpg|jpeg|png|gif)$/)) {
        var reader = new FileReader();
        var me = this;
        reader.onload = (function(theFile) {
          return function(e) {
            me.images2.push({
              file: f,
              binary: e.target.result
            });
          };
        })(f);
        reader.readAsDataURL(f);
      }
    },
    async saveFiles(of_id) {
      // console.log("this.images2", this.images2);

      for (let iImage = 0; iImage < this.images2.length; iImage++) {
        const row_fi = this.images2[iImage];
        if (!row_fi.file) continue;
        let formData = new FormData();
        formData.append("of_id", of_id);
        formData.append("fi_from", "images");
        formData.append("fi_sort", iImage + 1);
        formData.append("fi_subtype", row_fi.fi_subtype);
        formData.append("fi_description", row_fi.fi_description);
        formData.append("image", row_fi.file);
        await this.$axios.post(
          this.$config.server_url + "/backoffice/1.0/files",
          formData,
          {
            headers: {
              "Content-Type": "multipart/form-data"
            }
          }
        );
      }
    },
    reset() {
      // this.files = [];
      this.filesInput = [];
      this.showInputs = false;
      this.showInputsDetails = false;
    },
    updateImage(row_fi) {
      // console.log("row_fi", row_fi);
      for (let i = 0; i < this.images2.length; i++) {
        this.images2[i].active = "";
      }
      // this.$refs["littleimg" + row_fi.fi_id].styles.opacity = 0.5;
      this.currentRowFi = row_fi;
      this.currentRowFi.active = "active";
      this.showInputsDetails = true;
      this.fi_description = this.currentRowFi.fi_description;
      this.fi_subtype = this.currentRowFi.fi_subtype;
      this.$forceUpdate();
    },
    deleteImage(indexFi) {
      this.confirmDeleteDialogIndex = indexFi;
      this.confirmDeleteDialog = true;
    },
    deleteImageOk() {
      this.images2.splice(this.confirmDeleteDialogIndex, 1);
      this.confirmDeleteDialog = false;
    },
    addImages() {
      this.showInputs = !this.showInputs;
    },
    cancelDetails() {
      this.showInputsDetails = false;
      this.currentRowFi.active = "";
    },
    saveDetails() {
      this.currentRowFi.fi_description = this.fi_description;
      this.currentRowFi.fi_subtype = this.fi_subtype;
      this.fi_description = "";
      this.fi_subtype = "";
      this.showInputsDetails = false;
      this.currentRowFi.active = "";
    }
  }
};
</script>
<style lang="scss" scoped>
.active {
  box-shadow: 0px 0px 9px 0px;
  transform: scale(1.15);
  z-index: 10;
}
.frameDropImage {
  background-color: white;
  display: flex;
  align-items: center;
  margin-bottom: 15px;
  border: 1px solid black;
  border-radius: 10px;
  height: 200px;
  width: 100%;
  justify-content: center;
}
.container-icons {
  text-align: right;
  //   background-color: rgba(0, 0, 0, 0.3);
  background: linear-gradient(to bottom, rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0));
  color: white;
  padding-bottom: 15px;
}
.item_image {
  padding: 2px;
  cursor: move;
  float: left;
}
</style>
