<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
    
        <v-icon class="mr-1">mdi-calculator-variant</v-icon>
        <span class="mr-2">Crypto Calc</span>
    </v-app-bar>

    <v-main>
      <CryptoCalc></CryptoCalc>
    </v-main>
  </v-app>
</template>

<script>
import CryptoCalc from "./components/CryptoCalc.vue";

export default {
    name: "App",
    components: { CryptoCalc }
};
</script>
