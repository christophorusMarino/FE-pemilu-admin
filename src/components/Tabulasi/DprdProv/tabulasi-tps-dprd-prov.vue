<template>
  <div class="ma-5">
    <v-row no-gutters v-if="!loading">
      <v-col cols="12">
        <v-card-title class="py-0 justify-center">
          <v-icon x-large @click="() => tabulasiDprdProvTps()">
            mdi-refresh-circle
          </v-icon>
        </v-card-title>
      </v-col>
      <v-col cols="6" v-for="(data, idx) in dataHasilDprdProvTps" :key="idx">
        <v-card color="blue" class="ma-2">
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
                Suara Masuk : {{ fromTpsDprdProv[idx]?.tps_input_suara }} /
                {{ fromTpsDprdProv[idx]?.jumlah_tps }} TPS
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
                    :data="resultPartaiDprdProvTps[idx].result"
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
                    :data="resultCalegDprdProvTps[idx].result"
                    :chart-id="chartId"
                    :dataset-id-key="datasetIdKey"
                    :width="width"
                    :height="height"
                  />
                </v-card-text>
              </v-col>
              <v-col cols="12">
                <v-card-title
                  class="py-0 justify-center font-weight-bold black--text text-subtitle-2"
                >
                  - PEROLEHAN KURSI PARPOL -
                </v-card-title>
                <v-card-text class="ma-0 px-2">
                  <v-row no-gutters>
                    <v-col
                      cols="3"
                      v-for="(val, key, index) in dataHasilDprdProvTps[idx]
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
        <v-card color="blue lighten-4" class="ma-2">
          <v-skeleton-loader type="image"></v-skeleton-loader>
        </v-card>
      </v-col>
      <v-col cols="6">
        <v-card color="blue lighten-4" class="ma-2">
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
import { mapActions } from "vuex";
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
    dataHasilDprdProvTps: [],
    dataHasilCalegDprdProvTps: [],
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
    resultPartaiDprdProvTps: [],
    resultCalegDprdProvTps: [],
    fromTpsDprdProv: [],
  }),

  watch: {
    layer() {
      if (this.layer === "DPRD_PROV" && this.jenis === "TPS_DPRD_PROV") {
        this.tabulasiDprdProvTps();
      }
    },
    jenis() {
      if (this.layer === "DPRD_PROV" && this.jenis === "TPS_DPRD_PROV") {
        this.tabulasiDprdProvTps();
      }
    },
  },

  methods: {
    ...mapActions({
      getTabulasi: "tabulasi/getDataTabulasi",
      getSuara: "tabulasi/getHasilTps",
    }),
    async tabulasiDprdProvTps() {
      this.resultPartaiDprdProvTps = [];
      this.loading = true;
      let layer = "DPRD PROVINSI RIAU";
      let jenis = "tps";
      let param = {
        layer: layer,
        jenis: jenis,
      };
      await this.getTabulasi(param)
        .then((response) => {
          this.dataHasilDprdProvTps = response.hasilFinal;
          this.setDataHasilPartai(response.hasilFinal);
          this.setHasilCaleg(response.hasilFinal);
        })
        .catch((e) => {
          this.textSnackbar = "FETCH DATA DAPIL ERROR";
          this.colorSnackbar = "error";
          this.alertSnackbar = true;
        });
      let payload = {
        dapil: "dprdprov",
        param: {},
      };
      this.getSuara(payload).then((response) => {
        this.fromTpsDprdProv = response.data;
      });
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
        this.resultPartaiDprdProvTps.push(rest);
      });
      this.loading = false;
      console.log(this.resultPartaiDprdProvTps);
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
        this.resultCalegDprdProvTps.push(rest);
      });
      this.loading = false;
      console.log(this.resultCalegDprdProvTps);
    },
  },

  mounted() {
    if (this.layer === "DPRD_PROV" && this.jenis === "TPS_DPRD_PROV") {
      this.tabulasiDprdProvTps();
    }
  },
};
</script>