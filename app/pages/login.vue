<template>
  <!-- <div>
    <v-container class="fill-height d-flex align-center justify-center">
      <v-card width="400" class="pa-6 rounded-xl">
        <v-card-title class="text-center text-h5">
          Login
        </v-card-title>
        <v-card-text>
          <v-btn block color="primary" size="large" prepend-icon="mdi-google" @click="loginWithGoogle">
            Sign in with Google
          </v-btn>
        </v-card-text>
      </v-card>
    </v-container>
  </div> -->
  <div class="mx-auto d-flex align-center justify-center" style="height: 100vh;">
    <v-card width="500" class="py-8 bg-card" rounded="xl" elevation="0">
      <v-card-text class="text-center">
        <v-icon size="100" color="primary">mdi-account</v-icon>
        <h3 class="text-black">Welcome back, please login</h3>
        <v-form class="px-8">
          <v-text-field prepend-inner-icon="mdi-account" variant="solo-filled" flat label="Username"
            rounded></v-text-field>
          <v-text-field prepend-inner-icon="mdi-lock" variant="solo-filled" flat label="Password" rounded
            type="password"></v-text-field>
          <v-btn color="primary" class="mt-3" rounded block>Login</v-btn>
          <v-divider class="my-8">OR</v-divider>
          <v-btn prepend-icon="mdi-google" @click="loginWithGoogle" rounded variant="flat" color="red" block>Signin with google</v-btn>
        </v-form>


      </v-card-text>

    </v-card>
  </div>
</template>

<script lang="ts" setup>
// @ts-nocheck
definePageMeta({
  layout: false
})
const config = useRuntimeConfig()
declare global {
  interface Window {
    google: any
  }
}
const loginWithGoogle = () => {
  const client = window.google.accounts.oauth2.initTokenClient({
    client_id: config.public.googleClientId,
    scope: 'openid email profile',
    callback: async (response: any) => {
      const userInfo = await $fetch(
        'https://www.googleapis.com/oauth2/v3/userinfo',
        {
          headers: {
            Authorization: `Bearer ${response.access_token}`
          }
        }
      )
      localStorage.setItem(
        'google_user',
        JSON.stringify(userInfo)
      )
      localStorage.setItem(
        'google_token',
        response.access_token
      )
      navigateTo('/')
    }
  })
  client.requestAccessToken()
}



</script>

<style scoped>
.bg-card {
  background: #FFFFFF;
  filter: drop-shadow(-1px 0px 40px #BDBDBD);
}
</style>