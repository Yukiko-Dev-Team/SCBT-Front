<template>
    <v-container>
        <v-row>
            <v-app-bar>
                <router-link to="/">
                    <v-btn class="ml-4">
                        <span class="text-headline-small ">Stellar Corporation</span>
                    </v-btn>
                </router-link>
                <v-spacer />
                <router-link to="/">
                    <v-btn>Projects</v-btn>
                </router-link>
                <v-spacer />
                <v-btn size="large" v-if="userStore.userId">
                    <v-avatar size="35" class="mr-2">
                            <v-img :src="userStore.avatar"></v-img>
                        </v-avatar>
                    {{ userStore.displayName }}
                </v-btn>
                <v-btn size="large" v-else>
                    <a :href="apiLink">login</a>
                </v-btn>
            </v-app-bar>
        </v-row>
    </v-container>
</template>

<script setup>
import { useUserStore } from '@/store/user';
const userStore = useUserStore();
const apiLink = `${import.meta.env.VITE_APP_API}/auth/login`
</script>