<template>
  <v-container>
    <v-row>
      <v-col cols="3" v-if="alert.status">
        <v-alert :type='alert.status'>{{ alert.message }}</v-alert>
      </v-col>
      <v-col cols="12">
        <span class="text-display-medium">Create a new project!!!</span>
      </v-col>
      <v-col>
        <v-form>
          <v-text-field
            v-model="project.name"
            label="Project Name"
            required
          ></v-text-field>
          <v-text-field
            v-model="project.description"
            label="Project's description"
          ></v-text-field>
          <v-text-field
            v-model="project.projectId"
            label="Project Id Eg: 'YB'"
            required
          ></v-text-field>
          <v-select
            v-model="project.assignee"
            label="assignee"
            :items="selectItems"
          >
          </v-select>
          <v-btn color="success" class="mr-4" @click="createProject"
            >Create</v-btn
          >
          <v-btn color="warning">Reset</v-btn>
        </v-form>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      project: {
        name: "",
        description: "",
        projectId: "",
        assignee: "",
      },
      alert: {
        status: "",
        message: "",
      },
      selectItems: [],
    };
  },
  methods: {
    createProject() {
      if (!this.project.name) {
        (this.alert.status = "error"),
          (this.alert.message = "You are missing the project name!");
      } else {
        axios
          .post(
            `${import.meta.env.VITE_APP_API}/project/`,
            {
              name: this.project.name,
              description: this.project.description,
              projectId: this.project.projectId,
              assignee: this.project.assignee,
            },
            {
              withCredentials: true,
            }
          )
          .then((res) => {
            console.log(res.data);
            this.alert = {
              status: "success",
              message: res.data.message
            };
            // Here the script gonna have to wait for like 5 seconds and redirect to home page or smth.
            // @Kyoko please Todo?
          });
      }
    },
    getStaffUsers() {
      axios.get(`${import.meta.env.VITE_APP_API}/project/staff`).then((res) => {
        const users = res.data.staff;
        users.forEach((u) => {
          this.selectItems.push({
            title: u.displayName,
            value: u._id,
            avatar: u.avatar,
          });
        });
      });
    },
  },
  mounted() {
    this.getStaffUsers();
  },
};
</script>
