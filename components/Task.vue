<template>
  <v-card elevation="2" class="task-card" @click="onClick">
    <div v-if="edit" class="d-flex flex-row align-center">
      <v-text-field
        label="Nom"
        outlined
        color="primary"
        style="color: black"
        :value="newName"
        @input="onInput"
      />
      <v-btn icon color="green" @click.stop="onEndEdit">
        <v-icon>mdi-checkbox-marked-circle-outline</v-icon>
      </v-btn>
    </div>
    <h2 v-else>{{ task.name }}</h2>
    <div class="d-flex flex-row justify-space-between">
      <h5>{{ start }} ➜ {{ end }}</h5>
      <h5>{{ task.progress }}%</h5>
    </div>
  </v-card>
</template>

<script>
import Vue from "vue";
import { Task } from "frappe-gantt";
import moment from "moment";

export default Vue.extend({
  props: {
    task: {
      type: Task,
      required: true,
    },
  },
  data() {
    return { edit: false, newName: this.task.name };
  },
  computed: {
    start() {
      return moment(this.task.start).format("DD-MM-YY");
    },
    end() {
      return moment(this.task.end).format("DD-MM-YY");
    },
  },

  methods: {
    onClick() {
      console.log("click!");
      this.newName = this.task.name;
      this.edit = true;
    },
    onEndEdit() {
      this.$emit("onTaskChange", { ...this.task, name: this.newName });
      this.edit = false;
    },

    onInput(newName) {
      this.newName = newName;
    },
  },
});
</script>