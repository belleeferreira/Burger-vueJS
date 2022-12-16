<template>
  <Navbar :logo="logo_src" :alt="app_name"/>
  <router-view />
  <Footer/>

</template>

<script>
import Navbar from "./components/Navbar.vue";
import Footer from "./components/Footer.vue";

export default {
  components: {
    Navbar,
    Footer
  },
  data(){
    return {
      logo_src: "/img/Logo.png",
      app_name: "Make your burger"
    }
  }
};
</script>

