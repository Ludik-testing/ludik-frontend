<template>
  <div>
    <h1>Lista de Tareas</h1>

    <form @submit.prevent="createTask">
      <label for="title">Título:</label>
      <input type="text" v-model="newTask.title" id="title" required />
      
      <label for="description">Descripción:</label>
      <textarea v-model="newTask.description" id="description" required></textarea>

      <button type="submit">Agregar tarea</button>
    </form>

    <div v-if="tasks.length === 0">
      <p>No hay tareas disponibles.</p>
    </div>

    <ul v-else>
      <li v-for="task in tasks" :key="task.id">
        <strong>{{ task.title }}</strong><br />
        <p>{{ task.description }}</p>
        <p>Estado: <span :class="{'completed': task.completed}">{{ task.completed ? 'Completada' : 'Pendiente' }}</span></p>
        <button @click="deleteTask(task.id)">Eliminar</button>
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
  },
};
</script>
