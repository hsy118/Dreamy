<template>     
    <v-btn icon elevation="3" outlined>
        <img src="@/assets/snsLogin/kakao.png" @click="Kakaologin" alt="kakao"/>
    </v-btn>
</template>

<script>

export default {
    name: "kakaoLogin",
    methods: {
        Kakaologin() {
            window.Kakao.Auth.login({
                    scope : 'profile, account_email',
                    success: this.kakaogetUserinfo,
                });
        },

        kakaogetUserinfo(){
                window.Kakao.API.request({
                url:'/v2/user/me',
                 success: ((response) =>{
                    const user={
                        email:response.kakao_account.email,
                        name:response.kakao_account.profile.nickname,
                        logintype:"kakao"
                    }
                    this.$store.dispatch("userStore/getSocialUserinfo",user)
                }),
                  fail: function(error) {
                  console.log(error.message);
               }
           })
        },
    },
    }
</script>
<style scoped>
</style>
