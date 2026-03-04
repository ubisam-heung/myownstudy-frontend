<script>
import Header from "@/components/Header.vue";
import Footer from "@/components/Footer.vue";
import axios from "axios";
import { useRouter } from "vue-router";

export default {
  name: "HelloAdd",
  components: {
    Header,
    Footer,
  },
  data() {
    return {
      firstName: "",
      email: "",
      firstNameRules: [v => !!v || "필수 입력 항목입니다."],
      emailRules: [
        v => !!v || "필수 입력 항목입니다.",
        v =>
          !v ||
          /^(?:[a-zA-Z0-9_'^&amp;/+-])+(?:\.(?:[a-zA-Z0-9_'^&amp;/+-])+)*@(?:[a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}$/.test(v) ||
          "유효한 이메일을 입력하세요",
      ],
      router: useRouter(),
    };
  },
  methods: {
    createHello() {
      if (!this.$refs.form) return;
      this.$refs.form.validate().then((result) => {
        if (!result.valid) return;
        axios({
          method: "post",
          url: "http://localhost:8080/api/helloes",
          data: {
            name: this.firstName,
            email: this.email,
          },
        })
          .then(() => {
            this.router.push("/hello");
          })
          .catch((error) => {
            alert(
              "추가 실패: " + (error.response?.data?.message || error.message)
            );
          });
      });
    },
  },
};
</script>
<template>
  <v-app>
    <Header />
    <v-main>
      <v-container
        class="d-flex justify-center align-center"
        style="min-height: 70vh"
      >
        <v-card elevation="8" width="400" class="pa-6">
          <v-card-title class="text-h5 text-center mb-4">
            <v-icon color="primary" size="32" class="me-2"
              >mdi-account-plus</v-icon
            >
            유저 추가
          </v-card-title>
          <v-form
            ref="form"
            validation-mode="eager"
            @submit.prevent="createHello"
          >
            <v-text-field
              v-model="firstName"
              :rules="firstNameRules"
              label="이름"
              placeholder="이름을 입력하세요"
              prepend-inner-icon="mdi-account"
              variant="outlined"
              class="mb-3"
              clearable
            ></v-text-field>
            <v-text-field
              v-model="email"
              :rules="emailRules"
              label="이메일"
              placeholder="이메일을 입력하세요"
              prepend-inner-icon="mdi-email"
              variant="outlined"
              class="mb-5"
              clearable
              type="email"
            ></v-text-field>
            <v-btn
              type="submit"
              color="primary"
              size="large"
              block
              elevation="2"
              prepend-icon="mdi-plus"
            >
              추가
            </v-btn>
          </v-form>
        </v-card>
      </v-container>
    </v-main>
    <Footer />
  </v-app>
</template>
