<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <v-spacer></v-spacer>
      <div class="d-flex align-center">
        <v-img
          alt="DrChrono Remote Patient Monitoring Logo"
          class="shrink mt-1 hidden-sm-and-down"
          contain
          min-width="200"
          src="https://www.drchrono.com/site_media/images/logos/drchrono_logo_black_1400x399.4cd8bfb2149e.png"
          width="200"
        />

      </div>
      <h1 id="product-name">Remote Patient Monitoring - FHIR app</h1>
      <v-spacer></v-spacer>
    </v-app-bar>

    <v-main>
      <HelloWorld/>
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld';

export default {
  name: 'App',

  components: {
    HelloWorld,
  },

  data: () => ({
    //
  }),
};
</script>
<style type="text/css">
#product-name {
  margin-top: 10px;
  margin-left: 10px;
}
</style>