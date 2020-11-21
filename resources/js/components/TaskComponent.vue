<template>
  <div class="container">
    <div class="form-inline my-2 my-lg-0">
      <input
        class="form-control mr-sm-2"
        type="text"
        placeholder="Rechercher une tâche..."
        aria-label="Search"
        @keyup="searchTask"
        v-model="q"
      />
    </div>
    <add-task @task-added="refresh"></add-task>
    <ul class="list-group">
      <li
        class="list-group-item d-flex justify-content-between align-items-center"
        v-for="task in tasks.data"
        :key="task.id"
      >
        <a href="#">{{ task.name }}</a>
        <div>
          <button
            type="button"
            class="btn btn-secondary"
            data-toggle="modal"
            data-target="#editModal"
            @click="getTask(task.id)"
          >
            Éditer
          </button>
          <button
            type="button"
            class="btn btn-danger"
            @click="deleteTask(task.id)"
          >
            Supprimer
          </button>
        </div>
      </li>
      <edit-task
        v-bind:taskToEdit="taskToEdit"
        @task-updated="refresh"
      ></edit-task>
    </ul>
    <pagination
      :data="tasks"
      @pagination-change-page="getResults"
      class="mt-5"
    ></pagination>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tasks: {},
      taskToEdit: "",
      q: "",
    };
  },

  created() {
    axios
      .get("http://vuejs.test/tasksList")
      .then((response) => (this.tasks = response.data))
      .catch((error) => console.log(error));
  },

  methods: {
    // Our method to GET results from a Laravel endpoint
    getResults(page = 1) {
      axios.get("http://vuejs.test/tasksList?page=" + page).then((response) => {
        this.tasks = response.data;
      });
    },

    getTask(id) {
      axios
        .get("http://vuejs.test/tasks/edit/" + id)
        .then((response) => (this.taskToEdit = response.data))
        .catch((error) => console.log(error));
    },

    deleteTask(id) {
      axios
        .delete("http://vuejs.test/tasks/" + id)
        .then((response) => (this.tasks = response.data))
        .catch((error) => console.log(error));
    },

    searchTask() {
      if (this.q.length > 3) {
        axios
          .get("http://vuejs.test/tasksList/" + this.q)
          .then((response) => (this.tasks = response.data))
          .catch((error) => console.log(error));
      } else {
        axios
          .get("http://vuejs.test/tasksList")
          .then((response) => (this.tasks = response.data))
          .catch((error) => console.log(error));
      }
    },

    refresh(tasks) {
      this.tasks = tasks.data;
    },
  },

  mounted() {
    console.log("Component mounted.");
  },
};
</script>
