<template>
  <div class="ma-5">
    <v-row no-gutters v-if="!loading">
      <v-col cols="12">
        <v-card-title class="py-0 justify-center">
          <v-row no-gutters>
            <v-col cols="4"></v-col>
            <v-col cols="3">
              <v-autocomplete
                v-model="kabupaten"
                :items="listKota"
                item-text="nama"
                item-value="nama"
                outlined
                dense
                @change="tabulasiDprdKotaTps"
              ></v-autocomplete>
            </v-col>
            <v-col cols="1">
              <v-icon x-large @click="tabulasiDprdKotaTps"> mdi-refresh-circle </v-icon>
            </v-col>
            <v-col cols="4"></v-col>
          </v-row>
        </v-card-title>
      </v-col>
      <v-col cols="6" v-for="(data, idx) in dataHasilDprdKotaTps" :key="idx">
        <v-card color="green" class="ma-2">
          <v-row no-gutters>
            <v-col cols="12">
              <v-card-title
                class="pt-1 py-0 justify-center font-weight-bold white--text text-subtitle-1"
              >
                HASIL PERHITUNGAN SUARA
              </v-card-title>
              <v-card-title
                class="py-0 justify-center font-weight-bold white--text text-subtitle-1"
              >
                - {{ data.jenis }} / {{ data.nama }} -
              </v-card-title>
              <v-card-title
                class="py-0 justify-center font-weight-bold white--text text-caption"
              >
                Jumlah Kursi : {{ data.jumlah_kursi }}
              </v-card-title>
              <v-card-title
                class="py-0 justify-center font-weight-bold white--text text-caption"
              >
                Suara Masuk : {{ fromTpsDprdKota[idx]?.tps_input_suara }} /
                {{ fromTpsDprdKota[idx]?.jumlah_tps }} TPS
              </v-card-title>
            </v-col>
          </v-row>
          <v-divider class="my-1"></v-divider>
          <v-card-text class="px-0 mx-0">
            <v-row no-gutters>
              <v-col cols="12">
                <v-card-title
                  class="py-0 justify-center font-weight-bold white--text text-subtitle-2"
                >
                  - PEROLEHAN SUARA PARTAI -
                </v-card-title>
                <v-card-text class="ma-0 px-2">
                  <Bar
                    :chart-options="chartOptions"
                    :data="resultPartaiDprdKotaTps[idx].result"
                    :chart-id="chartId"
                    :dataset-id-key="datasetIdKey"
                    :width="width"
                    :height="height"
                  />
                </v-card-text>
              </v-col>
              <v-col cols="12">
                <v-card-title
                  class="py-0 justify-center font-weight-bold white--text text-subtitle-2"
                >
                  - PEROLEHAN SUARA CALEG -
                </v-card-title>
                <v-card-text class="ma-0 px-2">
                  <Bar
                    :chart-options="chartOptions"
                    :data="resultCalegDprdKotaTps[idx].result"
                    :chart-id="chartId"
                    :dataset-id-key="datasetIdKey"
                    :width="width"
                    :height="height"
                  />
                </v-card-text>
              </v-col>
              <v-col cols="12">
                <v-card-title
                  class="py-0 justify-center font-weight-bold white--text text-subtitle-2"
                >
                  - PEROLEHAN KURSI PARPOL -
                </v-card-title>
                <v-card-text class="ma-0 px-2">
                  <v-row no-gutters>
                    <v-col
                      cols="3"
                      v-for="(val, key, index) in dataHasilDprdKotaTps[idx]
                        .kursi"
                      :key="index"
                    >
                      <v-card color="teal darken-2" outlined class="ma-1">
                        <v-card-title
                          class="justify-center white--text font-weight-bold black--text text-subtitle-2"
                        >
                          {{ key.toUpperCase() }}
                        </v-card-title>
                        <v-divider></v-divider>
                        <v-card-title
                          class="justify-center white--text font-weight-bold black--text text-caption"
                        >
                          Kursi: {{ val }}
                        </v-card-title>
                      </v-card>
                    </v-col>
                  </v-row>
                </v-card-text>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>

    <v-row no-gutters v-else>
      <v-col cols="6">
        <v-card color="green lighten-4" class="ma-2">
          <v-skeleton-loader type="image"></v-skeleton-loader>
        </v-card>
      </v-col>
      <v-col cols="6">
        <v-card color="green lighten-4" class="ma-2">
          <v-skeleton-loader type="image"></v-skeleton-loader>
        </v-card>
      </v-col>
    </v-row>
    <v-snackbar v-model="alertSnackbar" timeout="2000" :color="colorSnackbar">
      <strong>{{ textSnackbar }}</strong>
    </v-snackbar>
  </div>
</template>

<script>
import { mapActions, mapGetters } from "vuex";
import { Bar } from "vue-chartjs";
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from "chart.js";
ChartJS.register(
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
);
import ChartDataLabels from "chartjs-plugin-datalabels";
ChartJS.register(ChartDataLabels);

