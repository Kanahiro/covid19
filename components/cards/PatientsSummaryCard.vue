<template>
  <v-col class="DataCard" cols="12" md="6">
    <time-bar-chart
      :chart-data="patientsGraph"
      :date="convertToDateFromData(patients_summary.last_update)"
      :default-data-kind="'cumulative'"
      :loaded="patients_summary.loaded"
      :source-from="$t('北海道 オープンデータポータル')"
      :title="$t('陽性患者数')"
      :unit="$t('人')"
      title-id="patients-summary"
      source-link="https://www.harp.lg.jp/opendata/dataset/1369.html"
    />
  </v-col>
</template>

<script>
import TimeBarChart from '@/components/TimeBarChart.vue'
import formatPatientsSummaryGraph from '@/utils/formatPatientsSummaryGraph'
import convertToDateFromData from '@/utils/convertToDateFromData'

export default {
  name: 'PatientsSummaryCard',
  components: {
    TimeBarChart
  },
  data() {
    return {
      patients_summary: {
        loaded: false,
        last_update: ''
      },
      patientsGraph: [],
      convertToDateFromData
    }
  },
  created() {
    this.getPatientsSummaryGraphFromAPI()
  },
  methods: {
    // 治療終了者数グラフ
    async getPatientsSummaryGraphFromAPI() {
      await this.$axios
        .$get('/patients_summary.json')
        .then(response => {
          this.patientsGraph = formatPatientsSummaryGraph(response.data)
          this.patients_summary.last_update = response.last_update
          this.patients_summary.loaded = true
        })
        .catch(_ => {
          this.$emit('failed', '陽性患者数データ ')
        })
    }
  }
}
</script>
