<template>
  <v-app class="bg-ui">
    <v-navigation-drawer v-model="drawer" :rail="rail"  color="primary" permanent
      @click="rail = false">
      <v-list>
        <v-list-item :prepend-avatar="user?.picture" :title="user?.name">
          <template v-slot:prepend>
            <v-avatar ></v-avatar>
          </template>
          <template v-slot:append>
            <v-btn :inert="rail" icon="mdi-chevron-left" variant="text" @click.stop="rail = !rail"></v-btn>
          </template>
        </v-list-item>
      </v-list>

      <v-divider></v-divider>

      <v-list density="compact" nav>
        <v-list-item v-for="item in items" :key="item.value" :class="{ 'pl-5': wider }" :prepend-icon="item.icon"
          :title="item.title" :to="item.to"></v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-main>
      <slot />
    </v-main>

  </v-app>
</template>

<script lang="ts" setup>
//@ts-nocheck
const drawer = ref(true)
const rail = ref(false)

const items = [
  { icon: 'mdi-view-dashboard', title: 'Dashboard', to:'/' },
  { icon: 'mdi-qrcode', title: 'QR Scanner', to: '/qr-scanner' },
  { icon: 'mdi-weather-lightning-rainy', title: 'Weather App', to: '/weather' },
]

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

<style>
.bg-ui {
  background: #E0E0E0;
}
</style>