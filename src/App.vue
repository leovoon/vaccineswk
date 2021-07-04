<template>
  <h2>Vaccination 💉 in Sarawak</h2>
  <span class="souce"
    >source from
    <a href="https://github.com/CITF-Malaysia/citf-public/"
      >citf-malaysia</a
    ></span
  >

  <div class="container">
    <div class="chart">
      <p class="chart-title">Total dose daily</p>

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
              dose1_daily: { label: 'Dose 1', color: '#b693fe' },
              dose2_daily: { label: 'Dose 2', color: '#8c82fc' },
              total_daily: { label: 'Total Dose per day', color: '#27296d' },
              dose1_cumul: { label: 'Total Dose 1', color: '#b693fe' },
              dose2_cumul: { label: 'Total Dose 2', color: '#8c82fc' },
              total_cumul: { label: 'Overall Total', color: '#27296d' },
            }"
          />
        </template>
      </Chart>
    </div>

    <div class="summary">
      <h4>Most recent updates</h4>
      <p>
        Date: <span style="color: red">{{ latestUpdateData.date }}</span>
      </p>
      <p>
        Dose 1:
        <span style="color: #b693fe">{{ latestUpdateData.dose1_daily }}</span>
      </p>
      <p>
        Dose 2:
        <span style="color: #8c82fc">{{ latestUpdateData.dose2_daily }}</span>
      </p>
      <p>
        Total Dose per day:
        <span style="color: #27296d">{{ latestUpdateData.total_daily }}</span>
      </p>
      <p>
        Total Dose 1:
        <span style="color: #b693fe">{{ latestUpdateData.dose1_cumul }}</span>
      </p>
      <p>
        Total Dose 2:
        <span style="color: #8c82fc">{{ latestUpdateData.dose2_cumul }}</span>
      </p>
      <p>
        Overall Total:
        <span style="color: #27296d">{{ latestUpdateData.total_cumul }}</span>
      </p>
      <p style="color: lightslategray; font-size: smaller">
        *update at 2359 daily
      </p>
    </div>
  </div>

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
    const latestUpdateData = ref(null)
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
          latestUpdateData.value = swk.value.slice().pop()
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
      latestUpdateData,
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

.container {
  display: flex;
  justify-content: space-around;
  align-items: center;
}

.summary {
  border: 3px solid rgba(135, 206, 250, 0.5);
  padding: 0.5rem 3rem;
  border-radius: 10px;
}

.souce {
  margin-bottom: 30px;
}

layer a {
  text-decoration: none;
}
svg {
  overflow: visible;
}

@media screen and (max-width: 320px) {
  .container {
    flex-direction: column;
  }
}
</style>
