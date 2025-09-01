<template>
  <v-app>
    <!-- Navbar -->
    <v-app-bar app color="indigo darken-3" dark height="75">
      <v-toolbar-title class="font-weight-bold"> Dashboard</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn color="warning" class="ms-2" @click="logout"> Logout </v-btn>
    </v-app-bar>

    <!-- Sidebar -->
    <client-only>
      <v-navigation-drawer
        app
        permanent
        color="indigo lighten-5"
        elevation="2"
        width="220"
        :mobile-break-point="0"
      >
        <v-list nav dense>
          <v-list-item
            link
            class="drawer-item"
            @click="currentView = 'showdata'"
          >
            <v-list-item-title>Show Data</v-list-item-title>
          </v-list-item>
          <v-list-item link class="drawer-item" @click="currentView = 'iot'">
            <v-list-item-title>IoT</v-list-item-title>
          </v-list-item>

          <v-list-item
            link
            class="drawer-item"
            @click="currentView = 'settings'"
          >
            <v-list-item-title>Settings</v-list-item-title>
          </v-list-item>
        </v-list>
      </v-navigation-drawer>
    </client-only>

    <!-- Main Content -->
    <v-main class="main-container">
      <v-container fluid class="pa-4 fill-height">
        <component :is="currentComponent"></component>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import { useRouter } from "vue-router";

// import component
// import ShowData from "@/components/ShowData.vue";
import IoT from "@/components/IoT.vue";
import Settings from "@/components/Settings.vue";

const router = useRouter();
const currentView = ref("showdata");

// ตรวจสอบ token
onMounted(() => {
  const token = localStorage.getItem("token");
  if (!token) {
    router.push("/login");
  }
});

// Logout
function logout() {
  localStorage.removeItem("token");
  router.push("/login");
}

// Dynamic component
const currentComponent = computed(() => {
  switch (currentView.value) {
    case "showdata":
      return ShowData;
    case "settings":
      return Settings;
    case "iot":
      return IoT;
    default:
      return ShowData;
  }
});
</script>

<style>
html,
body,
#app {
  height: 100%;
  margin: 0;
  font-family: "Roboto", sans-serif;
}

.v-application {
  min-height: 100vh;
  background-color: #f4f6fc;
}

.main-container {
  min-height: calc(100vh - 64px);
}

.fill-height {
  height: 100%;
}
.d-flex {
  display: flex !important;
}

.dashboard-card {
  border-radius: 16px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.08);
  transition:
    transform 0.2s,
    box-shadow 0.2s;
  background: linear-gradient(145deg, #ffffff, #f0f2f7);
  flex-grow: 1;
}
.dashboard-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 12px 28px rgba(0, 0, 0, 0.15);
}

.drawer-item {
  border-radius: 12px;
  transition: background 0.2s;
  cursor: pointer;
}
.drawer-item:hover {
  background-color: rgba(63, 81, 181, 0.1);
}
</style>
