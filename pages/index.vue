<template>
  <section class="container">
    <header class="text-center">
      <div class="row">
        <div class="col-12">
          <h1>
            Abundantia <br>
            <small>GeekHunter code challenge</small>
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
  async asyncData({ params }) {
    const { currencies } = await axios.get(`currencies/`)
    return { currencies: currencies }
  },
  components: {

  },
  data() {
    return {
      title: 'Abundantia - GeekHunter Code Challenge'
    }
  },
  head() {
    return {
      title: this.title,
      meta: [
        // hid is used as unique identifier. Do not use `vmid` for it as it will not work
        { hid: 'description', name: 'description', content: 'GeekHunter Code Challenge' }
      ]
    }
  }
}
</script>

<style>
</style>
