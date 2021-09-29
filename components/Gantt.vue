<template>
  <div>
    <svg ref="gantt" />
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
          this.updateTasks();
        },
        on_progress_change: (task, progress) => {
          this.$emit("task-progress-updated", { task, progress });
        },
        custom_popup_html: function (task) {
          const days = moment(task._end).diff(moment(), "days");
          return `
		<div class="popup-html">
		  <h1>${task.name}</h1>
      ${
        days >= 0
          ? `<p>Fin dans ${days} jour${days > 1 ? "s" : ""}</p>`
          : `<p>Fin il y à ${Math.abs(days)} jour${days < -1 ? "s" : ""}</p>`
      }

		  <p>${task.progress}% complété!</p>
		</div>
	  `;
        },
        language: "fr",
        view_mode: this.viewMode,
      });
      this.updateTasks();
      this.updateViewMode();
    },
    updateViewMode() {
      this.gantt.change_view_mode(this.viewMode);
    },
    updateTasks() {
      this.gantt.refresh(this.tasks);
    },
  },
});
</script>