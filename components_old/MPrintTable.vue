<template>
  <div>
    <m-list-simple
      ref="userslist"
      :dynamic="true"
      :items="users"
      item-value="us_id"
      :item-height="30"
      :total="users_total"
    >
      <template v-slot:ths="{}">
        <th v-for="col in cols" :key="col.key">
          <span>{{ col.text }}</span>
        </th>
      </template>
      <template v-slot:tds="{ item }">
        <td v-for="col in cols" :key="col.key">
          {{ item[col.key] }}
        </td>
      </template>
    </m-list-simple>
  </div>
</template>
<script>
export default {
  name: "user-list",
  components: {},
  data() {
    return {
      users_limit: 50,
      users_total: 0,
      users: [],
      cols: []
    };
  },
  async mounted() {
    await this.loadUsers();
  },
  watch: {},
  methods: {
    async loadUsers() {
      // console.log("this.usersType", this.usersType);
      let query = {
        table: this.$route.params.table,
        type: "pdf"
      };
      let response = await this.$axios.get(
        this.$config.server_url + "/backoffice/1.0/exporttable",
        { params: query }
      );
      if (!response || !response.data) return;
      this.users = response.data.data;
      this.cols = this.$store.state["tablePrint_" + this.$route.params.table];

      //this.users_total = response.data.meta.total;
      this.users_total = this.users.length;
      setTimeout(() => {
        window.print();
        window.close();
      }, 500);
    }
  }
};
</script>
<style lang="scss" scoped></style>
