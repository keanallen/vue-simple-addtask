<template>
  <div class="container">
    <Header
      @toggle-task-form="toggleForm"
      title="Task Tracker"
      :showAddTask="showAddTask"
    />
    <AddTask v-show="showAddTask" @add-task="addTask" />
    <Tasks
      @toggle-reminder="toggleReminder"
      @delete-task="deleteTask"
      :tasks="tasks"
    />
  </div>
</template>

<script>
import Parse from "parse";
import Header from "./components/Header.vue";
import Tasks from "./components/Tasks.vue";
import AddTask from "./components/AddTask.vue";

Parse.serverURL = "http://localhost:1337/parse";
Parse.initialize(
  "Nr16b7CypPzNnSLzdLg2pv1HwM5AKn6v",
  "VmYq3t6w9y$B&E)H@McQfTjWnZr4u7x!"
);

export default {
  name: "App",
  components: { Header, Tasks, AddTask },
  data() {
    return {
      tasks: [],
      showAddTask: false,
    };
  },
  methods: {
    toggleForm() {
      this.showAddTask = !this.showAddTask;
    },
    async addTask(task) {
      const Tasks = Parse.Object.extend("Tasks");
      const tasks = new Tasks();

      tasks.set("text", task.text);
      tasks.set("day", task.day);
      tasks.set("reminder", task.reminder);

      const result = await tasks.save();

      this.tasks = [...this.tasks, result.toJSON()];
    },
    async deleteTask(id) {
      if (confirm("Are you sure?")) {
        const Tasks = Parse.Object.extend("Tasks");
        const query = new Parse.Query(Tasks);
        query.equalTo("objectId", id);
        const result = await query.find();
        for (var i = 0; i < result.length; i++) {
          result[i].destroy();
          this.tasks = this.tasks.filter((task) => task.objectId != id);
        }
      }
    },
    async toggleReminder(id) {
      this.tasks = this.tasks.map((task) =>
        task.objectId == id ? { ...task, reminder: !task.reminder } : task
      );

      const currentTaskIndex = this.tasks.findIndex((x) => x.objectId === id);

      try {
        const Tasks = Parse.Object.extend("Tasks");
        const query = new Parse.Query(Tasks);
        query.equalTo("objectId", id);
        const results = await query.find();
        for (var i = 0; i < results.length; i++) {
          results[i].set("reminder", this.tasks[currentTaskIndex].reminder);
          results[i].save();
        }
      } catch (error) {
        console.error("YOU HAVE AN ERROR: ", error);
      }
    },
    async fetchTask() {
      try {
        const Tasks = Parse.Object.extend("Tasks");
        const query = new Parse.Query(Tasks);
        const response = await query.find();
        this.tasks = [];
        for (var i = 0; i < response.length; i++) {
          this.tasks.push(response[i].toJSON());
        }
        console.log(this.tasks);
        return this.tasks;
      } catch (error) {
        console.error("YOU HAVE AN ERROR: ", error);
      }
    },
  },
  async created() {
    this.tasks = await this.fetchTask();
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap");
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
body {
  font-family: "Poppins", sans-serif;
}
.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}
.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}
.btn:focus {
  outline: none;
}
.btn:active {
  transform: scale(0.98);
}
.btn-block {
  display: block;
  width: 100%;
}
</style>
