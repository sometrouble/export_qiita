<template>
  <v-app>
    <v-app-bar
      app
      dark
      dense
    >
      <div class="d-flex align-center">
        <span class="title">Qiita記事Export</span>
      </div>
    </v-app-bar>

    <v-content>
      <HelloWorld/>
    </v-content>
  </v-app>
</template>

<script lang="ts">
import Vue from 'vue';
import HelloWorld from './components/HelloWorld.vue';

export default Vue.extend({
  name: 'App',

  components: {
    HelloWorld,
  },

  data: () => ({
    //
  }),
});
</script>
