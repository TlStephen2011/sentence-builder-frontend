<template>
  <v-app>
    <v-main>
      <SentenceBuilder/>
    </v-main>
  </v-app>
</template>

<script>
import SentenceBuilder from './components/SentenceBuilder';

export default {
  name: 'App',

  components: {
    SentenceBuilder,
  },

  data: () => ({
    //
  }),
};
</script>
