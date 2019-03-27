<template>
  <q-pull-to-refresh :handler="refresher">
    <q-page class="flex flex-center">

      <div v-if="fetchMade">
      <p>EOS in Posession: {{ eosHave }}</p>
      <p>Price per EOS token ${{ eosPriceAud.toFixed(2) }}</p>
      <p>Total worth ${{ totalWorth }} AUD</p>
      </div>
      <p v-else-if="error">
        Unable to fetch the latest EOS price.
      </p>
      <p v-else>
        Fetching EOS price...
      </p>
    </q-page>
  </q-pull-to-refresh>
</template>

<style>

</style>

<script>
export default {
  name: 'PageIndex',
  data () {
    return {
      fetchMade: false,
      error: false,
      eosPriceAud: null,
      eosHave: 22.1217
    }
  },
  mounted () {
    this.fetchEosPrice()
  },
  computed: {
    totalWorth: function () {
      return (this.eosHave * this.eosPriceAud).toFixed(2)
    }
  },
  methods: {
    fetchEosPrice: async function () {
      this.$q.loadingBar.start()
      try {
        const res = await this.$axios.get('https://acx.io/api/v2/tickers')

        this.$q.loadingBar.stop()
        this.eosPriceAud = Number(res.data.eosaud.ticker.last)
        this.fetchMade = true
      } catch (e) {
        this.$q.loadingBar.stop()
        this.error = true
      }
    },
    refresher: async function (done) {
      await this.fetchEosPrice()
      done()
    }
  }
}
</script>
