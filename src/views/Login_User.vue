<template>
  <div class="background sm:bg-light bg-white">
    <div class="md:grid md:grid-cols-2">
      <!-- image -->
      <div>
        <img
          src="https://helioscore.sytes.net/backend/public/images/LoginHelio.png"
          class="justify-center place-content-center h-screen md:block hidden"
        />
      </div>

      <!-- login -->
      <div class="box bg-white">
        <div class="pt-14 text-xl font-bold text-primary justify-center flex">
          <h1>เข้าสู่ระบบ</h1>
        </div>

        <form @submit.prevent="login()">
          <div class="pt-2 lg:mx-20 md:mx-12 mx-20">
            <div class="flex justify-center lg:mt-20 md:mt-10 mt-20">
              <span class="material-symbols-outlined"> alternate_email </span>
              <input
                v-model="user"
                type="text"
                placeholder="อีเมล"
                class="border-b border-gray50"
              />
            </div>
            <div class="flex justify-center mt-10">
              <span class="material-symbols-outlined"> lock </span>
              <input
                v-model="pass"
                type="password"
                placeholder="รหัสผ่าน"
                class="border-b border-gray50"
                maxlength="25"
              />
            </div>

            <div class="text-gray100 flex justify-end text-xs mt-2">
              <router-link to="/helioscore/forgotpassword">
                ลืมรหัสผ่าน
              </router-link>
            </div>
          </div>
          <div class="flex justify-center mt-14 lg:mx-20 md:mx-10 mx-20">
            <button class="bg-primary rounded w-full justify-center flex py-2">
              <p class="text-white text-sm">เข้าสู่ระบบ</p>
            </button>
          </div>
        </form>

        <div class="flex justify-center my-5 text-gray100">
          <p>or</p>
        </div>

        <div class="flex justify-center lg:mx-20 md:mx-10 mx-20">
          <GoogleLogin :callback="callback" />
        </div>

        <div class="flex justify-center mt-10 md:text-sm text-xs">
          <p class="inline-block text-gray100">หากคุณเป็นสมาชิกใหม่</p>
          <p class="inline-block text-primary underline">
            <a href="/helioscore/signup"> สร้างบัญชี</a>
          </p>
          <p class="inline-block text-gray100">ที่นี่</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { decodeCredential } from "vue3-google-login";
import router from "@/router";

const callback = (response) => {
  // This callback will be triggered when the user selects or login to
  // his Google account from the popup
  const userData = decodeCredential(response.credential);
  const url = "helio/account/google/redirect";
  axios
    .post(url, {
      firstName: userData.given_name,
      lastName: userData.family_name,
      email: userData.email,
      googleId: userData.sub,
      image: userData.picture,
    })
    .then((response) => {
      if (response.data.statusCode === 200) {
        if (response.data.data.school == 0) {
          localStorage.setItem("token", response.data.data.token);
          localStorage.setItem("school", true);
          return router.push({ path: "/helioscore/school" });
        } else {
          localStorage.setItem("token", response.data.data.token);
          return router.push({ path: "/helioscore" });
        }
      }
      if(response.data.statusCode === 403){
        alert("บัญชีนี้ถูกลงทะเบียนด้วยช่องทางอื่น")
      }
    })
    .catch((err) => {
      alert(err.response.data);
    });
};
</script>

<script>
import axios from "axios";
import { ref } from 'vue'

const user = ref()
const pass = ref()

export default {
  data() {
    return {
      url: "helio/account/login",
      email: "",
    };
  },

  methods: {
    async login() {
      if (!user.value || !pass.value) {
        alert("กรุณากรอก อีเมล และ รหัสผ่าน");
      }
      else {
        await axios
          .post(this.url, {
            email: user.value,
            password: pass.value,
          })
          .then((res) => {
            if (res.data.statusCode === 200) {
              localStorage.setItem("token", res.data.data.token);
              return this.$router.push("/helioscore");
            }
          })
          .catch((err) => {
            if (err.response.data.message == "Account has not been verified.") {
              return this.$router.push("/helioscore/resendemail");
            } else if (err.response.data.message == "Unauthorized") {
              alert("อีเมล หรือ รหัสผ่าน ไม่ถูกต้อง");
              pass.value = "";
            }
          });
      }
    },
  },
};
</script>


<style scoped>
::placeholder {
  color: #c4c4c4;
}
.background {
  @apply sm:pt-20 md:pt-0 h-screen;
}
img {
  @apply h-auto
  xl:px-20 xl:py-20 
  lg:px-20 lg:py-36
  md:px-12 md:py-28;
}
.box {
  @apply xl:mr-80 xl:my-20
  lg:my-20 lg:mr-28  lg:pb-10
  md:my-16 md:mr-20 md:mx-0 md:rounded-xl md:pb-5
  sm:justify-center sm:mx-40 sm:rounded-lg sm:pb-3;
}
span {
  color: #b3dbfb;
  @apply lg:mr-1 lg:text-base 
  md:text-base
  text-sm;
}
input {
  @apply text-primary w-full px-2;
  font-size: small;
}
</style>
