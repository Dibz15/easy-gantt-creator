<template>
  <div class="d-flex flex-row flex-wrap justify-space-around">
    <v-card class="container-gantt">
      <div>
        <ViewModeButton
          @click="changeViewMode"
          handle="Year"
          :viewMode="mode"
          title="Année"
        />
        <ViewModeButton
          @click="changeViewMode"
          handle="Month"
          :viewMode="mode"
          title="Mois"
        />
        <ViewModeButton
          @click="changeViewMode"
          handle="Week"
          :viewMode="mode"
          title="Semaine"
        />
        <ViewModeButton
          @click="changeViewMode"
          handle="Day"
          :viewMode="mode"
          title="Journée"
        />
      </div>
      <Gantt
        ref="gantt"
        :view-mode="mode"
        :tasks="tasks"
        @task-date-updated="updateTaskDate($event)"
        @task-progress-updated="updateTaskProgress($event)"
      />
    </v-card>
    <v-card class="tasks-container" elevation="2">
      <h1 style="color: white">Mes Taches</h1>
      <Task
        v-for="task in tasks"
        :key="task.id"
        :task="task"
        @onTaskChange="(newT) => updateTask(task, newT)"
      />
      <NewTask @onNewTask="(newT) => addTask(newT)" :tasks="tasks" />
    </v-card>
  </div>
</template>

<script lang="ts">
import "../assets/style.css";
import Vue from "vue";
import { Task } from "frappe-gantt";
import moment from "moment";

export default Vue.extend({
  data() {
    const tasks: Task[] = [
      {
        id: "0",
        name: "Hello",
        start: moment().add(-3, "d").format("YYYY-MM-DD"),
        end: moment().add(-2, "d").format("YYYY-MM-DD"),
        progress: 10,
        dependencies: "",
      },
      {
        id: "1",
        name: "World",
        start: moment().format("YYYY-MM-DD"),
        end: moment().add(2, "d").format("YYYY-MM-DD"),
        progress: 20,
        dependencies: "0",
      },
    ];
    return {
      mode: "Day",
      tasks,
    };
  },
  methods: {
    updateTaskDate(event: any) {
      const { task } = event;
      task.end = event.end;
      task.start = event.start;
    },
    updateTaskProgress(event: any) {
      const { task } = event;
      task.progress = event.progress;
    },
    updateTask(oldTask: Task, newTask: Task) {
      console.log("update", oldTask, newTask);
      oldTask.name = newTask.name;
      oldTask.start = newTask.start;
      oldTask.end = newTask.end;
      if (this.$refs.gantt) (this.$refs.gantt as any).updateTasks();
    },
    changeViewMode(mode: string) {
      console.log("change view mode", mode);
      this.mode = mode;
    },
    addTask(task: Task) {
      this.tasks.push(task);
    },
  },
});
</script>