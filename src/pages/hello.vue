<script>
import axios from "axios";
import { ref } from "vue";
import Header from "@/components/Header.vue";
import Footer from "@/components/Footer.vue";

export default {
  name: "Hello",
  components: {
    Header,
    Footer,
  },
  data() {
    return {
      posts: ref([{ id: 1, name: "", email: "" }]),
      headers: ref([
        {
          align: "start",
          key: "id",
          sortable: true,
          title: "번호",
        },
        { key: "name", title: "이름" },
        { key: "email", title: "이메일" },
      ]),
      search: ref(""),
    };
  },
  methods: {
    goAdd() {
      this.$router.push("/helloAdd");
    },
  },
  mounted() {
    console.log("Hello mounted");
    axios({
      method: "post",
      url: "http://localhost:8080/api/helloes/search",
      data: {
        keyword: "",
      },
      params: {
        size: 1000,
      },
    })
      .then((res) => {
        console.log(res.data._embedded);
        this.posts = res.data._embedded.helloes;
      })
      .catch((error) => {
        console.error("목록 가져오기 실패:", error);
      });
  },
};
</script>
<template>
  <v-app>
    <Header />
    <v-main>
      <v-container class="py-8">
        <v-card title="유저목록" flat>
          <template v-slot:append>
            <v-btn color="blue" prepend-icon="mdi-plus" @click="goAdd">
              추가
            </v-btn>
          </template>

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
            :items-per-page="5"
            :items-per-page-options="[5, 10, 15, 20]"
            items-per-page-text="페이지당:"
          >
          </v-data-table>
        </v-card>
      </v-container>
    </v-main>
    <Footer />
  </v-app>
</template>
