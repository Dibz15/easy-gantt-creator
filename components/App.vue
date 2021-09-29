<template>
  <div class="gantt-container">
    <Gantt
      :view-mode="mode"
      :tasks="tasks"
      @task-date-updated="updateTaskDate($event)"
      @task-progress-updated="updateTaskProgress($event)"
    />
    <button @click="addRandomTask">Add</button>
  </div>
</template>

<script lang="ts">
import "../assets/style.css";
import Vue from "vue";
import { Task } from "frappe-gantt";

export default Vue.extend({
  data() {
    const tasks: Task[] = [
      {
        id: "1",
        name: "Hello",
        start: "2021-04-05",
        end: "2021-04-07",
        progress: 10,
        dependencies: "",
      },
      {
        id: "2",
        name: "World",
        start: "2021-04-10",
        end: "2021-04-13",
        progress: 20,
        dependencies: "1",
      },
    ];
    return {
      mode: "Day",
      tasks,
    };
  },
  methods: {
    addRandomTask() {
      const id = Math.random();
      this.tasks.push({
        id: id.toString(),
        name: id.toString(),
        start: "2021-01-01",
        end: "2021-01-05",
        dependencies: "0",
        progress: Math.random() * 100,
      });
    },
    updateTaskDate(event: any) {
      const { task } = event;
      task.end = event.end;
      task.start = event.start;
    },
    updateTaskProgress(event: any) {
      const { task } = event;
      task.progress = event.progress;
    },
    demoViewMode(viewMode: string) {
      this.mode = viewMode;
    },
  },
});
</script>