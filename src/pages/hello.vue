<template>
  Hello
  COC철학을 따른다.
  <div class="mt-1">
    <v-text-field clearable label="Label" prepend-icon="$vuetify">
    </v-text-field>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: 'Hello',

    mounted () {
      console.log('Hello mounted')
      axios({
        method: 'post',
        url: 'http://localhost:8080/api/helloes/search',
        data: {
          keyword: 'hello',
        },
      })
        .then(res => {
          console.log(res)
        })
        .catch(error => {
          console.log(error)
        })
    },
  }
</script>
