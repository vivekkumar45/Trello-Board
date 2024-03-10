<template>
  <div class="board">
    <div class="column"
         v-for="(status, index) in statuses"
         :key="index"
         @dragover.prevent
         @drop="handleDrop(index)">
      <h2>{{ status }}</h2>
      <button @click="addTask(index)">New</button>
      <div class="cards"
           @dragstart="handleDragStart(taskIndex, index)"
           @dragend="handleDragEnd"
           draggable="true"
           v-for="(task, taskIndex) in filteredTasks(index)"
           :key="taskIndex">
        <div class="card">
          <div class="card-content"
               @click="openTask(taskIndex)">
            <h3>{{ task.title }}</h3>
            <p>{{ task.description }}</p>
          </div>
        </div>
      </div>
      <p>Count: {{ getCount(index) }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tasks: [],
      editedTask: {
        title: '',
        description: '',
        status: 0
      },
      selectedTask: null,
      statuses: ['To Do', 'In Progress', 'Done']
    };
  },
  mounted() {
    this.fetchTasks();
  },
  methods: {
    fetchTasks() {
      // Fetch tasks from local storage or API
      // For simplicity, let's use a dummy array
      const storedTasks = localStorage.getItem('tasks');
      if (storedTasks) {
        this.tasks = JSON.parse(storedTasks);
      } else {
        // Dummy tasks for demonstration
        this.tasks = [
          { title: 'Task 1', description: 'Description 1', status: 0 },
          { title: 'Task 2', description: 'Description 2', status: 1 },
          { title: 'Task 3', description: 'Description 3', status: 2 }
        ];
      }
    },
    addTask(status) {
      this.tasks.push({
        title: 'New Task',
        description: '',
        status: status
      });
      this.saveTasks();
    },
    openTask(index) {
      this.selectedTask = index;
      this.editedTask = { ...this.tasks[index] };
    },
    closeTask() {
      this.selectedTask = null;
    },
    saveTask() {
      this.tasks.splice(this.selectedTask, 1, { ...this.editedTask });
      this.selectedTask = null;
      this.saveTasks();
    },
    deleteTask() {
      if (confirm('Are you sure you want to delete this task?')) {
        this.tasks.splice(this.selectedTask, 1);
        this.selectedTask = null;
        this.saveTasks();
      }
    },
    handleDragStart(taskIndex, fromStatus) {
      this.draggedTask = { taskIndex, fromStatus };
    },
    handleDragEnd() {
      this.draggedTask = null;
    },
    handleDrop(toStatus) {
      if (this.draggedTask) {
        const { taskIndex, fromStatus } = this.draggedTask;
        this.tasks[taskIndex].status = toStatus;
        this.saveTasks();
      }
    },
    getCount(statusIndex) {
      return this.tasks.filter(task => task.status === statusIndex).length;
    },
    filteredTasks(statusIndex) {
      return this.tasks.filter(task => task.status === statusIndex);
    },
    saveTasks() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    }
  }
};
</script>

<style scoped>
.board {
  display: flex;
  justify-content: space-around;
  padding: 20px;
}

.column {
  flex: 1;
  margin: 0 10px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f9f9f9;
}

.column h2 {
  margin-bottom: 10px;
}

.cards {
  margin-top: 10px;
}

.card {
  background-color: #fff;
  border: 1px solid #ddd;
  border-radius: 5px;
  padding: 10px;
  margin-bottom: 10px;
  cursor: pointer;
}

.card-content {
  cursor: pointer;
}

</style>
