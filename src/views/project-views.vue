<template>
    <v-container>
        <v-row>
            <v-col cols="12">
                <router-link :to="`/project/${project.projectId}/bug/new`" v-if="bugs => 1 && user.isLoggedIn == true">
                    <v-btn :disabled="user.isStaff == false">Report a new bug</v-btn>
                </router-link>
            </v-col>
            <v-col cols="12">
                <v-card class="mx-auto">
                <v-img src="https://cdn.yukiko.app/web/HomeYukiko.webp" height="200px" cover></v-img>
                <v-card-title>{{ project.projectId }} - {{ project.name }}</v-card-title>
                <v-card-text>
                    This is some random text that will be used as project description at some point. <br />
                    maybe some day who know. I don't. <br />
                    {{ project.description }}
                </v-card-text>
            </v-card>
            </v-col>
            <v-col cols="12" v-if="bugs <= 0">
                <span class="text-display-large">No bugs to report!</span> <br />
                <span class="text-headline-large">Good Job!</span>
                <br />
                <router-link :to="`/project/${project.projectId}/bug/new`">
                    <v-btn>Report a new bug</v-btn>
                </router-link>
            </v-col>
            <v-col cols="8" v-for="bug in bugs" :key="bug.bugId" >
                <bugComp :bug="bug" v-if="bug.status !== 'close'"/>
            </v-col>
        </v-row>
    </v-container>
</template>

<script>
import axios from 'axios';
import bugComp from '@/components/bugs.vue';
import { useUserStore } from '@/store/user';

export default {
    name: "project-view",
    data() {
        return {
            project: {},
            bugs: [],
            user: [],
        }
    },
    components: {
        bugComp
    },
    methods: {
        getUser() {
            const userStore = useUserStore();
            this.user = userStore;
        },
        getProjectInfo() {
            axios.get(`${import.meta.env.VITE_APP_API}/project/project/${this.$route.params.projectId}`, {
                withCredentials: true
            })
            .then((res) => {
                this.project = res.data.project;
                this.bugs = res.data.project.bugs;
            })
            .catch((err) => {
                console.log(err.response.data)
            })
        }
    },
    created() {
        this.getProjectInfo();
    }
}
</script>
