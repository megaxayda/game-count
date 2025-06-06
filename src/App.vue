<template>
  <v-app>
    <v-app-bar color="primary" prominent dark>
      <v-app-bar-nav-icon></v-app-bar-nav-icon>
      <v-toolbar-title>Player Counter App</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn icon @click="toggleTheme">
        <v-icon>{{ themeIcon }}</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <v-container fluid>
        <PlayerManager />
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref, computed } from "vue";
import { useTheme } from "vuetify";
import PlayerManager from "./components/PlayerManager.vue";

const theme = useTheme();

const toggleTheme = () => {
  theme.global.name.value = theme.global.current.value.dark ? "light" : "dark";
};

const themeIcon = computed(() => {
  return theme.global.current.value.dark
    ? "mdi-brightness-7"
    : "mdi-brightness-4";
});
</script>

<style>
/* Global styles */
.v-application {
  font-family: "Roboto", sans-serif !important;
}
</style>
