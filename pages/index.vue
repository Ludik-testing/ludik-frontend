<template>
  <div class="container">
    <h1 class="title">Lista de Tareas</h1>

    <form @submit.prevent="createTask" class="task-form">
      <label for="title" class="label">Título:</label>
      <input type="text" v-model="newTask.title" id="title" required class="input-field input-text-white" />

      <label for="description" class="label">Descripción:</label>
      <textarea v-model="newTask.description" id="description" required class="input-field input-text-white"></textarea>

      <button type="submit" class="button primary-button">Agregar tarea</button>
    </form>
   
    <div v-if="editingTask" class="task-edit-form">
      <h2 class="subtitle">Editar Tarea</h2>
      <form @submit.prevent="updateTask">
        <label for="editTitle" class="label edit-label">Título:</label>
        <input type="text" v-model="editingTask.title" id="editTitle" required class="input-field input-text-white" />

        <label for="editDescription" class="label edit-label">Descripción:</label>
        <textarea v-model="editingTask.description" id="editDescription" required class="input-field input-text-white"></textarea>

        <label for="editStatus" class="label edit-label">Estado:</label>
        <select v-model="editingTask.status" id="editStatus" required class="input-field">
          <option value="PENDING">Pendiente</option>
          <option value="IN_PROGRESS">En Progreso</option>
          <option value="COMPLETED">Completado</option>
        </select>

        <div class="form-actions">
          <button type="submit" class="button primary-button">Actualizar tarea</button>
          <button @click="cancelEdit" type="button" class="button cancel-button">Cancelar</button>
        </div>
      </form>
    </div>



    <div v-if="tasks.length === 0" class="empty-message">
      <p>No hay tareas disponibles.</p>
    </div>

    <ul v-else class="task-list">
      <li v-for="task in tasks" :key="task.id" class="task-item">
        <div class="task-content">
          <strong class="task-title">{{ task.title }}</strong><br />
          <p class="task-description">{{ task.description }}</p>
          <p class="status-text" :class="{'completed': task.status === 'COMPLETED'}">
            {{ task.status === 'COMPLETED' ? 'Completada' : task.status === 'IN_PROGRESS' ? 'En Progreso' : 'Pendiente' }}
          </p>
        </div>
        <div class="task-actions">
          <button @click="deleteTask(task.id)" class="button delete-button">Eliminar</button>
          <button @click="editTask(task)" class="button edit-button">Editar</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tasks: [],
      newTask: {
        title: '',
        description: '',
      },
      editingTask: null,
    };
  },
  async mounted() {
    await this.fetchTasks();
  },
  methods: {
    async fetchTasks() {
      try {
        const response = await this.$axios.get('tasks/');
        this.tasks = response.data;
      } catch (error) {
        console.error('Error al cargar tareas:', error);
      }
    },

    async createTask() {
      try {
        const response = await this.$axios.post('tasks/', this.newTask);
        this.tasks.push(response.data);
        this.newTask.title = '';
        this.newTask.description = '';
      } catch (error) {
        console.error('Error al crear tarea:', error);
      }
    },

    async deleteTask(id) {
      try {
        await this.$axios.delete(`tasks/${id}/`);
        this.tasks = this.tasks.filter(task => task.id !== id);
      } catch (error) {
        console.error('Error al eliminar tarea:', error);
      }
    },

    editTask(task) {
      this.editingTask = { ...task }; 
    },


    async updateTask() {
      try {
        const response = await this.$axios.put(`tasks/${this.editingTask.id}/`, this.editingTask);
        const updatedTask = response.data;
        
        const index = this.tasks.findIndex(task => task.id === updatedTask.id);
        if (index !== -1) {
          this.tasks.splice(index, 1, updatedTask);
        }

        this.editingTask = null;
      } catch (error) {
        console.error('Error al actualizar tarea:', error);
      }
    },

    cancelEdit() {
      this.editingTask = null;
    },
  },
};
</script>

<style scoped>

.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Arial', sans-serif;
}

.title {
  font-size: 2rem;
  text-align: center;
  margin-bottom: 20px;
}

.subtitle {
  font-size: 1.5rem;
  margin-bottom: 10px;
  color: black; 
}

/* .label {
  color: white;
} */

 .edit-label {
  font-weight: bold;
  margin-bottom: 5px;
  display: block;
  color: black; 
}

.input-field {
  width: 100%;
  padding: 10px;
  margin-bottom: 15px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}

.input-text-white {
  color: white; 
  background-color: #333; 
}

.button {
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.3s ease;
  margin-right: 10px;
}

.primary-button {
  background-color: #4CAF50;
  color: white;
}

.primary-button:hover {
  background-color: #45a049;
}

.cancel-button {
  background-color: #f44336;
  color: white;
}

.cancel-button:hover {
  background-color: #e53935;
}

.delete-button {
  background-color: #f44336;
  color: white;
}

.delete-button:hover {
  background-color: #d32f2f;
}

.edit-button {
  background-color: #2196F3;
  color: white;
}

.edit-button:hover {
  background-color: #1976D2;
}

.task-list {
  list-style: none;
  padding: 0;
  margin-top: 30px;
}

.task-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  border: 1px solid #ddd;
  border-radius: 4px;
  margin-bottom: 10px;
  background-color: #f9f9f9;
}

.task-content {
  flex-grow: 1;
}

.task-title, .task-description, .status-text  {
  color: black; 
}

.task-actions {
  display: flex;
  gap: 10px;
}

.status-text {
  font-weight: bold;
}

.completed {
  color: green;
}

.empty-message {
  text-align: center;
  margin-top: 30px;
}

.task-edit-form {
  margin-top: 20px;
  padding: 20px;
  border-radius: 4px;
  background-color: #f0f0f0;
  margin-bottom: 30px;
}

@media (max-width: 600px) {
  .container {
    padding: 10px;
  }

  .task-item {
    
    flex-direction: column;
    align-items: flex-start;
  }

  .task-actions {
    margin-top: 10px;
    justify-content: flex-start;
  }

  .button {
    width: 100%;
    margin-bottom: 10px;
  }

  .primary-button {
    width: 100%;
  }
}
</style>
