<template>
  <h2>Vaccination in Sarawak</h2>
  <span
    >source from
    <a href="https://github.com/CITF-Malaysia/citf-public/"
      >citf-malaysia</a
    ></span
  >
  <p>Total dose daily</p>

  <Chart
    v-if="swk"
    :size="size"
    :data="swk"
    :margin="margin"
    :direction="direction"
    :axis="axis"
  >
    <template #layers>
      <Grid strokeDasharray="2,2" />
      <Line
        :dataKeys="[
          'date',
          'dose1_daily',
          'dose2_daily',
          'total_daily',
          'dose1_cumul',
          'dose2_cumul',
          'total_cumul',
        ]"
        :lineStyle="lineStyle"
        :dotStyle="dotStyle"
      />
    </template>

    <template #widgets>
      <Tooltip
        borderColor="#48CAE4"
        :config="{
          date: { label: 'Date', color: 'red' },
          state: { hide: true },
          dose1_daily: { label: 'Dose 1 Today', color: '#b693fe' },
          dose2_daily: { label: 'Dose 2 Today', color: '#8c82fc' },
          total_daily: { label: 'Total Dose Today', color: '#27296d' },
          dose1_cumul: { label: 'Total Dose 1', color: '#b693fe' },
          dose2_cumul: { label: 'Total Dose 2', color: '#8c82fc' },
          total_cumul: { label: 'Total Overall Dose', color: '#27296d' },
        }"
      />
    </template>
  </Chart>

  <p style="font-size: small">Made with ❤️ by leovoon</p>
</template>

<script>
import { ref, onMounted } from "vue"

import { Chart, Grid, Line, Tooltip } from "vue3-charts"

const url =
  "https://raw.githubusercontent.com/CITF-Malaysia/citf-public/main/vaccination/vax_state.csv"

export default {
  components: { Chart, Grid, Line, Tooltip },
  setup() {
    const size = ref({ width: 500, height: 420 })

    const swk = ref(null)
    const direction = ref("horizontal")
    let filterString = ref(["Sarawak"])
    let today = new Date()
    today.setDate(today.getDate() - 1)
    let dd = String(today.getDate()).padStart(2, "0")
    let mm = String(today.getMonth() + 1).padStart(2, "0") //January is 0!
    let yyyy = today.getFullYear()
    today = yyyy + "-" + mm + "-" + dd
    const margin = ref({
      left: 20,
      top: 0,
      right: 60,
      bottom: 20,
    })
    const lineStyle = ref({
      strokeWidth: 2,
      stroke: "#4CC9F0",
      strokeDasharray: 3,
    })

    const dotStyle = ref({
      strokeWidth: 1,
      stroke: "#5e63b6",
      strokeDasharray: 0,
    })
    console.info(today)
    const axis = ref({
      primary: {
        type: "band",
        format: (val) => {
          if (val === today) {
            return "Last Updated 😄"
          } else {
            return ""
          }

          return val
        },
      },
      secondary: {
        domain: ["dataMin ", "dataMax + 100000"],
        type: "linear",
        ticks: 8,
      },
    })

    const filteredData = (bigData) => {
      return bigData.filter((i) => filterString.value.includes(i.state))
    }

    onMounted(() => {
      Papa.parse(url, {
        download: true,
        header: true,
        complete: function (result) {
          swk.value = filteredData(result.data)
        },
      })
    })

    return {
      swk,
      direction,
      margin,
      axis,
      Tooltip,
      lineStyle,
      dotStyle,
      size,
    }
  },
}
</script>

<style>
#app {
  display: grid;
  align-items: center;
  justify-items: center;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 30px;
}

span {
  margin-bottom: 50px;
}

layer a {
  text-decoration: none;
}
svg {
  overflow: visible;
}
</style>
