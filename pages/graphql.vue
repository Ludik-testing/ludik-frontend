<template>
  <div class="container">
    <h1 class="title">Lista de Tareas</h1>
    <div class="filter">
      <select v-model="status" @change="fetchTasks" class="status-select">
        <option value="">Todos</option>
        <option value="PENDING">Pendiente</option>
        <option value="IN_PROGRESS">En Progreso</option>
        <option value="COMPLETED">Completado</option>
      </select>
    </div>

    <ul v-if="tasks.length" class="task-list">
      <li v-for="task in tasks" :key="task.id" class="task-item">
        <div class="task-content">
          <h2 class="task-title">{{ task.title }}</h2>
          <p class="task-description">{{ task.description }}</p>
          <span class="task-status" :class="getStatusClass(task.status)">
            {{ formatStatus(task.status) }}
          </span>
        </div>
      </li>
    </ul>
    <p v-else class="no-tasks">No hay tareas</p>
  </div>



  
</template>

<script>
import gql from 'graphql-tag';

export default {
  data() {
    return {
      tasks: [],
      status: '',
    };
  },
  methods: {
    async fetchTasks() {
      const GET_TASKS = gql`
        query GetTasks($status: String) {
          taskByStatus(status: $status) {
            id
            title
            description
            status
          }
        }
      `;
      const client = this.$apollo.provider.defaultClient;
      const { data } = await client.query({
        query: GET_TASKS,
        variables: { status: this.status },
      });
      this.tasks = data.taskByStatus;
    },
    getStatusClass(status) {
      return {
        'status-pending': status === 'PENDING',
        'status-in-progress': status === 'IN_PROGRESS',
        'status-completed': status === 'COMPLETED',
      };
    },
    formatStatus(status) {
      switch (status) {
        case 'PENDING':
          return 'Pendiente';
        case 'IN_PROGRESS':
          return 'En Progreso';
        case 'COMPLETED':
          return 'Completado';
        default:
          return '';
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
  background-color: #f5f5f5;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.title {
  font-size: 2.5rem;
  text-align: center;
  margin-bottom: 20px;
  color: #333;
}

.filter {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.status-select {
  padding: 10px;
  font-size: 1rem;
  border-radius: 8px;
  border: 2px solid #ccc;
  transition: border-color 0.3s ease;
}

.status-select:focus {
  border-color: #4CAF50;
  outline: none;
}

.task-list {
  list-style: none;
  padding: 0;
  margin-top: 20px;
}

.task-item {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 15px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s;
}

.task-item:hover {
  transform: translateY(-5px);
}

.task-content {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.task-title {
  font-size: 1.5rem;
  color: #333;
  margin: 0;
}

.task-description {
  color: #666;
}

.task-status {
  padding: 5px 10px;
  border-radius: 4px;
  font-weight: bold;
  text-align: center;
  width: fit-content;
}

.status-pending {
  background-color: #ff9800;
  color: white;
}

.status-in-progress {
  background-color: #2196F3;
  color: white;
}

.status-completed {
  background-color: #4CAF50;
  color: white;
}

.no-tasks {
  text-align: center;
  color: #999;
}

@media (max-width: 600px) {
  .title {
    font-size: 2rem;
  }

  .task-item {
    padding: 15px;
  }

  .task-title {
    font-size: 1.25rem;
  }
}
</style>
