<template>
  <div>
    <svg ref="gantt"></svg>
  </div>
</template>

<script>
import Gantt from "frappe-gantt";
import moment from "moment";
import Vue from "vue";

export default Vue.extend({
  props: {
    viewMode: {
      type: String,
      required: false,
      default: "Day",
    },
    tasks: {
      type: Array,
      required: true,
      default: [],
    },
  },
  data() {
    return {
      gantt: {},
    };
  },
  watch: {
    viewMode() {
      this.updateViewMode();
    },
    tasks() {
      this.updateTasks();
    },
  },
  mounted() {
    this.setupGanttChart();
  },
  methods: {
    setupGanttChart() {
      this.gantt = new Gantt(this.$refs.gantt, this.tasks, {
        on_date_change: (task, start, end) => {
          this.$emit("task-date-updated", { task, start, end });
          // this.updateTasks();
        },
        on_progress_change: (task, progress) => {
          this.$emit("task-progress-updated", { task, progress });
        },
    //     custom_popup_html: function (task) {
    //       const days = moment(task._end).diff(moment(), "days");
    //       return `
		// <div class="popup-html">
		//   <h1>${task.name}</h1>
    //   ${
    //     days >= 0
    //       ? `<p>Ends in ${days} day${days > 1 ? "s" : ""}</p>`
    //       : `<p>Ended ${Math.abs(days)} day${days < -1 ? "s" : ""} ago</p>`
    //   }

		//   <p>${task.progress}% complete</p>
		// </div>
	  // `;
    //     },
        language: "en",
        view_mode: this.viewMode,
        bar_height: 25,
        arrow_curve: 10,
      });
      this.updateTasks();
      this.updateViewMode();
    },
    updateViewMode() {
      this.gantt.change_view_mode(this.viewMode);
    },
    updateTasks() {
      this.gantt.refresh(this.tasks);
    }
  },
});
</script>