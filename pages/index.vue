<script lang="ts" setup>
import { User } from "~/types/User";
import axios, { AxiosRequestConfig, AxiosResponse, AxiosError } from "axios";

const isOpen = ref<boolean>(false);
const isLoading = ref<boolean>(false);

// Cardリストの背景色を交互に切り替えるためのstyle用オブジェクト定義
const styleForNonSelectedEvenRow: object = { "background-color": "#FFFFFF" };
const styleForNonSelectedOddRow: object = { "background-color": "#DAF4E9" };

// useFetch()で、jsonplaceholderからユーザのリストを取得
const {
  pending,
  error,
  data: usersData,
} = useFetch<User[]>("https://jsonplaceholder.typicode.com/users", {
  onResponse({ request, response, options }) {
    return response._data;
  },
  //   immediate: false,
});

const clickAddUser = () => {
  const _empty_user: User = {
    id: 0,
    name: "",
    username: "",
    email: "",
    address: {
      street: "",
      suite: "",
      city: "",
      zipcode: "",
      geo: {
        lat: "",
        lng: "",
      },
    },
    phone: "",
    website: "",
    company: {
      name: "",
      catchPhrase: "",
      bs: "",
    },
  };

  if (usersData != null) {
    // cardのユーザを追加する
    usersData.value?.push(_empty_user);
    // cardを追加後、最下部にスクロールさせる
    nextTick(() => {
      const target = document.querySelector("#container");
      if (target != null) {
        const bottom =
          document.documentElement.scrollHeight -
          document.documentElement.clientHeight;
        window.scroll(0, bottom);
      }
    });
  }
};

// 送信コンファームダイアログ表示切替
const openConfirmDialog = () => {
  isOpen.value = !isOpen.value;
};

const saveFatch = async () => {
  // ※あくまでテストなので、単一userではなくuserのリストを送ります。
  type UserPostResponse = {
    users: User[];
    id: number;
  };

  // ローディング表示
  isLoading.value = true;
  // axiosを利用してjsonplaceholderへPOSTする
  axios
    .post<UserPostResponse>("https://jsonplaceholder.typicode.com/users", {
      users: usersData.value,
    })
    .then((res: AxiosResponse<any>) => {
      const { data: response, status } = res;
      // POST結果のレスポンス表示
      console.log("response", response);
    })
    .catch((e: AxiosError<{ error: string }>) => {
      // エラー処理
    })
    .finally(() => {
      // 送信終わったらローディング、disable無効にする
      isLoading.value = false;
      isOpen.value = false;
    });
};
</script>

<template>
  <div class="mb-4 pb-3">
    <span>{{ error }}</span>
    <!-- API読み込み完了までローディング表示 -->
    <div v-if="pending" class="text-center mt-10">
      <v-progress-circular
        indeterminate
        color="teal-accent-4"
      ></v-progress-circular>
    </div>
    <!-- API読み込んでカードのリストを表示 -->
    <div v-else-if="usersData">
      <div id="container">
        <!-- リストトランジション -->
        <transition-group name="list" tag="div">
          <div v-for="(item, index) in usersData" :key="index">
            <UserEditCard
              v-model="usersData[index]"
              :style="[
                index % 2 === 0
                  ? styleForNonSelectedEvenRow
                  : styleForNonSelectedOddRow,
              ]"
            />
          </div>
        </transition-group>
      </div>

      <v-btn
        @click="openConfirmDialog"
        ripple
        icon
        position="fixed"
        color="teal-accent-4"
        style="bottom: 70px; right: 40px"
      >
        <v-icon>mdi-content-save-edit</v-icon>
      </v-btn>
      <div class="text-center">
        <v-btn
          color="teal-accent-4"
          prepend-icon="mdi-account-plus"
          @click="clickAddUser"
        >
          add User
        </v-btn>
      </div>
    </div>

    <v-dialog v-model="isOpen">
      <v-card class="mx-auto pa-5 text-xs-center">
        ユーザーデータを保存しますか？
        <v-btn
          @click="saveFatch"
          class="mx-auto"
          width="100"
          color="teal-accent-4"
          :loading="isLoading"
          :disabled="isLoading"
        >
          保存する
        </v-btn>
      </v-card>
    </v-dialog>
  </div>
</template>

<style scoped>
/* ADD USER押下時のトランジション */
.list-item {
  display: inline-block;
  margin-right: 10px;
}
.list-enter-active,
.list-leave-active {
  transition: all 1.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
</style>
