<template>
  <v-menu offset-y>
    <template v-slot:activator="{ on }">
      <v-btn icon v-on="on" class="mx-3 my-auto">
        <v-avatar size="34">
          <template>
              <v-avatar size="45">
                <img class="rounded-circle portrait" alt :src="profileUrl" />
              </v-avatar>
            </template>
        </v-avatar>
      </v-btn>
    </template>
    <v-card width="300">
      <v-container grid-list-md>
        <v-layout row wrap>

          <v-flex xs4>
            <template>
              <v-avatar size="60">
                <img class="rounded-circle portrait" alt :src="profileUrl" />
              </v-avatar>
            </template>
          </v-flex>


          <v-flex xs8>
            <v-card-text>
              {{ getUsername }} 님<br />
              <hr />
              {{ getEmail }}
            </v-card-text>
          </v-flex>
        </v-layout>
          <v-btn target="_blank" @click="moveToMyPage" text style="width:180px;" 
            >MyPage</v-btn
          >
        <br />
        <v-btn
          target="_blank"
          text
          @click="logout"
          style="width:180px; color:red;"
          >Logout</v-btn
        >
      </v-container>
    </v-card>
  </v-menu>
</template>


<script>
import {router} from '@/routes'
export default {
  computed: {
    getEmail() {
      // 렌더링 시점때문에 mapgetters 사용시 오류가 나므로 이렇게 쓰기
      return this.$store.getters["userStore/getEmail"];
    },
    getUsername() {
      return this.$store.getters["userStore/getUsername"];
    },
    getLogintype() {
      return this.$store.getters["userStore/getLogintype"];
    },
    profileUrl () {
      return this.$store.getters["userStore/getProfileUrl"];
    },
  },

  methods: {
    logout() {
      if (this.getLogintype == "kakao") {
        window.Kakao.API.request({
          url: "/v1/user/unlink",
          success: () => {
            console.log("kakao log out")
          },
        });
        window.Kakao.Auth.logout(function() {
          console.log("토큰 만료 설정");
        });
      }
      if (this.getLogintype == "google") {
        var auth2 = window.gapi.auth2.getAuthInstance();
        auth2.signOut().then(function() {
          console.log("User log out");
          auth2.disconnect();
          });
      }
    this.$store.dispatch("userStore/logout");
    },
    moveToMyPage () {
            // 원래는 다른사람 uid을 받아오겠지만, 마이페이지니까 그냥 uid 준다
            const targetUid = this.$store.getters["userStore/getUserId"]
            // const name = this.$store.getters["userStore/getUsername"];
            // this.$store.commit('userStore/PUT_REQUEST_UID', targetUid)
            //uid 받아서 팔로우 요청(이것도 내꺼 그냥 준다)
            // this.$store.dispatch('userStore/GET_MEMBER', targetUid)
            this.$store.commit('userStore/MYSELF')
            router.push(`/user/mypage/${targetUid}`)
        },
  },
};
</script>


<style></style>
