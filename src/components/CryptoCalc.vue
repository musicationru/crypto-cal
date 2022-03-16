<template>
  <v-container>
    <v-card class="px-16 mx-16">
      <v-card-title>Amount I have</v-card-title>
      <v-row>
        <v-col>
          <v-select 
          v-model="myCurrency" 
          :items="currencies"
          item-text="name"
          item-value="name"
          return-object
          :prepend-icon="myCurrency.icon"
          @change="reset()"
          >
        </v-select>
        </v-col>
        <v-col>
          <v-text-field :disabled="myCurrency.name === gotCurrency.name" v-model="myCurrency.value" @input="calc('my')" value="myCurrency.value"></v-text-field>
        </v-col>
      </v-row>
      <v-card-title>I need</v-card-title>
      <v-row>
        <v-col>
          <v-select 
            v-model="gotCurrency" 
            :items="currencies"
            item-text="name"
            item-value="name"
            return-object
            :prepend-icon="gotCurrency.icon"
            @change="reset()"
            >
          </v-select>
        </v-col>
        <v-col>
            <v-text-field :disabled="myCurrency.name === gotCurrency.name" v-model="gotCurrency.value" @input="calc()"></v-text-field>
        </v-col>
      </v-row>
      <v-card-subtitle>Commision {{ fee }} {{gotCurrency.name}}</v-card-subtitle>
    </v-card>
  </v-container>
</template>

<script>
import axios from 'axios'

  export default {
    name: 'CryptoCalc',
    data() {
      return {
        myCurrency: { name: 'BTC', icon: 'mdi-currency-btc', value: 0},
        gotCurrency: { name: 'USD', icon: 'mdi-currency-usd', value: 0},
        currencies: [
          { name: 'EUR', icon: 'mdi-currency-eur', value: 0},
          { name: 'USD', icon: 'mdi-currency-usd', value: 0},
          { name: 'BTC', icon: 'mdi-currency-btc', value: 0},
          { name: 'ETH', icon: 'mdi-currency-eth', value: 0},
        ],
      }
    },
    computed: {
      fee() {
        return (this.gotCurrency.value / 100).toFixed(2)
      }
    },
    methods: {
      async calc(my) {
        const [firstResponse, secondResponse] = await Promise.all([
          axios.get(`https://api.kraken.com/0/public/Ticker?pair=${this.myCurrency.name}${this.gotCurrency.name}`),
          axios.get(`https://api.kraken.com/0/public/Ticker?pair=${this.gotCurrency.name}${this.myCurrency.name}`)
        ])
        let response = null
        if (firstResponse.data.result) {
          response = firstResponse
          const ask = Object.values(response.data.result)[0].a[0]
          if (my) {
            this.gotCurrency.value = ((this.myCurrency.value * ask) - this.fee).toFixed(2)
          } else {
            this.myCurrency.value = (this.gotCurrency.value / ask).toFixed(2)
          }
        } else {
          response = secondResponse
          const bid = Object.values(response.data.result)[0].b[0]
          if (my) {
            this.gotCurrency.value = ((this.myCurrency.value / bid) - this.fee).toFixed(2)
          } else {
            this.myCurrency.value = (this.gotCurrency.value * bid).toFixed(2)
          }
        }
        if (this.gotCurrency.value <= 0) {
          this.gotCurrency.value = 0
        }
      },
      reset() {
        this.myCurrency.value = 0
        this.gotCurrency.value = 0
      }
    }
  }
</script>
