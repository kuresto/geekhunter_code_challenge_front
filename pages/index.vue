<template>
  <section class="container">
    <header class="text-center">
      <div class="row">
        <div class="col-12">
          <h1>
            Abundantia <br>
            <small>GeekHunter code challenge </small>
            <h6>by Nilton Teixeira</h6>
          </h1>
        </div>
      </div>
    </header>
    <main>
      <div v-if="currencies">
        <div v-for="currency in currencies" :key="currency.code" class="row currency-container">
          <header class="col-12">
            <h3>{{ currency.name }}</h3>
          </header>
          <div class="col-12 col-sm-9">
            <lineChart :data="quotations[currency.code]" :options="{ maintainAspectRatio: false }" />
          </div>
          <div class="col-12 col-md-3">
            <div class="row">
              <div class="col-12">
                <div class="card text-white bg-info mb-3 shadow rounded">
                  <div class="card-body">
                    <h6 class="card-title text-center">
                      Today
                    </h6>
                    <h4>
                      <p class="card-text text-center">
                        <strong>{{ analytics[currency.code].last_day }} %</strong>
                      </p>
                    </h4>
                  </div>
                </div>
              </div>
            </div>
            <div class="row">
              <div class="col">
                <div class="card text-white bg-secondary mb-3">
                  <div class="card-body">
                    <h6 class="card-title text-center">
                      Last 7 days
                    </h6>
                    <h4>
                      <p class="card-text text-center">
                        {{ analytics[currency.code].last_week }} %
                      </p>
                    </h4>
                  </div>
                </div>
              </div>
            </div>
            <div class="row">
              <div class="col">
                <div class="card text-white bg-dark mb-3">
                  <div class="card-body">
                    <h6 class="card-title text-center">
                      This month
                    </h6>
                    <h4>
                      <p class="card-text text-center">
                        {{ analytics[currency.code].last_month }} %
                      </p>
                    </h4>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-else class="d-flex align-items-center">
        <strong>Loading...</strong>
        <div class="spinner-border ml-auto" role="status" aria-hidden="true" />
      </div>
    </main>
  </section>
</template>

<script>
import axios from '~/plugins/axios'
import lineChart from '~/components/line-chart'
import moment from 'moment'

export default {
  async asyncData({ req, params }) {
    const { data } = await axios.get(`currencies/`)
    const currencies = data.results

    const quotationsData = {}
    const promises = []
    for (let i = 0; i < currencies.length; i++) {
      const currency = currencies[i]
      promises.push(axios.get(`currencies/` + currency.code + `/quotations`))
    }

    let currencyData = []
    Promise.all(promises).then(function (responses) {
      for (let i = 0; i < responses.length; i++) {
        const response = responses[i]
        currencyData = response.data
        const quotations = currencyData.quotations.slice(Math.max(currencyData.quotations.length - 5, 0))

        quotationsData[currencyData.code] = {
          labels: quotations.map(quotation => moment(quotation.created).format('YYYY-MM-DD HH:mm')),
          datasets: [
            {
              label: currencyData.code,
              backgroundColor: '#282828',
              data: quotations.map(quotation => quotation.amount)
            }
          ]
        }
      }
    })

    const analyticsData = {}
    for (let i = 0; i < currencies.length; i++) {
      const currency = currencies[i]

      const { data } = await axios.get(`currencies/` + currency.code + `/analytics`)
      analyticsData[currency.code] = data
    }

    return {
      currencies: data.results,
      quotations: quotationsData,
      analytics: analyticsData
    }
  },
  components: {
    lineChart
  },
  data() {
    return {
      title: 'Abundantia - GeekHunter Code Challenge',
      analytics: [],
      currencies: [],
      showLine: false
    }
  },
  mounted() {
    this.showLine = true // showLine will only be set to true on the client. This keeps the DOM-tree in sync.
  },
  head() {
    return {
      title: this.title,
      meta: [
        { hid: 'description', name: 'description', content: 'GeekHunter Code Challenge' }
      ]
    }
  }
}
</script>

<style>
  body {background: #EDEBE8}
</style>
