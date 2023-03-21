<template>
  <div class="d-flex flex-row flex-wrap justify-space-around">
    <v-card class="container-gantt" :style="{'min-width': '500px', width: fullWidth}">
      <div>
        <ViewModeButton
          @click="changeViewMode"
          handle="Year"
          :viewMode="mode"
          title="Year"
        />
        <ViewModeButton
          @click="changeViewMode"
          handle="Month"
          :viewMode="mode"
          title="Month"
        />
        <ViewModeButton
          @click="changeViewMode"
          handle="Week"
          :viewMode="mode"
          title="Week"
        />
        <ViewModeButton
          @click="changeViewMode"
          handle="Day"
          :viewMode="mode"
          title="Day"
        />
      </div>
      <Gantt
        ref="gantt"
        :view-mode="mode"
        :tasks="tasks"
        @task-date-updated="updateTaskDate($event)"
        @task-progress-updated="updateTaskProgress($event)"
      />
      <v-btn style="align: right;" icon @click="ganttLarge = !ganttLarge"><v-icon>mdi-fullscreen</v-icon></v-btn>
    </v-card>
    <v-card elevation="2" style="min-width: 500px; width: 40%; max-height: 900px;">
      <NewTask @onNewTask="addTask" :tasks="tasks" />
      <v-card class="tasks-container" elevation="2">
        <h1 style="color: white">Your tasks</h1>
        <v-card-text>
          <label>
            <v-btn
              @click="saveTasks"
            >
              Save Tasks...
            </v-btn>
          </label>
          <label>
            <v-btn
              @click="loadTasks"
            >
              Load Tasks...
            </v-btn>
            <input @change="onLoadFileChange" type="file" ref="loadfile" accept=".json" hidden/>
          </label>
        </v-card-text>
        <div>&nbsp;</div>
        <div>
          <Task
            v-for="task in tasks"
            :key="task.id"
            :task="task"
            :tasks="tasks"
            @onTaskChange="(newT) => updateTask(task, newT)"
            @onDelete="deleteTask(task)"
          />
        </div>
      </v-card>
    </v-card>
  </div>
</template>

<script lang="ts">
import "../assets/style.css";
import Vue from "vue";
import moment from "moment";
import { saveAs } from 'file-saver';

export default Vue.extend({
  data() {
    const defaultTask = {
      name: "Default task",
      start: moment().format("YYYY-MM-DD"),
      end: moment().add(7, "d").format("YYYY-MM-DD"),
      progress: 100,
      dependencies: "",
    }
    const tasks = [ defaultTask ];
    return {
      mode: "Day",
      tasks,
      defaultTask,
      fileData: "",
      ganttLarge: false,
    };
  },
  computed: {
    fullWidth() {
      return ((this as any).ganttLarge) ? "95%" : "50%"
    }
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
    updateTask(oldTask: any, newTask: any) {
      oldTask.name = newTask.name;
      oldTask.start = newTask.start;
      oldTask.end = newTask.end;
      oldTask.dependencies = newTask.dependencies;
      if (this.$refs.gantt) (this.$refs.gantt as any).updateTasks();
    },
    deleteTask(task : any) {
      const index = this.tasks.indexOf(task)
      if (index > -1) { // only splice array when item is found
        this.tasks.splice(index, 1); // 2nd parameter means remove one item only
      }
      if (this.tasks.length === 0) this.addTask(this.defaultTask);
    },
    changeViewMode(mode: string) {
      this.mode = mode;
    },
    addTask(task: any) {
      // if (task.dependencies) {
      //   console.log(this.tasks)
      //   const dependencies = this.tasks.filter(x => task.dependencies.includes(x._id))
      //   console.log(dependencies)
      // }
      this.tasks.push(task);
    },
    saveTasks(event : any) {
      let blob = new Blob([JSON.stringify(this.tasks, null, 2)], {type : 'application/json'});

      // Save blob as a file named "data.json"
      saveAs(blob, `gantt-save${moment().format("YYYY-MM-DD_hh-mm-ss")}.json`);
    },
    loadTasks() {
      const loadInput = (this.$refs.loadfile as any);
      loadInput.click();
    },
    onLoadFileChange(event : any) {
      const file = event.target.files ? event.target.files[0] : null;
      if (file) {
        const reader = new FileReader();

        reader.onload = (e) => {
          /* Parse data */
          // console.log(e.target);
          if (e.target) {
            const bstr = e.target.result; 
            // Assuming data is text...
            this.tasks = JSON.parse(bstr as string)
            if (this.tasks.length === 0) this.addTask(this.defaultTask);
          }
        }
        reader.readAsBinaryString(file);
      }
    }
  },
});
</script>