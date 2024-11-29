<template>
    <form @submit.prevent="updateTask" class="task-edit-form">
      <label for="editTitle" class="label edit-label">Título:</label>
      <input type="text" v-model="task.title" id="editTitle" required class="input-field input-text-white" />
  
      <label for="editDescription" class="label edit-label">Descripción:</label>
      <textarea v-model="task.description" id="editDescription" required class="input-field input-text-white"></textarea>
  
      <label for="editStatus" class="label edit-label">Estado:</label>
      <select v-model="task.status" id="editStatus" required class="input-field">
        <option value="PENDING">Pendiente</option>
        <option value="IN_PROGRESS">En Progreso</option>
        <option value="COMPLETED">Completado</option>
      </select>
  
      <div class="form-actions">
        <button type="submit" class="button primary-button">Actualizar tarea</button>
        <button @click="$emit('cancel')" type="button" class="button cancel-button">Cancelar</button>
      </div>
    </form>
  </template>
  
  <script>
  export default {
    props: ['task'],
    methods: {
      async updateTask() {
        try {
          await this.$emit('update', this.task);
        } catch (error) {
          console.error('Error al actualizar tarea:', error);
        }
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
    
  