
<script>
  import axios from 'axios'
  import { computed, ref } from 'vue'
  
  export default {
    
    name: 'Hello',
    data () {
      return {
          icons: [
            { icon: 'mdi-microphone-message', url: 'https://www.notion.so/4-3181dd99f25d80c69513dc2934fabece#3181dd99f25d809cbaedc66d8b7df154' },
            { icon: 'mdi-wifi', url: 'https://ubisam.hanbiro.net/ngw/app/#/' },
            { icon: 'mdi-github', url: 'https://github.com/ubisam-heung' },
          ],
          posts: ref([
            { id: 1, name: '', email: '' },
          ]),
          headers: ref([
          {
            align: 'start',
            key: 'id',
            sortable: true,
            title: '번호',
          },
          { key: 'name', title: '이름' },
          { key: 'email', title: '이메일' },
        ]),
        search: ref(''),
      }
    },
    methods: {
      goHome () {
        this.$router.push('/')
      },
    },
    mounted () {
      console.log('Hello mounted')
      axios({
        method: 'post',
        url: 'http://localhost:8080/api/helloes/search',
        data: {
          keyword: '',
        },
      })
        .then(res => {
          console.log(res.data._embedded)
          this.posts = res.data._embedded.helloes
        })
        .catch(error => {
          console.error('목록 가져오기 실패:', error)
        })
    },
  }
</script>
<template>
  <v-app>
    <v-app-bar color="green-darken-1" density="comfortable" flat>
      <v-app-bar-title style="cursor: pointer;" @click="goHome">유비샘-조흥재연구원</v-app-bar-title>
      <v-spacer />
    </v-app-bar>
    <v-main>
      <v-container class="py-8">
        <v-card
          title="유저목록"
          flat
        >
          <template v-slot:text>
            <v-text-field
              v-model="search"
              label="Search"
              prepend-inner-icon="mdi-magnify"
              variant="outlined"
              hide-details
              single-line
            ></v-text-field>
          </template>

          <v-data-table
            :headers="headers"
            :items="posts"
            :search="search"
          >
          </v-data-table>
        </v-card>
      </v-container>
    </v-main>
    <v-footer class="d-flex flex-column" color="teal" rounded="lg">
      <div class="d-flex w-100 align-center px-4 py-2">
        <strong>(주) 유비샘-조흥재연구원</strong>

        <div class="d-flex ga-2 ms-auto">
          <v-btn
            v-for="item in icons"
            :key="item.icon"
            :icon="item.icon"
            size="small"
            variant="plain"
            :href="item.url"
            target="_blank"
            rel="noopener"
          ></v-btn>
        </div>
      </div>

      <div class="px-4 py-2 bg-surface-variant text-center w-100 rounded-lg">
        <strong>(주) 유비샘</strong>
      </div>
    </v-footer>
  </v-app>
</template>

