<template>
  <div>
    <input
      class="form-control"
      type="file"
      ref="theinputfile"
      :label="label"
      :multiple="multiple"
      :accept="type === 'images' ? 'image/*' : 'file/*'"
      @change="handleChangeFile($event)"
    />
    <div wrap align-center v-if="type === 'images'">
      <div
        v-for="row_fi in offer.images"
        :key="row_fi.fi_id"
        style="padding:2px;"
      >
        <v-img
          :src="
            `${$config.server_url}/backoffice/1.0/images/${row_fi.fi_id}/75/75?token=${$store.state.accesstoken}`
          "
          height="75px"
          width="75px"
        ></v-img>
      </div>
    </div>
    <!--     <div wrap align-center v-if="type === 'files'">
      <div
        v-for="row_fi in offer.files"
        :key="row_fi.fi_id"
        style="padding:2px;"
      >
        <v-img
          :src="`${$config.server_url}/backoffice/1.0/files/${row_fi.fi_id}`"
          height="75px"
          width="75px"
        ></v-img>
      </div>
    </div> -->
  </div>
</template>

<script>
export default {
  name: "mselect",
  components: {},
  props: {
    multiple: { type: Boolean, default: false },
    label: String,
    type: String,
    offer: Object
  },
  data() {
    return {
      files: []
    };
  },
  mounted() {},
  methods: {
    handleChangeFile(e) {
      let files = e.target.files;
      if (!files.length) return;
      this.$emit("inputfile", files, this.type);
    },
    reset() {
      this.files = [];
      this.$refs.theinputfile.value = "";
    }
  }
};
</script>
<style scoped lang="scss"></style>
