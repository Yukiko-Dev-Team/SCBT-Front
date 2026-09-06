<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <v-img height="100px" src="https://cdn.yukiko.app/web/stellar-logo.png"></v-img>
      </v-col>
      <v-col cols="12" class="justify-center text-center">
        <span class="text-display-large">Active Projects</span>
        <projects />
      </v-col>
      <v-col cols="12">
        <v-btn to="/new-project" class="mt-4" v-if="userStore.isLoggedIn">Create a new project</v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import projects from '@/components/projects.vue';
import { useUserStore } from '@/store/user';
const userStore = useUserStore();
</script>

<script>
export default {
  name: "home-page"
}
</script>