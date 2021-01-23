<template>
  <v-app>
    <v-main>
      <Inicio/>
    </v-main>
  </v-app>
</template>

<script>
import Inicio from './views/inicio';

export default {
  name: 'App',

  components: {
    Inicio,
  },

  data: () => ({
    //
  }),
};
</script>

<style>
html {
  overflow: hidden; /* Hide scrollbars */
}
</style>
