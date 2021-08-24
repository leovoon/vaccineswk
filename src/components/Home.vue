<template>
  <div class="selectByState">
    <Multiselect
      class="selectStyle"
      v-model="selectedValue"
      :options="options"
      :searchable="true"
      :close-on-select="false"
      :show-labels="false"
      :hide-selected="false"
      open-direction="bottom"
      placeholder="Pick a state"
      @select="updateGraph(selectedValue)"
    />
  </div>
  <div class="container">
    <span class="chart-title">Total dose daily</span>

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
            'daily',
            'daily_full',
            'daily_partial',            
            'cumul',
            'cumul_partial',
            'cumul_full',
            'pending',
            
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
            daily: { label: 'Daily', color: '#ff69b4' },
            daily_partial: { label: 'Daily Partial', color: '#27296d' },
            daily_full: { label: 'Daily Full', color: '#27296d' },
            cumul: { label: 'Cumulative', color: '#27296d' },
            cumul_partial: { label: 'Cumulative Partial', color: '#27296d' },
            cumul_full: { label: 'Overall Total', color: '#27296d' },
          }"
        />
      </template>
    </Chart>




    <div v-if="latestUpdateData" class="summary">
      <h3 style="color: purple">{{ latestUpdateData.state }}</h3>
      <h4>Most recent updates</h4>
      <p>
        Date: <span style="color: red">{{ latestUpdateData.date }}</span>
      </p>
      <p>
        Daily:
        <span style="color: #b693fe">{{ latestUpdateData.daily  }}</span>
      </p>
      <p>
        Daily Full:
        <span style="color: #8c82fc">{{ latestUpdateData.daily_full }}</span>
      </p>
      <p>
        Daily Partial:
        <span style="color: #27296d">{{ latestUpdateData.daily_partial }}</span>
      </p>
      <p>
        Cumulative:
        <span style="color: #b693fe">{{ latestUpdateData.cumul }}</span>
      </p>
      <p>
        Cumulative Full:
        <span style="color: #8c82fc">{{ latestUpdateData.cumul_full }}</span>
      </p>
      <p>
        Cumulative Partial:
        <span style="color: #27296d">{{ latestUpdateData.cumul_partial }}</span>
      </p>
      <p>
        AstraZeneca 1:
        <span style="color: #27296d">{{ latestUpdateData.astra1 }}</span>
      </p>
      <p>
        AstraZeneca 2:
        <span style="color: #27296d">{{ latestUpdateData.astra2 }}</span>
      </p>
      <p>
        Pfizer1:
        <span style="color: #27296d">{{ latestUpdateData.pfizer1 }}</span>
      </p>
      <p>
        Pfizer2:
        <span style="color: #27296d">{{ latestUpdateData.pfizer2 }}</span>
      </p>
      <p>
        Sinovac1:
        <span style="color: #27296d">{{ latestUpdateData.sinovac1 }}</span>
      </p>
      <p>
        Sinovac2:
                <span style="color: #27296d">{{ latestUpdateData.sinovac2 }}</span>
      </p>
      <p style="color: lightslategray; font-size: smaller">
        *update at 2359 daily
      </p>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, onUpdated } from 'vue'
import { Chart, Grid, Line, Tooltip } from 'vue3-charts'
import Multiselect from '@vueform/multiselect'

const url =
  'https://raw.githubusercontent.com/CITF-Malaysia/citf-public/main/vaccination/vax_state.csv'

export default {
  name: 'Home',
  components: { Chart, Grid, Line, Tooltip, Multiselect },
  setup() {
    const selectedValue = ref('Sarawak')
    let updateGraph = ref(null)
    const options = ref([
      'Johor',
      'Kedah',
      'Kelantan',
      'Melaka',
      'Negeri Sembilan',
      'Pahang',
      'Perak',
      'Perlis',
      'Pulau Pinang',
      'Sabah',
      'Sarawak',
      'Selangor',
      'Terengganu',
      'W.P. Kuala Lumpur',
      'W.P. Labuan',
      'W.P. Putrajaya',
    ])
    const size = ref({ width: 600, height: 420 })
    const swk = ref([])
    const latestUpdateData = ref(null)
    const direction = ref('horizontal')
    let today = new Date()
    today.setDate(today.getDate() - 1)
    let dd = String(today.getDate()).padStart(2, '0')
    let mm = String(today.getMonth() + 1).padStart(2, '0') //January is 0!
    let yyyy = today.getFullYear()
    today = yyyy + '-' + mm + '-' + dd

    const margin = ref({
      left: 25,
      top: 25,
      right: 30,
      bottom: 25,
    })
    const lineStyle = ref({
      strokeWidth: 2,
      stroke: '#4CC9F0',
      strokeDasharray: 3,
    })

    const dotStyle = ref({
      strokeWidth: 1,
      stroke: '#5e63b6',
      strokeDasharray: 0,
    })
    const axis = ref({
      primary: {
        type: 'band',
        format: (val) => {
          if (val === today) {
            return '🌟'
          } else {
            return ''
          }

          return val
        },
      },
      secondary: {
        domain: ['dataMin ', 'dataMax + 100000'],
        type: 'linear',
        ticks: 8,
      },
    })

    const filteredData = (bigData) => {
      return bigData.filter((i) => selectedValue.value.includes(i.state))
    }

    onMounted(() => {
      Papa.parse(url, {
        download: true,
        header: true,
        complete: function (result) {
          if (result !== null) {
            swk.value = filteredData(result.data)
            latestUpdateData.value = swk.value.slice().pop()
            console.log(latestUpdateData.value)
          } else {
            console.log('No data from api')
          }
        },
      })
    })

    onUpdated(
      (updateGraph = (selectedValue) => {
        Papa.parse(url, {
          download: true,
          header: true,
          complete: function (result) {
            if (result !== null) {
              swk.value = filteredData(result.data)
              latestUpdateData.value = swk.value.slice().pop()
            } else {
              console.log('No data from api')
            }
          },
        })
      }),
    )

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
      selectedValue,
      options,
    }
  },
}
</script>
<style src="@vueform/multiselect/themes/default.css"></style>

<style>
.selectStyle {
  width: 240px;
}

.container {

  position: relative;
}
.chart-title {
  position: absolute;
  left: 20px;
  top: 0;
  font-size: small;
  z-index: 1;
}

.summary {
  border: 3px solid rgba(135, 206, 250, 0.5);
  padding: 0.5rem 3rem;
  border-radius: 10px;
  max-width: 90vw;
  margin: 0 auto;
}

layer a {
  text-decoration: none;
}
svg {
  overflow: visible;
}

@media screen and (max-width: 420px) {


  .chart {
    width: 100vw;
    overflow-x: scroll;
  }

  .summary{
    z-index: 0;
  }
}
</style>
