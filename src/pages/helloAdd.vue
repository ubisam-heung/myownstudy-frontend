<script>
  import Header from '@/components/Header.vue'
  import Footer from '@/components/Footer.vue'

export default {
  name: 'HelloAdd',
  components: {
    Header,
    Footer
  },
  data() {
    const required = v => !!v || '필수 입력 항목입니다.'
    const emailRule = v => !v || /^(?:[a-zA-Z0-9_'^&amp;/+-])+(?:\.(?:[a-zA-Z0-9_'^&amp;/+-])+)*@(?:[a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}$/.test(v) || '유효한 이메일을 입력하세요'
    return {
      firstName: '',
      email: '',
      firstNameRules: [required],
      emailRules: [required, emailRule],
    }
  }
}
</script>
<template>
  <v-app>
    <Header />
    <v-main>
      <v-container class="d-flex justify-center align-center" style="min-height: 70vh;">
        <v-card elevation="8" width="400" class="pa-6">
          <v-card-title class="text-h5 text-center mb-4">
            <v-icon color="primary" size="32" class="me-2">mdi-account-plus</v-icon>
            유저 추가
          </v-card-title>
          <v-form fast-fail @submit.prevent>
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
            <v-btn type="submit" color="primary" size="large" block elevation="2">
              <v-icon start>mdi-plus</v-icon>
              추가
            </v-btn>
          </v-form>
        </v-card>
      </v-container>
    </v-main>
    <Footer />
  </v-app>
</template>