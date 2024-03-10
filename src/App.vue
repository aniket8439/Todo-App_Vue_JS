<!-- for project set please see readme.md file of this assignment -->

<template>
  <div class="container">
    <h1>
      <span class="T">T</span>
      <span class="O">O</span>
      <span class="D">D</span>
      <span class="O2">O</span> -
      <span class="A"> A</span>
      <span class="P">P</span>
      <span class="P2">P</span>
    </h1>

    <div class="add-task">
      <input type="text" id="newTask" placeholder="Enter task here" v-model="newTask" />
      <button @click="addTask">Add Task</button>
    </div>

    <div class="tasks-container">
    <div class="task-list">
      <h2>Open Tasks:</h2>
      <ul>
        <li v-for="task in openTasks" :key="task.id">
          <input type="checkbox" v-model="task.done" @change="updateTaskStatus(task)" />
          <span> {{ task.text }}</span> (Created at: {{ formatDate(task.createdAt) }})
          <button @click="editTask(task)">
            <i class="fas fa-edit"></i>
          </button>
        </li>
      </ul>
    </div>

    <div class="task-list">
      <h2>Done Tasks:</h2>
      <ul>
        <li v-for="task in doneTasks" :key="task.id">
          <input type="checkbox" v-model="task.done" @change="updateTaskStatus(task)" />
          <span class="done-task">{{ task.text }}</span> (Created at: {{ formatDate(task.createdAt) }})
          <button @click="deleteTask(task.id)">
            <i class="fas fa-trash"></i>
          </button>
        </li>
      </ul>
    </div>
  </div>

  <div v-if="editedTask !== null" class="edit-task-modal">
    <div class="modal-content">
      <label for="editedTaskText">Edit here:</label>
      <input type="text" id="editedTaskText" v-model="editedTask.text" />
      <button @click="saveEditedTask">Save</button>
      <button @click="cancelEdit">Cancel</button>
    </div>
  </div>
  </div>
</template>
<script>
import axios from 'axios';

export default {
  data() {
    return {
      newTask: '',
      tasks: [],
      editedTask: null,
    };
  },
  computed: {
    openTasks() {
      return this.tasks.filter(task => !task.done);
    },
    doneTasks() {
      return this.tasks.filter(task => task.done);
    },
  },
  methods: {
    
    async fetchTasks() {
      try {
        const response = await axios.get('http://localhost:3000/tasks');
        this.tasks = response.data;
      } catch (error) {
        console.error('Error fetching tasks:', error);
      }
    },
    async addTask() {
      if (this.newTask.trim() === '') return;

      const newTask = {
        text: this.newTask,
        done: false,
        createdAt: new Date().toISOString(),
      };

      try {
        const response = await axios.post('http://localhost:3000/tasks', newTask);
        this.tasks.push(response.data);
        this.newTask = '';
      } catch (error) {
        console.error('Error adding task:', error);
      }
    },
    async updateTaskStatus(task) {
      try {
        await axios.put(`http://localhost:3000/tasks/${task.id}`, task);
      } catch (error) {
        console.error('Error updating task status:', error);
      }
    },
    editTask(task) {
      this.editedTask = { ...task }; 
    },
    saveEditedTask() {
      if (this.editedTask.text.trim() === '') {
        return;
      }

      axios
        .put(`http://localhost:3000/tasks/${this.editedTask.id}`, this.editedTask)
        .then(() => {
          this.fetchTasks(); 
          this.editedTask = null; 
        })
        .catch((error) => {
          console.error('Error updating task:', error);
        });
    },
    cancelEdit() {
      this.editedTask = null;
    },
    deleteTask(taskId) {
      axios
        .delete(`http://localhost:3000/tasks/${taskId}`)
        .then(() => {
          this.fetchTasks(); 
        })
        .catch((error) => {
          console.error('Error deleting task:', error);
        });
    },
    formatDate(dateString) {
      const options = { year: 'numeric', month: 'long', day: 'numeric' };
      return new Date(dateString).toLocaleDateString(undefined, options);
    },
  },
  mounted() {
    this.fetchTasks();
    window.addEventListener('beforeunload', this.saveTasks);
    const fontAwesomeLink = document.createElement('link');
    fontAwesomeLink.rel = 'stylesheet';
    fontAwesomeLink.href = 'https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css';
    document.head.appendChild(fontAwesomeLink);
  },
};
</script>



