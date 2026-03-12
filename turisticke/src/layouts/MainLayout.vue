<template>
  <q-layout view="lHh Lpr lFf">

    <!-- HEADER -->
    <q-header elevated class="bg-primary text-white">
      <q-toolbar>

        <q-btn
          flat
          dense
          round
          icon="menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title class="row items-center">
          <q-icon name="travel_explore" size="28px" class="q-mr-sm" />
          <span class="text-weight-bold">
            Turističke atrakcije
          </span>
        </q-toolbar-title>

        <!-- USER INFO -->
        <div v-if="tokenExists" class="row items-center q-gutter-sm">
          <q-icon name="person"/>
          <span>{{ userRole }}</span>

          <q-btn
            flat
            dense
            icon="logout"
            label="Odjava"
            @click="clearLocalStorage"
          />
        </div>

      </q-toolbar>
    </q-header>

    <!-- DRAWER -->
    <q-drawer
      v-model="leftDrawerOpen"
      bordered
      show-if-above
      class="bg-grey-2"
    >

      <q-list>

        <q-item-label header class="text-grey-8">
          Navigacija
        </q-item-label>

        <EssentialLink
          v-for="link in essentialLinks"
          :key="link.title"
          v-bind="link"
        />

      </q-list>

    </q-drawer>

    <!-- PAGE CONTENT -->
    <q-page-container class="bg-grey-1">
      <router-view />
    </q-page-container>

  </q-layout>
</template>

<script>
import { defineComponent, ref, onMounted, computed } from "vue"
import EssentialLink from "components/EssentialLink.vue"
import { jwtDecode } from "jwt-decode"

const linksList = [
  {
    title: "Prijava",
    icon: "login",
    link: "/auth",
    requiresAuth: false,
    hideOnAuth: true,
  },
  {
    title: "Sve atrakcije",
    icon: "place",
    link: "/",
    requiresAuth: false,
    hideOnAuth: false,
  },
  {
    title: "Moje atrakcije",
    icon: "favorite",
    link: "/index",
    requiresAuth: true,
    hideOnAuth: false,
  },
  {
    title: "Unos atrakcija",
    icon: "add_location",
    link: "/unos",
    requiresAuth: true,
    hideOnAuth: false,
  },
]

export default defineComponent({
  name: "MainLayout",

  components: {
    EssentialLink
  },

  setup() {

    const leftDrawerOpen = ref(false)
    const userRole = ref("")
    const tokenExists = ref(false)

    function toggleLeftDrawer() {
      leftDrawerOpen.value = !leftDrawerOpen.value
    }

    function clearLocalStorage() {
      localStorage.removeItem("token")
      window.location.href = "/"
    }

    function getUserRole() {

      const token = localStorage.getItem("token")

      tokenExists.value = !!token

      if (!token) return

      try {
        const decoded = jwtDecode(token)
        userRole.value = decoded.uloga
      }
      catch (err) {
        console.error(err)
      }

    }

    const filteredLinks = computed(() => {
      return linksList.filter(link => {
        if (link.requiresAuth && !tokenExists.value) return false
        if (link.hideOnAuth && tokenExists.value) return false
        return true
      })
    })

    onMounted(getUserRole)

    return {
      essentialLinks: filteredLinks,
      leftDrawerOpen,
      toggleLeftDrawer,
      clearLocalStorage,
      userRole,
      tokenExists
    }

  }
})
</script>