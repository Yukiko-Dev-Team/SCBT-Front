<template>
  <v-container>
    <v-row>
      <div class="report" v-if="isLoading == false">
        <span><router-link to="./">⬅️ Go back</router-link></span>
        <v-col cols="12">
        <v-card>
          <v-card-title>
            <v-avatar class="mr-4 mb-2"><v-img :src="report.author.avatar"></v-img></v-avatar>
            <span class="text-headline-large align-self-center"
              >{{ report.author.displayName }} <br />
              <span class="code" v-if="report.author.isStaff == true"
                >Staff</span
              ></span
            >
          </v-card-title>
          <v-card-text class="ml-4">
            {{ report.description }} <br />
            File: <span> {{ userStore.isStaff ? report.file ? report.file : report.file || "Not found" : "null" || "Null" }}</span> <br />
            Line: <span> {{ userStore.isStaff ? report.Line ? report.Line : report.Line || "Not found" : "null" || "Null" }}</span> <br />
            <hr class="mt-4" />
            <div class="ticket-status mt-4">
              Status: {{ report.status }} <br />
              Assigned to:
              <v-avatar
                ><v-img :src="report.assignedTo.avatar"></v-img
              ></v-avatar>
              <span>{{ report.assignedTo.displayName }}</span> <br />
              priority: <v-btn variant="outlined" :color="report.priority == 'High' || report.priority == 'critical' ? 'red' : 'blue' ">{{ report.priority }}</v-btn> <br />
              Created at: {{ new Date(report.createdAt).toLocaleString() }} <br />
              Last updated: {{ report.updatedAt == null ? "Never" : new Date(report.updatedAt).toLocaleString() }}
            </div>
          </v-card-text>
          <v-card-actions>
            <v-btn variant="outlined" color="blue" :disabled="!userStore.isStaff" @click="closeReport">Close Report</v-btn> <v-btn variant="outlined" color="red" :disabled="!userStore.isStaff">Remove report</v-btn>
          </v-card-actions>
        </v-card>
        <!-- TODO: Add Move the comments to it's own component -->
      </v-col>
      <v-col cols="12">
        <v-card>
                <v-card-title>
                    Discutions:
                </v-card-title>
                <v-card-text v-if="isSubmitted == false">
                  <v-text-field v-model="form.content" v-if="userStore.isLoggedIn == true">
                  </v-text-field>
                  <v-btn @click="submit" v-if="userStore.isLoggedIn == true">submit</v-btn>
                  <hr />
                </v-card-text>
                <v-card-text v-else>
                  <v-alert type="success">Your comment has been submitted</v-alert>
                  <hr />
                </v-card-text>
                <v-card v-for="comment in report.comments" :key="comment._id" class="mt-4">
                  <v-card-title>
                    <v-avatar class="mr-4 mb-2"><v-img :src="comment.author.avatar"></v-img></v-avatar>
            <span class="text-headline-small align-self-center"
              >{{ comment.author.displayName }}
              <span class="code" v-if="comment.author.isStaff == true"
                >Staff</span
              ></span
            >
                  </v-card-title>
                  <v-card-text>
                    {{ comment.content }}
                  </v-card-text>
              <v-card-actions>
                 <span class="text-title-small">Posted at: {{ formatDate(comment.createdAt) }}</span>
              </v-card-actions>
              <hr />
                </v-card>
            </v-card>
      </v-col>
      </div>
      <v-col cols="12" v-else>
        <h1>Page is loading...</h1>
      </v-col>
    </v-row>
  </v-container>
  <pre style="display: none">{{ userStore }}</pre>
</template>

<script setup>
import { useUserStore } from "@/store/user";
// const userStore = useUserStore();
</script>

<script>
import { useUserStore } from "@/store/user";
const userStore = useUserStore();
import axios from "axios";
export default {
  name: "bug-views",
  data() {
    return {
      report: {},
      isLoading: true,
      form: {},
      isSubmitted: false
    };
  },
  async beforeMount() {
    await this.getReport();
  },
  methods: {
    getReport() {
      axios
        .get(`${import.meta.env.VITE_APP_API}/bug/${this.$route.params.bugId}`)
        .then((res) => {
          this.report = res.data.report[0];
          this.isLoading = false;
        })
        .catch((err) => {
          console.error(err);
        });
    },
    formatDate(date) {
      const options = {
        year: 'numeric',
      month: 'short',
      day: 'numeric',
      hour: 'numeric',
      minute: 'numeric',
      second: 'numeric',
      hour12: false
      };
      return new Intl.DateTimeFormat('en-US', options).format(new Date(date));
    },
    submit() {
      axios.post(`${import.meta.env.VITE_APP_API}/bug/comment/${this.$route.params.bugId}`, {
        content: this.form.content
      }, {
        withCredentials: true
      })
        .then((res) => {
          this.report.comments.push({
            _id: "",
            author: {
              avatar: userStore.avatar,
              displayName: userStore.displayName,
              isStaff: userStore.isStaff,
              userId:  userStore.userId,
              username: userStore.username
            },
            content: this.form.content,
            createdAt: Date.now(),
          });
          this.isSubmitted = true;
          this.comment.content = "";
        })
        .catch((err) => {
          console.log(err);
        })
    },
    closeReport() {
      axios.post(`${import.meta.env.VITE_APP_API}/bug/${this.$route.params.bugId}/close`,{}, {
        withCredentials: true
      })
        .then((res) => {
          console.log(`${userStore.username} (${userStore.userId}) has closed ${this.$route.params.bugId}`);
          console.log(res.data)
        })
        .catch((err) => {
          console.log(err.response.data.message);
        })
    }
  },
};
</script>

<style>
.code {
  background-color: rgb(var(--v-theme-code));
  color: rgb(var(--v-theme-on-code));
  border-radius: 4px;
  line-height: 1.8;
  font-size: 0.5em;
  font-weight: normal;
  padding: 0.2em 0.4em;
  margin-top: 0.5em !important;
}
</style>
