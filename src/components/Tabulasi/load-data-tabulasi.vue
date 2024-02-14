<template>
  <div>
    <v-card flat>
      <v-tabs grow v-model="currentTab">
        <v-tab href="#DPR_RI"> DPR RI </v-tab>
        <v-tab href="#DPRD_PROV"> DPRD PROVINSI </v-tab>
        <v-tab href="#DPRD_KOTA"> DPRD KABUPATEN/KOTA </v-tab>

        <v-tabs-items v-model="currentTab">
          <v-tab-item value="DPR_RI">
            <load-data-tabulasi-dpr-ri :layer="currentTab" />
          </v-tab-item>
          <v-tab-item value="DPRD_PROV"> </v-tab-item>
          <v-tab-item value="DPRD_KOTA"> </v-tab-item>
        </v-tabs-items>
      </v-tabs>
    </v-card>
  </div>
</template>

<script>
import loadDataTabulasiDprRi from "./DprRi/load-data-tabulasi-dpr-ri.vue";
export default {
  components: { loadDataTabulasiDprRi },
  data: () => ({
    currentTab: "DPR_RI",
  }),
};
</script>