<template>
  <v-app>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>OCULUS</span>
        <span class="font-weight-light"> PROJECT</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn
        flat
        href="https://github.com/OCULUS-project"
        target="_blank"
      >
        <span class="mr-2">GITHUB</span>
      </v-btn>
    </v-toolbar>

    <v-content>
      <!-- <HelloWorld/> -->
      <AllJobs/>
    </v-content>
  </v-app>
</template>

<script lang="js">
import {Vue, Component, Prop} from 'vue-property-decorator';
import HelloWorld from './components/HelloWorld';
import AllJobs from './components/AllJobs';

export default Vue.extend({
  name: 'App',
  components: {
    HelloWorld,
    AllJobs,
  },
  data: () => {
    return {
      //
    };
  },
});
</script>
