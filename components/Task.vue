<template>
  <v-card elevation="2" class="task-card" @click="onClick">
    <div v-if="edit" class="d-flex flex-row align-center">
      <v-text-field
        label="Name"
        outlined
        color="primary"
        style="color: black"
        :value="newName"
        @input="onInput"
      />
      <v-select
        :value="dependencies"
        :items="tasks"
        item-text="name"
        item-value="id"
        label="Depends on"
        multiple
        outlined
        @input="onDependenciesChange"
      ></v-select>
      <v-btn icon color="green" @click.stop="onEndEdit">
        <v-icon>mdi-checkbox-marked-circle-outline</v-icon>
      </v-btn>
      <v-btn icon color="red" @click.stop="onDelete">
        <v-icon>mdi-delete</v-icon>
      </v-btn>
    </div>
    <h2 v-else>{{ task.name }}</h2>
    <div class="d-flex flex-row justify-space-between">
      <h5>{{ start }} ➜ {{ end }}</h5>
      <h5>{{ task.progress }}%</h5>

      <!-- <v-btn @click="dialog = true" :color="color" rounded icon><v-icon>mdi-palette</v-icon></v-btn>
      <v-dialog v-model="dialog" max-width="500px">
        <v-card>
          <v-card-title>Select a color</v-card-title>
          <v-card-text>
            <v-color-picker
              v-model="color"
              hide-canvas
              hide-inputs
              show-swatches
            ></v-color-picker>
          </v-card-text>
          <v-card-actions>
            <v-btn @click="dialog = false">Close</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog> -->
    </div>
  </v-card>
</template>

<script>
import Vue from "vue";
// import { Task } from "frappe-gantt";
import moment from "moment";

export default Vue.extend({
  props: {
    task: {
      required: true,
    },
    tasks: {
      required: true,
      default: [],
    },
  },
  data() {
    return { edit: false, newName: this.task.name, dependencies: "", color: "", dialog: false };
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
      this.newName = this.task.name;
      this.edit = true;
    },
    onEndEdit() {
      this.$emit("onTaskChange", { 
        ...this.task, 
        name: this.newName, 
        dependencies: this.dependencies,
        custom_class: this.color ? this.newName : ''
      });
      this.edit = false;
    },
    onDelete() {
      this.$emit("onDelete");
    },
    onInput(newName) {
      this.newName = newName;
    },
    onDependenciesChange(dependencies) {
      this.dependencies = dependencies;
    },
  },
  watch: {
    color() {
      console.log(this.color)
      this.onEndEdit()
    }
  }
});
</script>