<style>

html {
  height: 100%;
}

body {
  font-family: 'Arial', sans-serif;
  margin: 0;
  padding: 0;
  background: linear-gradient(to bottom right, #3498db, #1abc9c);
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}


.container {
  max-width: 800px;
  padding: 20px;
  border-radius: 8px;
  background-color: whitesmoke;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  overflow-y: auto; 
  max-height: 100vh;
  margin: 0 auto;
}
h1{
  text-align: center;
}
.T{
  background-color: red;
  padding: 8px;
  border-radius: 8px;
  color: white;
  margin: 3px;
}
.O{
  background-color: yellow;
  padding: 8px;
  border-radius: 8px;
  color: black;
  margin: 3px;
}
.D{
  background-color: purple;
  padding: 8px;
  border-radius: 8px;
  color: white;
  margin: 3px;
}
.O2{
  background-color: aqua;
  padding: 8px;
  border-radius: 8px;
  color: black;
  margin: 3px;
}
.A{
  background-color: blue;
  padding: 8px;
  border-radius: 8px;
  color: white;
  margin: 3px;
}
.P{
  background-color: yellowgreen;
  padding: 8px;
  border-radius: 8px;
  color: black;
  margin: 3px;
}
.P2{
  background-color: orange;
  padding: 8px;
  border-radius: 8px;
  color: white;
  margin: 3px;
}

.add-task {
  margin-bottom: 20px;
  text-align: center;
}
input{
  border-radius: 8px;
  text-decoration: none;
  border: none;
  height: 30px;
  text-align: center;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

button {
  padding: 8px 12px;
  background-color: #4caf50;
  color: white;
  cursor: pointer;
  border-radius: 8px;
  margin-left: 10px;
  border: none;
}

button:hover {
  background-color: #45a049;
}

.tasks-container {
  display: flex;
  justify-content: space-between;
}

.task-list {
  flex: 1;
  background-color: #fff;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  margin: 10px;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin-bottom: 10px;
}

.done-task {
  text-decoration: line-through;
  color: #555;
}
.tasks-container button {
  padding: 8px;
  margin-left: 5px;
  background-color: #fff; 
  color: #000; 
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.tasks-container button:hover {
  background-color: #eee; 
}

.edit-task-modal button {
  margin-top: 10px;
  background-color: rgb(12, 191, 12); 
  color: #fff; 
}
.tasks-container button i,
.edit-task-modal button i {
  color: #000; 
  transition: transform 0.3s ease; 
}

.tasks-container button:hover i,
.edit-task-modal button:hover i {
  transform: scale(1.2); 
}
.edit-task-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2; 
}

.modal-content {
  background: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
  z-index: 3; 
}



/* Media Query for responsiveness */
@media (max-width: 600px) {
  .container{
    margin-top: 120px;
  }
  .tasks-container {
    flex-direction: column;
  }
  .task-list {
    margin-top: 20px;
  }
  .modal-content {
    width: 80%; 
  }
}
@media (min-width: 300px) and (max-width: 600px) {
  .tasks-container button i,
  .edit-task-modal button i {
    font-size: 14px; 
  }

  .container{
    margin-top: 250px;
    background: linear-gradient(to bottom right, #3498db, #1abc9c) !important;
  }

  .tasks-container button {
    padding: 10px; 
  }

  .edit-task-modal button {
    margin-top: 10px; 
  }
}


@media (min-width: 600px) {

  .tasks-container button i,
  .edit-task-modal button i {
    font-size: 18px; 
  }

  .tasks-container button {
    padding: 12px;
  }

  .edit-task-modal button {
    margin-top: 15px;
  }
}

@media (min-width: 768px) {

  .tasks-container button i,
  .edit-task-modal button i {
    font-size: 20px; 
  }

  .tasks-container button {
    padding: 14px;
  }

  .edit-task-modal button {
    margin-top: 20px;
  }
}
</style>

