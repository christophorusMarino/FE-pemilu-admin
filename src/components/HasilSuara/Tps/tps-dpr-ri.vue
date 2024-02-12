<template>
  <div>
    {{ tps }}
    <v-card flat v-if="tps">
      <template v-if="dataDapilDprRi">
        <v-card-text class="pa-0 py-2">
          <v-row no-gutters align="center" justify="center">
            <v-col cols="12">
              <v-card-title
                class="justify-center py-0 text-subtitle-1 black--text font-weight-bold"
              >
                {{ tps?.kabupaten }} / {{ tps?.kecamatan }} /
                {{ tps?.kelurahan }} / TPS {{ tps?.nomor }}
              </v-card-title>
            </v-col>
            <v-col cols="12">
              <v-card-title
                class="justify-center py-0 text-subtitle-1 black--text font-weight-bold"
              >
                - DPR RI / {{ dataDapilDprRi?.dapil?.nama?.toUpperCase() }} -
              </v-card-title>
            </v-col>
          </v-row>
        </v-card-text>
        <v-divider></v-divider>
        <v-card-text
          class="py-0"
          style="background-color: yellow; border-radius: 10px"
        >
          <v-form ref="form" v-model="valid">
            <v-row class="my-1"> </v-row>
          </v-form>
        </v-card-text>
      </template>
    </v-card>
  </div>
</template>

<script>
import { mapActions, mapGetters } from "vuex";

export default {
  props: ["tps"],

  data: () => ({
    document,
    valid: false,
    rules: {
      required: (v) => !!v || "!! Tidak boleh kosong !!",
      max: (v) => v <= 350 || "!! Maksimal 350 suara !!",
    },
    publicPathPartai: "img/partai/",
    publicPathCaleg: "img/caleg/",
    flag: 11,
    dataSuara: [],
    foto: [],
    temp: [],
    alertSnackbar: false,
    textSnackbar: "",
    colorSnackbar: "",
    state: "SAVE",
    btnLoading: false,
  }),

  computed: {
    ...mapGetters({
      dataDapilDprRi: "dapil/dapilDprRi",
    }),
  },

  methods: {
    ...mapActions({
      getDataDapilDprRi: "dapil/getDapilDprRi",
    }),
    async getDapil() {
      let queryDapil = {
        wilayah: this.tps?.uid_wilayah?.toString().substring(0, 4),
      };
      await this.getDataDapilDprRi(queryDapil);
    },
  },

  mounted() {
    if (this.tps) {
      console.log("GET DPR RI");
      this.getDapil();
    }
  },
};
</script>