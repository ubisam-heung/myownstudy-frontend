<script>
import axios from "axios";
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
      posts: [],
      headers: [
        {
          align: "start",
          key: "id",
          sortable: true,
          title: "번호",
        },
        { key: "name", title: "이름" },
        { key: "email", title: "이메일" },
        { key: "actions", title: "수정 / 삭제", sortable: false, align: "end" },
      ],
      search: "",
      sortBy: [],
      sortField: "id", 
      sortOrder: "asc", 
      page: 1, 
      editedRowId: null,
      editedRowTimer: null, 
      dialog: false,
      dialogMode: "add",
      form: {
        id: null,
        name: "",
        email: "",
      },
      nameRules: [(v) => !!v || "필수 입력 항목입니다."],
      emailRules: [
        (v) => !!v || "필수 입력 항목입니다.",
        (v) =>
          !v ||
          /^(?:[a-zA-Z0-9_'^&/+-])+(?:\.(?:[a-zA-Z0-9_'^&/+-])+)*@(?:[a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}$/.test(
            v,
          ) ||
          "유효한 이메일을 입력하세요",
      ],
      deleteDialog: false,
      deleteTarget: null,
    };
  },
  methods: {
    //////////////////
    // 테이블 불러오기
    //////////////////
    fetchList() {
      axios({
        method: "post",
        url: "http://localhost:8080/api/helloes/search",
        data: {
          keyword: "",
        },
        params: {
          size: 1000,
          sort: this.sortField + (this.sortOrder === 'desc' ? ',desc' : ''),
        },
      })
        .then((res) => {
          this.posts = res.data._embedded.helloes;
        })
        .catch((error) => {
          console.error("목록 가져오기 실패:", error);
        });
    },
    //////////////////
    // 유저 추가 다이얼로그 열기
    //////////////////
    openAddDialog() {
      this.dialogMode = "add";
      this.form = { id: null, name: "", email: "" };
      this.dialog = true;
    },
    //////////////////
    // 유저 수정 다이얼로그 열기
    //////////////////
    openEditDialog(item) {
      this.dialogMode = "edit";
      this.form = { id: item.id, name: item.name, email: item.email };
      this.dialog = true;
    },
    //////////////////
    // 유저 추가/수정 저장
    //////////////////
    saveHello() {
      if (!this.$refs.form) return;
      this.$refs.form.validate().then((result) => {
        if (!result.valid) return;
        if (this.dialogMode === "add") {
          axios({
            method: "post",
            url: "http://localhost:8080/api/helloes",
            data: {
              name: this.form.name,
              email: this.form.email,
            },
          })
            .then((res) => {
              this.dialog = false;
              if (res.data) {
                this.posts.unshift(res.data);
              } else {
                this.fetchList();
              }
              this.page = 1;
            })
            .catch((error) => {
              alert(
                "추가 실패: " +
                  (error.response?.data?.message || error.message),
              );
            });
        } else if (this.dialogMode === "edit") {
          axios({
            method: "put",
            url: `http://localhost:8080/api/helloes/${this.form.id}`,
            data: {
              name: this.form.name,
              email: this.form.email,
            },
          })
            .then(() => {
              this.dialog = false;
              this.editedRowId = this.form.id;
              this.fetchList();
              if (this.editedRowTimer) clearTimeout(this.editedRowTimer);
              this.editedRowTimer = setTimeout(() => {
                this.editedRowId = null;
              }, 3000);
            })
            .catch((error) => {
              alert(
                "수정 실패: " +
                  (error.response?.data?.message || error.message),
              );
            });
        }
      });
    },
    //////////////////
    // 유저 삭제
    //////////////////
    openDeleteDialog(item) {
      this.deleteTarget = item;
      this.deleteDialog = true;
    },
    confirmDelete() {
      if (!this.deleteTarget) return;
      axios({
        method: "delete",
        url: `http://localhost:8080/api/helloes/${this.deleteTarget.id}`,
      })
        .then(() => {
          this.fetchList();
          this.deleteDialog = false;
          this.deleteTarget = null;
        })
        .catch((e) => {
          alert("삭제 실패: " + (e.response?.data?.message || e.message));
        });
    },
    cancelDelete() {
      this.deleteDialog = false;
      this.deleteTarget = null;
    },
  },
  //////////////////
  // 컴포넌트 마운트 시 데이터 불러오기
  //////////////////
  mounted() {
    this.fetchList();
    document.addEventListener('click', this.clearEditedRow, true);
  },
  beforeUnmount() {
    document.removeEventListener('click', this.clearEditedRow, true);
  },
  watch: {
    sortBy(val) {
      if (val && val.length > 0) {
        this.sortField = val[0].key;
        this.sortOrder = val[0].order;
        this.fetchList();
      }
    },
    page() {
      this.fetchList();
    }
  },
  methods: {
    //////////////////
    // 테이블 불러오기
    //////////////////
    fetchList() {
      axios({
        method: "post",
        url: "http://localhost:8080/api/helloes/search",
        data: {
          keyword: "",
        },
        params: {
          size: 1000,
          sort: this.sortField + (this.sortOrder === 'desc' ? ',desc' : ''),
        },
      })
        .then((res) => {
          this.posts = res.data._embedded.helloes;
        })
        .catch((error) => {
          console.error("목록 가져오기 실패:", error);
        });
    },
    //////////////////
    // 유저 추가 다이얼로그 열기
    //////////////////
    openAddDialog() {
      this.dialogMode = "add";
      this.form = { id: null, name: "", email: "" };
      this.dialog = true;
    },
    //////////////////
    // 유저 수정 다이얼로그 열기
    //////////////////
    openEditDialog(item) {
      this.dialogMode = "edit";
      this.form = { id: item.id, name: item.name, email: item.email };
      this.dialog = true;
    },
    //////////////////
    // 유저 추가/수정 저장
    //////////////////
    saveHello() {
      if (!this.$refs.form) return;
      this.$refs.form.validate().then((result) => {
        if (!result.valid) return;
        if (this.dialogMode === "add") {
          axios({
            method: "post",
            url: "http://localhost:8080/api/helloes",
            data: {
              name: this.form.name,
              email: this.form.email,
            },
          })
            .then((res) => {
              this.dialog = false;
              // 1페이지 맨 위로 추가
              this.page = 1;
              this.fetchList();
            })
            .catch((error) => {
              alert(
                "추가 실패: " +
                  (error.response?.data?.message || error.message),
              );
            });
        } else if (this.dialogMode === "edit") {
          axios({
            method: "put",
            url: `http://localhost:8080/api/helloes/${this.form.id}`,
            data: {
              name: this.form.name,
              email: this.form.email,
            },
          })
            .then(() => {
              this.dialog = false;
              this.editedRowId = this.form.id; // 수정된 행 id 저장
              this.fetchList();
              // 3초 후 색상 원복
              if (this.editedRowTimer) clearTimeout(this.editedRowTimer);
              this.editedRowTimer = setTimeout(() => {
                this.editedRowId = null;
              }, 3000);
            })
            .catch((error) => {
              alert(
                "수정 실패: " +
                  (error.response?.data?.message || error.message),
              );
            });
        }
      });
    },
    //////////////////
    // 유저 삭제
    //////////////////
    // 삭제 다이얼로그 열기
    openDeleteDialog(item) {
      this.deleteTarget = item;
      this.deleteDialog = true;
    },
    // 실제 삭제
    confirmDelete() {
      if (!this.deleteTarget) return;
      axios({
        method: "delete",
        url: `http://localhost:8080/api/helloes/${this.deleteTarget.id}`,
      })
        .then(() => {
          this.fetchList();
          this.deleteDialog = false;
          this.deleteTarget = null;
        })
        .catch((e) => {
          alert("삭제 실패: " + (e.response?.data?.message || e.message));
        });
    },
    cancelDelete() {
      this.deleteDialog = false;
      this.deleteTarget = null;
    },
    //////////////////
    // 수정 강조 해제
    //////////////////
    clearEditedRow(e) {
      // 테이블 내 클릭은 무시
      if (e && e.target && e.target.closest && e.target.closest('.v-data-table')) return;
      this.editedRowId = null;
    },
  },
};
</script>
<template>
  <v-app>
    <Header />
    <v-main>
      <v-container class="py-8">
        <v-card title="유저목록" flat>
          <!-- 추가버튼 -->
          <template v-slot:append>
            <v-btn color="blue" prepend-icon="mdi-plus" @click="openAddDialog">
              추가
            </v-btn>
          </template>

          <!-- 검색바 -->
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

          <!-- 유저 테이블 -->
          <v-data-table
            :headers="headers"
            :items="posts"
            :search="search"
            :sort-by="sortBy"
            :items-per-page="5"
            :items-per-page-options="[5, 10, 15, 20]"
            items-per-page-text="페이지당:"
            v-model:page="page"
          >
            <template #item="{ item }">
              <tr :style="item.id === editedRowId ? 'background:#ffe082' : ''">
                <td>{{ item.id }}</td>
                <td>{{ item.name }}</td>
                <td>{{ item.email }}</td>
                <td align="right">
                  <v-btn icon color="primary" @click.stop="openEditDialog(item)" density="compact">
                    <v-icon size="18">mdi-pencil</v-icon>
                  </v-btn>
                  <v-btn icon color="error" @click.stop="openDeleteDialog(item)" density="compact">
                    <v-icon size="18">mdi-delete</v-icon>
                  </v-btn>
                </td>
              </tr>
            </template>
          </v-data-table>
                <!-- 삭제 다이얼로그 -->
                <v-dialog v-model="deleteDialog" max-width="350">
                  <v-card>
                    <v-card-title class="text-h6">정말 삭제하시겠습니까?</v-card-title>
                    <v-card-actions>
                      <v-spacer></v-spacer>
                      <v-btn color="grey" text @click="cancelDelete">취소</v-btn>
                      <v-btn color="red" text @click="confirmDelete">삭제</v-btn>
                      <v-btn icon @click="cancelDelete" style="position:absolute;right:8px;top:8px"><v-icon>mdi-close</v-icon></v-btn>
                    </v-card-actions>
                  </v-card>
                </v-dialog>
        </v-card>

        <!-- 유저 추가/수정 다이얼로그 -->
        <v-dialog persistent v-model="dialog" max-width="450">
          <v-card elevation="8" class="pa-6">
            <v-card-title
              class="d-flex align-center justify-center text-h5 mb-4"
              style="position: relative"
            >
              <v-icon color="primary" size="32" class="me-2">
                {{ dialogMode === "add" ? "mdi-account-plus" : "mdi-pencil" }}
              </v-icon>
              {{ dialogMode === "add" ? "유저 추가" : "유저 수정" }}
              <v-btn
                icon
                size="x-small"
                elevation="0"
                style="
                  position: absolute;
                  right: 0;
                  top: 0;
                  min-width: 28px;
                  height: 28px;
                  box-shadow: none;
                "
                @click="dialog = false"
              >
                <v-icon size="16">mdi-close</v-icon>
              </v-btn>
            </v-card-title>
            <v-form
              ref="form"
              validation-mode="eager"
              @submit.prevent="saveHello"
            >
              <v-text-field
                v-model="form.name"
                :rules="nameRules"
                label="이름"
                placeholder="이름을 입력하세요"
                prepend-inner-icon="mdi-account"
                variant="outlined"
                class="mb-3"
                clearable
              ></v-text-field>
              <v-text-field
                v-model="form.email"
                :rules="emailRules"
                label="이메일"
                placeholder="이메일을 입력하세요"
                prepend-inner-icon="mdi-email"
                variant="outlined"
                class="mb-5"
                clearable
                type="email"
              ></v-text-field>
              <v-row class="mt-2" density="comfortable">
                <v-col cols="12">
                  <v-btn
                    type="submit"
                    :color="dialogMode === 'add' ? 'primary' : 'success'"
                    size="large"
                    block
                    elevation="2"
                    :prepend-icon="
                      dialogMode === 'add' ? 'mdi-plus' : 'mdi-pencil'
                    "
                  >
                    {{ dialogMode === "add" ? "추가" : "수정" }}
                  </v-btn>
                </v-col>
              </v-row>
            </v-form>
          </v-card>
        </v-dialog>
      </v-container>
    </v-main>
    <Footer />
  </v-app>
</template>
