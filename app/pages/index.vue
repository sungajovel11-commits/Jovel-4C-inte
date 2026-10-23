<template>
  <div class="mx-auto d-flex justify-center align-center" style="height: 95vh;">
 



    <v-card v-if="user" rounded="xl" width="450" class="pa-5 bg-card text-center" elevation="0">
      <v-card-text>
        <v-avatar size="120">
          <v-img :src="user.picture" :lazy-src="user.picture" alt="Photo" />
        </v-avatar>
      </v-card-text>
      <v-chip v-if="user.email_verified === true" append-icon="mdi-check-circle" class="ma-2" color="success">
        Verified
      </v-chip>
      <v-card-title>{{ user.name }}</v-card-title>
      <v-card-subtitle>{{ user.email }}</v-card-subtitle>
      <v-divider class="my-3"></v-divider>
      <v-card-actions >
        <v-btn block color="red" prepend-icon="mdi-logout" variant="flat" rounded="xl" @click="logout">
          Logout
        </v-btn>
      </v-card-actions>
    </v-card>




  </div>

</template>

<script lang="ts" setup>
//@ts-nocheck
const user = ref<any>(null)

const logout = () => {
  localStorage.removeItem('google_user')
  localStorage.removeItem('google_token')
  navigateTo('/login')
}


onMounted(() => {
  const savedUser = localStorage.getItem('google_user')
  if (savedUser) {
    user.value = JSON.parse(savedUser)
  }
})




</script>

<style scoped>
.bg-card {
  background: #FFFFFF;
  filter: drop-shadow(-1px 0px 40px #BDBDBD);
}
</style>