export default {
  components: { Bar },
  props: {
    layer: String,
    jenis: String,
    chartId: {
      type: String,
      default: "bar-chart",
    },
    datasetIdKey: {
      type: String,
      default: "label",
    },
    width: {
      type: Number,
      default: 400,
    },
    height: {
      type: Number,
      default: 300,
    },
  },

  data: () => ({
    loading: false,
    alertSnackbar: false,
    textSnackbar: "",
    colorSnackbar: "",
    kabupaten: null,
    dataHasilDprdKotaTps: [],
    dataHasilCalegDprdKotaTps: [],
    chartOptions: {
      responsive: true,
      maintainAspectRatio: false,
    },
    partai: [
      "PKB",
      "Gerindra",
      "PDI P",
      "Golkar",
      "NasDem",
      "Buruh",
      "Gelora",
      "PKS",
      "PKN",
      "Hanura",
      "Garuda",
      "PAN",
      "PBB",
      "Demokrat",
      "PSI",
      "Perindo",
      "PPP",
      "Ummat",
    ],
    bcPartai: "rgba(0, 137, 123, 0.7)",
    resultPartaiDprdKotaTps: [],
    resultCalegDprdKotaTps: [],
    fromTpsDprdKota: [],
  }),

  computed: {
    ...mapGetters({
      listKota: "wilayah/kota",
    }),
  },

  watch: {
    layer() {
      if (this.layer === "DPRD_PROV" && this.jenis === "TPS_DPRD_KOTA") {
        this.kabupaten = null;
        this.dataHasilDprdKotaTps = [];
        this.dataHasilCalegDprdKotaTps = [];
      }
    },
    jenis() {
      if (this.layer === "DPRD_KOTA" && this.jenis === "TPS_DPRD_KOTA") {
        this.kabupaten = null;
        this.dataHasilDprdKotaTps = [];
        this.dataHasilCalegDprdKotaTps = [];
      }
    },
  },

  methods: {
    ...mapActions({
      getDataKota: "wilayah/getKota",
      getTabulasi: "tabulasi/getDataTabulasi",
      getSuara: "tabulasi/getHasilTps",
    }),
    async tabulasiDprdKotaTps() {
      this.resultPartaiDprdKotaTps = [];
      this.loading = true;
      let layer = "DPRD KAB/KOTA";
      let jenis = "tps";
      let param = {
        layer: layer,
        jenis: jenis,
        kabupaten: this.kabupaten,
      };
      if (this.kabupaten) {
        await this.getTabulasi(param)
          .then((response) => {
            this.dataHasilDprdKotaTps = response.hasilFinal;
            this.setDataHasilPartai(response.hasilFinal);
            this.setHasilCaleg(response.hasilFinal);
          })
          .catch((e) => {
            this.textSnackbar = "FETCH DATA DAPIL ERROR";
            this.colorSnackbar = "error";
            this.alertSnackbar = true;
          });
        let payload = {
          dapil: "dprdkabkota",
          param: {
            wilayah: this.kabupaten,
          },
        };
        this.getSuara(payload).then((response) => {
          this.fromTpsDprdKota = response.data;
        });
      }
    },
    setDataHasilPartai(data) {
      console.log(data);
      data.forEach((el) => {
        let rest = {
          result: {
            labels: this.partai,
            datasets: [
              {
                label: "perolehan suara",
                backgroundColor: this.bcPartai,
                color: "black",
                data: [
                  el.suaraPartai.pkb,
                  el.suaraPartai.gerindra,
                  el.suaraPartai.PDIP,
                  el.suaraPartai.golkar,
                  el.suaraPartai.nasdem,
                  el.suaraPartai.PBuruh,
                  el.suaraPartai.gelora,
                  el.suaraPartai.pks,
                  el.suaraPartai.pkn,
                  el.suaraPartai.hanura,
                  el.suaraPartai.garuda,
                  el.suaraPartai.pan,
                  el.suaraPartai.pbb,
                  el.suaraPartai.demokrat,
                  el.suaraPartai.psi,
                  el.suaraPartai.perindo,
                  el.suaraPartai.ppp,
                  el.suaraPartai.ummat,
                ],
                datalabels: {
                  color: "white",
                  align: "end",
                  anchor: "center",
                },
              },
            ],
          },
        };
        this.resultPartaiDprdKotaTps.push(rest);
      });
      this.loading = false;
    },
    setHasilCaleg(data) {
      data.forEach((el) => {
        let label = [];
        let js = [];
        el.CalegPan.forEach((e) => {
          label.push(e.nama);
        });
        el.CalegPan.forEach((e) => {
          js.push(e.jumlah_suara);
        });
        let rest = {
          result: {
            labels: label,
            datasets: [
              {
                label: "perolehan suara",
                backgroundColor: "rgba(0, 121, 107, 0.4)",
                data: js,
                datalabels: {
                  color: "black",
                  backgroundColor: "whitesmoke",
                  align: "end",
                  anchor: "center",
                },
              },
            ],
          },
        };
        this.resultCalegDprdKotaTps.push(rest);
      });
      this.loading = false;
    },
  },

  mounted() {
    this.getDataKota();
    if (this.layer === "DPRD_KOTA" && this.jenis === "TPS_DPRD_KOTA") {
      this.kabupaten = null;
      this.dataHasilDprdKotaTps = [];
      this.dataHasilCalegDprdKotaTps = [];
    }
  },
};
</script>