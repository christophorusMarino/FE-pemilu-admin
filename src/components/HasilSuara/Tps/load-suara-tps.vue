<template>
  <div>
    <v-card flat>
      <v-card-title class="justify-center"> - SUARA TPS - </v-card-title>
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
              @change="(e) => populateDapil(e)"
            >
              <template v-slot:item="{ item }"> TPS {{ item.nomor }} </template>
              <template v-slot:selection="data">
                TPS {{ data.item.nomor }}
              </template>
            </v-autocomplete>
          </v-col>
        </v-row>
        <v-divider class="my-3"></v-divider>
        <v-tabs grow v-model="currentItems">
          <v-tab href="#DPR RI"> DPR RI </v-tab>
          <v-tab href="#DPRD PROV"> DPRD PROVINSI </v-tab>
          <v-tab href="#DPRD KOTA"> DPRD KABUPATEN/KOTA </v-tab>

          <v-tabs-items v-model="currentItems">
            <v-tab-item value="DPR RI">
              <tps-dpr-ri :tps="tps" />
            </v-tab-item>
            <v-tab-item value="DPRD PROV"> </v-tab-item>
            <v-tab-item value="DPRD KOTA"> </v-tab-item>
          </v-tabs-items>
        </v-tabs>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
import { mapActions, mapGetters } from "vuex";
import tpsDprRi from "./tps-dpr-ri.vue";

export default {
  components: { tpsDprRi },
  data: () => ({
    currentItems: "DPR RI",
    tps: null,
  }),
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
        uid_wilayah: e,
        limit: 50,
        offset: 0,
      };
      this.getDataTps(query);
    },
    populateDapil(e) {
      this.tps = e;
    },
  },

  created() {
    this.getDataKota();
  },
};
</script>