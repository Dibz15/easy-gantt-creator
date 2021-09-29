<template>
  <v-card elevation="2" class="task-card">
    <h3>Ajouter une tache</h3>
    <div class="d-flex flex-row align-center">
      <v-text-field
        label="Nom"
        outlined
        color="primary"
        style="color: black"
        :value="name"
        @input="onInput"
      />
      <v-btn icon color="green" @click.stop="onCreate">
        <v-icon>mdi-plus-box-outline</v-icon>
      </v-btn>
    </div>
    <v-select
      :value="dependencies"
      :items="tasks"
      item-text="name"
      item-value="id"
      label="Dépend de"
      multiple
      outlined
      @input="onDependenciesChange"
    ></v-select>
  </v-card>
</template>

<script>
import Vue from "vue";
import { Task } from "frappe-gantt";
import moment from "moment";

export default Vue.extend({
  props: {
    tasks: {
      required: true,
      default: [],
    },
  },
  data() {
    return { name: "", dependencies: "" };
  },

  methods: {
    onCreate() {
      console.log("value", this.dependencies);
      this.$emit("onNewTask", {
        name: this.name,
        start: moment().format("YYYY-MM-DD"),
        end: moment().format("YYYY-MM-DD"),
        progress: 10,
        dependencies: this.dependencies,
      });
      this.name = "";
      this.start = moment();
      this.end = moment();
      this.dependencies = "";
    },

    onInput(newName) {
      this.name = newName;
    },
    onDependenciesChange(dependencies) {
      this.dependencies = dependencies;
    },
  },
});
</script>