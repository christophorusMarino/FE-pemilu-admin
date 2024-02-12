<template>
  <div>
    <v-card flat>
      <v-card-title class="justify-center"> - SUARA KECAMATAN - </v-card-title>
      <v-card-text>
        <v-row no-gutters>
          <v-col cols="12" class="col-lg-3 col-md-3 col-sm-12">
            <v-autocomplete
              label="pilih kota/kabupaten"
              outlined
              dense
              hide-details
              :items="dataKota"
              item-text="nama"
              item-value="uid"
              @change="(e) => actionKecamatan(e)"
            ></v-autocomplete>
          </v-col>
          <v-col cols="12" class="col-lg-3 col-md-3 col-sm-12">
            <v-autocomplete
              label="pilih kecamatan"
              outlined
              dense
              hide-details
              :items="dataKecamatan"
              item-text="nama"
              item-value="uid"
              @change="(e) => actionDesa(e)"
            ></v-autocomplete>
          </v-col>
          <v-col cols="12" class="col-lg-3 col-md-3 col-sm-12">
            <v-autocomplete
              label="pilih kelurahan"
              outlined
              dense
              hide-details
              :items="dataDesa"
              item-text="nama"
              item-value="uid"
              @change="(e) => actionTps(e)"
            ></v-autocomplete>
          </v-col>
          <v-col cols="12" class="col-lg-3 col-md-3 col-sm-12">
            <v-autocomplete
              label="pilih tps"
              outlined
              dense
              hide-details
              :items="dataTps"
              item-text="nama"
              item-value="uid"
            ></v-autocomplete>
          </v-col>
        </v-row>
        <v-divider class="my-3"></v-divider>
      </v-card-text>
    </v-card>
  </div>
</template>
  
  <script>
import { mapActions, mapGetters } from "vuex";
export default {
  computed: {
    ...mapGetters({
      dataKota: "wilayah/kota",
      dataKecamatan: "wilayah/kecamatan",
      dataDesa: "wilayah/desa",
      dataTps: "wilayah/tps",
    }),
  },

  methods: {
    ...mapActions({
      getDataKota: "wilayah/getKota",
      getDataKecamatan: "wilayah/getKecamatan",
      getDataDesa: "wilayah/getDesa",
      getDataTps: "wilayah/getTps",
    }),
    actionKecamatan(e) {
      let query = {
        id: e,
      };
      this.getDataKecamatan(query);
    },
    actionDesa(e) {
      let query = {
        id: e,
      };
      this.getDataDesa(query);
    },
    actionTps(e) {
      let query = {
        id: e,
      };
      this.getDataTps(query);
    },
  },

  created() {
    this.getDataKota();
  },
};
</script>