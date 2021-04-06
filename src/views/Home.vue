<template>
  <v-app style="height:100%">
    <home-view/>
  </v-app>
</template>

<script>
// @ is an alias to /src
import HomeView from '@/components/HomeView.vue'

export default {
  name: 'Home',
  components: {
    HomeView
  }
}
</script>
