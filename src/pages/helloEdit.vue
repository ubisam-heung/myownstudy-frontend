<script>
import Header from "@/components/Header.vue";
import Footer from "@/components/Footer.vue";
import { ref } from "vue";
import axios from "axios";
import { useRouter, useRoute } from "vue-router";

export default {
  name: "HelloEdit",
  components: {
    Header,
    Footer,
  },
  setup() {
    const firstName = ref("");
    const email = ref("");
    const form = ref(null);
    const router = useRouter();
    const route = useRoute();
    const required = (v) => !!v || "필수 입력 항목입니다.";
    const emailRule = (v) =>
      !v ||
      /^(?:[a-zA-Z0-9_'^&amp;/+-])+(?:\.(?:[a-zA-Z0-9_'^&amp;/+-])+)*@(?:[a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}$/.test(
        v,
      ) ||
      "유효한 이메일을 입력하세요";
    const firstNameRules = [required];
    const emailRules = [required, emailRule];

    // 데이터 조회 및 채우기
    const fetchHello = (id) => {
      axios({
        method: "post",
        url: "http://localhost:8080/api/helloes/search",
        data: {
          helloId: id,
        },
      })
        .then((res) => {
          const hello = res.data._embedded.helloes?.[0];
          if (hello) {
            firstName.value = hello.name;
            email.value = hello.email;
          }
        })
        .catch((e) => {
          alert("데이터 조회 실패: " + (e.response?.data?.message || e.message));
        });
    };

    // onMounted에서 id로 데이터 조회
    onMounted(() => {
      const id = route.params.id;
      if (id) fetchHello(id);
    });

    const submitForm = () => {
      if (!form.value) return;
      form.value.validate().then((result) => {
        if (!result.valid) return;
        axios({
          method: "put",
          url: "http://localhost:8080/api/helloes",
          data: {
            name: firstName.value,
            email: email.value,
          },
        })
          .then(() => {
            router.push("/hello");
          })
          .catch((e) => {
            alert("수정 실패: " + (e.response?.data?.message || e.message));
          });
      });
    };

    return {
      firstName,
      email,
      form,
      firstNameRules,
      emailRules,
      submitForm,
    };
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
            유저 수정
          </v-card-title>
          <v-form
            ref="form"
            validation-mode="eager"
            @submit.prevent="submitForm"
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
            <v-row class="mt-2" dense>
              <v-col cols="6" class="pr-1">
                <v-btn
                  type="submit"
                  color="primary"
                  size="large"
                  block
                  elevation="2"
                >
                  <v-icon start>mdi-pencil</v-icon>
                  수정
                </v-btn>
              </v-col>
              <v-col cols="6" class="pl-1">
                <v-btn
                  color="error"
                  size="large"
                  block
                  elevation="2"
                >
                  <v-icon start>mdi-delete</v-icon>
                  삭제
                </v-btn>
              </v-col>
            </v-row>
          </v-form>
        </v-card>
      </v-container>
    </v-main>
    <Footer />
  </v-app>
</template>
