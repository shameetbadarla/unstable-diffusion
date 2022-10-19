<template>
  <div id="app">
    <v-main>
      <router-view/>
    </v-main>
  </div>
</template>

<script>

export default {
  name: 'App',

  data: () => ({
    //
  }),
};
</script>
<style>
body {
  background: linear-gradient(90deg, rgba(19,18,18,1) 0%, rgba(47,45,45,1) 43%, rgba(75,72,72,1) 100%);
}
</style>
