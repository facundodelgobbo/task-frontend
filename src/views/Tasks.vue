<template>
  <div>
    <h1 class="text-2xl font-bold mb-4">Listado de Tareas</h1>
    <button
      class="bg-indigo-600 text-white py-2 px-4 rounded-md hover:bg-indigo-700 mb-4"
      @click="openModal('create')"
    >
      Crear Tarea
    </button>

    <table class="table-auto w-full border-collapse border border-gray-200">
      <thead>
        <tr class="bg-gray-100">
          <th class="border px-4 py-2 text-left">Título</th>
          <th class="border px-4 py-2 text-left">Descripción</th>
          <th class="border px-4 py-2 text-left">Estado</th>
          <th class="border px-4 py-2 text-center">Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="task in tasks" :key="task.id">
          <td class="border px-4 py-2">{{ task.title }}</td>
          <td class="border px-4 py-2">{{ task.description }}</td>
          <td
            class="border px-4 py-2"
            :class="{
              'text-green-600': task.status === 'completed',
              
              'text-yellow-600': task.status === 'pending',
            }"
          >
            {{ task.status }}
          </td>
          <td class="border px-4 py-2 text-center">
            <button
              class="bg-blue-500 text-white py-1 px-3 rounded-md hover:bg-blue-600 mr-2"
              @click="openModal('edit', task)"
            >
              Editar
            </button>
            <button
              class="bg-red-500 text-white py-1 px-3 rounded-md hover:bg-red-600"
              @click="deleteTask(task.id)"
            >
              Eliminar
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Modal -->
    <div
      v-if="showModal"
      class="fixed inset-0 bg-gray-900 bg-opacity-50 flex items-center justify-center z-50"
    >
      <div class="bg-white p-6 rounded-lg shadow-lg max-w-md w-full">
        <h2 class="text-xl font-bold mb-4">
          {{ modalType === 'create' ? 'Crear Nueva Tarea' : 'Editar Tarea' }}
        </h2>
        <form @submit.prevent="modalType === 'create' ? createTask() : updateTask()">
          <div class="mb-4">
            <label for="title" class="block text-sm font-medium text-gray-700">
              Título
            </label>
            <input
              id="title"
              type="text"
              v-model="currentTask.title"
              required
              class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
            />
          </div>

          <div class="mb-4">
            <label for="description" class="block text-sm font-medium text-gray-700">
              Descripción
            </label>
            <textarea
              id="description"
              v-model="currentTask.description"
              required
              class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
            ></textarea>
          </div>

          <div class="mb-4">
            <label for="status" class="block text-sm font-medium text-gray-700">
              Estado
            </label>
            <select
              id="status"
              v-model="currentTask.status"
              required
              class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
            >
              <option value="pending">Pendiente</option>
             
              <option value="completed">Completada</option>
            </select>
          </div>

          <div class="flex justify-end">
            <button
              type="button"
              @click="showModal = false"
              class="bg-gray-300 text-gray-700 py-2 px-4 rounded-md mr-2 hover:bg-gray-400"
            >
              Cancelar
            </button>
            <button
              type="submit"
              class="bg-indigo-600 text-white py-2 px-4 rounded-md hover:bg-indigo-700"
            >
              Guardar
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      tasks: [], // Listado de tareas
      showModal: false, // Controla la visibilidad del modal
      modalType: 'create', // 'create' o 'edit'
      currentTask: {
        id: null,
        title: '',
        description: '',
        status: 'pending',
      },
    };
  },
  async created() {
    const token = localStorage.getItem('token');
    try {
      const response = await axios.get('/api/tasks', {
        headers: { Authorization: `Bearer ${token}` },
      });
      this.tasks = response.data;
    } catch (error) {
      alert('No se pudo cargar el listado de tareas');
    }
  },
  methods: {
    openModal(type, task = null) {
      this.modalType = type;
      if (type === 'edit' && task) {
        this.currentTask = { ...task };
      } else {
        this.currentTask = { id: null, title: '', description: '', status: 'pending' };
      }
      this.showModal = true;
    },
    async createTask() {
      const token = localStorage.getItem('token');
      try {
        const response = await axios.post('/api/tasks', this.currentTask, {
          headers: { Authorization: `Bearer ${token}` },
        });
        this.tasks.push(response.data);
        this.showModal = false;
      } catch (error) {
        alert('No se pudo crear la tarea');
      }
    },
    async updateTask() {
      const token = localStorage.getItem('token');
      try {
        await axios.put(`/api/tasks/${this.currentTask.id}`, this.currentTask, {
          headers: { Authorization: `Bearer ${token}` },
        });
        const index = this.tasks.findIndex((task) => task.id === this.currentTask.id);
        if (index !== -1) this.tasks.splice(index, 1, { ...this.currentTask });
        this.showModal = false;
      } catch (error) {
        alert('No se pudo actualizar la tarea');
      }
    },
    async deleteTask(id) {
      const token = localStorage.getItem('token');
      try {
        await axios.delete(`/api/tasks/${id}`, {
          headers: { Authorization: `Bearer ${token}` },
        });
        this.tasks = this.tasks.filter((task) => task.id !== id);
      } catch (error) {
        alert('No se pudo eliminar la tarea');
      }
    },
  },
};
</script>
