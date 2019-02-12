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
      <div v-for="currency in currencies" :key="currency.code" class="row currency-container">
        <div class="col-12">
          <h3>{{ currency.name }}</h3>
        </div>
        <div class="col-9">

        </div>
        <div class="col-3">

        </div>
      </div>
    </main>
  </section>
</template>

<script>
import axios from '~/plugins/axios'

export default {
  async asyncData({ req, params }) {
    const { data } = await axios.get(`currencies/`)
    const currencies = data.results

    const quotationsData = []
    for (let i = 0; i < currencies.length; i++) {
      const currency = currencies[i]
      axios.get(`currencies/` + currency.code + `/quotations`).then((response) => {
        quotationsData[i] = response.data
      })
    }

    const analyticsData = []
    for (let i = 0; i < currencies.length; i++) {
      const currency = currencies[i]
      axios.get(`currencies/` + currency.code + `/analytics`).then((response) => {
        analyticsData[i] = response.data
      })
    }

    return { currencies: data.results, quotations: quotationsData, analyticsData: analyticsData }
  },
  components: {

  },
  data() {
    return {
      title: 'Abundantia - GeekHunter Code Challenge',
      analytics: [],
      currencies: []
    }
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
</style>
