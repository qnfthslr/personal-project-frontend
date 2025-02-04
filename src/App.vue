<template>
  <v-app>
    <v-app-bar app color="black" dark>
      <div class="d-flex align-center">
        <router-link to="/">
          <v-img contain :src="require(`@/assets/LogoImage.png`)" transition="scale-transition" width="350" />
        </router-link>

        <div class="menu-container" style="transform: none;">
          <v-menu v-for="button in buttons" :key="button.id" :open-on-hover="true" offset-y>
            <template v-slot:activator="{ on }">
              <router-link :to="button.link">
                <v-btn v-on="on">
                  <v-icon class="font" style="font-style: normal;">{{ button.text }}</v-icon>
                </v-btn>
              </router-link>
            </template>

            <v-card class="menu-card">
              <router-link v-for="(image, index) in button.images" :key="image.id" :to="image.link">
                <transition-group>
                  <v-img :src="require(`@/assets/${image.src}`)" :key="image.id" contain class="menu-image" :style="{
                    animationDelay: `${index * 0.2}s`,
                    animationDuration: '2s'
                  }" />
                </transition-group>
              </router-link>
            </v-card>
          </v-menu>
        </div>
      </div>
      <v-spacer></v-spacer>

      <span v-if="accountName">{{ accountName }} 님 안녕하세요</span>

      <v-btn to="/account-regist-page" text>
        SIGNUP
        <v-icon>person_add_alt</v-icon>
      </v-btn>
      <v-btn to="/login-page" text>
        LOGIN
        <v-icon>login</v-icon>
      </v-btn>
    </v-app-bar>
    <v-main>
      <router-view />
    </v-main>
  </v-app>
</template>

<script>

import VueCookies from 'vue-cookies';
import { mapActions } from 'vuex';

const accountModule = 'accountModule'

export default {

  name: "App",
  created() {
    this.checkUserToken();
    setInterval(this.checkUserToken, 5000);
  },
  methods: {
    ...mapActions(accountModule, ['checkToken']),
    checkUserToken() {
      this.userToken = VueCookies.get('userToken');
      console.log('User Token:', this.userToken);
      this.checkToken(this.userToken).then((res) => {
        console.log(res)
        this.accountName = res.data.accountName;
      }).catch((error) => {
        console.error("회원 이름 요청 중 오류 발생 : ", error);
      });
    },
  },
  data: () => ({
    accountName: '',
    buttons: [
      {
        id: 1,
        text: "NOTICE",
        link: "/notice-page",
        images: [
          { id: 1, src: "noticelogo.png", link: "/notice-page", },
          { id: 2, src: "waytocomelogo.png", link: "/way-to-come-page", },
        ],
      },
      {
        id: 2,
        text: "SHOP",
        link: "/",
        images: [
          { id: 4, src: "suitlogo.png", link: "/", },
          { id: 5, src: "proteinlogo.png", link: "/", },
          { id: 6, src: "medicinelogo.png", link: "/account-list-page", },
        ],
      },
      {
        id: 3,
        text: "COMMUNITY",
        link: "/community-page"
      },
      {
        id: 4,
        text: "MYPAGE",
        link: "/check-password",
      },
    ],
  }),
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Black+Han+Sans&display=swap');

.font {
  /* font-family: 'Russo One', sans-serif; */
  font-family: 'Black Han Sans', sans-serif;
}

.menu-container {
  display: flex;
}

.menu-container>*:not(:last-child) {
  margin-right: 10px;
  margin-left: 20px;
}

.menu-image {
  margin: 8px;
  animation: fade-slide 5s;
  width: 80px;
  height: 50px;
}

.menu-card {
  background-color: black;
}

@keyframes fade-slide {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>