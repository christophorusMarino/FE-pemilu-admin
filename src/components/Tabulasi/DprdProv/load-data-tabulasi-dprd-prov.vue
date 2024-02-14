<template>
  <div>
    <v-card outlined>
      <v-app-bar elevate-on-scroll scroll-target="#scroll-dpr-ri">
        <v-tabs
          centered
          background-color="teal lighten-4"
          v-model="currentTabDprdProv"
        >
          <v-tab href="#TPS_DPRD_PROV"> PLANO TPS </v-tab>
          <v-tab href="#KECAMATAN_DPRD_PROV"> PLANO KECAMATAN </v-tab>
        </v-tabs>
      </v-app-bar>
      <v-sheet id="scroll-dpr-ri" class="overflow-y-auto" max-height="75vh">
        <v-container fluid style="height: 100vh">
          <v-tabs-items v-model="currentTabDprdProv">
            <v-tab-item value="TPS_DPRD_PROV">
              <tabulasi-tps-dprd-prov :layer="layer" :jenis="currentTabDprdProv" />
            </v-tab-item>
            <v-tab-item value="KECAMATAN_DPRD_PROV"> </v-tab-item>
          </v-tabs-items>
        </v-container>
      </v-sheet>
    </v-card>
  </div>
</template>
  
  <script>
import TabulasiTpsDprdProv from "../DprdProv/tabulasi-tps-dprd-prov.vue";

export default {
  components: { TabulasiTpsDprdProv },
  props: ["layer"],

  data: () => ({
    currentTabDprdProv: "TPS_DPRD_PROV",
  }),

  watch: {
    layer() {
      if (this.layer === "DPRD_PROV") {
        this.currentTabDprdProv = "TPS_DPRD_PROV";
      }
    },
  },

  mounted() {
    if (this.layer === "DPRD_PROV") {
      this.currentTabDprdProv = "TPS_DPRD_PROV";
    }
  },
};
</script>