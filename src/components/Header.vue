<script>
export default {
  name: 'Header',
  methods: {
    goHome() {
      this.$router.push('/')
    }
  }
}
</script>

<template>
  <v-app-bar color="green-darken-1" density="comfortable" flat>
    <v-app-bar-title style="cursor: pointer;" @click="goHome">유비샘-조흥재연구원</v-app-bar-title>
    <v-spacer />
  </v-app-bar>
</template>
