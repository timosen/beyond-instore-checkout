<template>
  <b-navbar toggleable="lg" :sticky="true" type="light" variant="light">
    <b-navbar-brand to="/">
      <img class="p-0 m-0" :src="shopLogo" :alt="shopName"/>
    </b-navbar-brand>

    <b-nav-text>{{ shopName }}</b-nav-text>

    <b-navbar-nav class="font-weight-bold">
      <b-nav-item to="/install">Install</b-nav-item>
    </b-navbar-nav>
  </b-navbar>
</template>

<script>
/* eslint-disable */
export default {
  name: "NavBar",

  /*
  props: {
    shopName: {
      type: String,
      required: true,
    },
    shopLogo: {
      type: String,
      required: true,
    },
  },
  */

  props: ["shopName", "shopLogo"],
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
