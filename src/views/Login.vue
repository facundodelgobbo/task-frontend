<template>
  <div class="max-w-md mx-auto bg-white shadow-md rounded-lg p-8">
    <h1 class="text-2xl font-bold mb-6 text-center">Iniciar Sesión</h1>
    <form @submit.prevent="login" class="space-y-4">
      <div>
        <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
        <input id="email" v-model="email" type="email"
          class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
          placeholder="example@example.com" required />
      </div>
      <div>
        <label for="password" class="block text-sm font-medium text-gray-700">Contraseña</label>
        <input id="password" v-model="password" type="password"
          class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
          placeholder="********" required />
      </div>
      <button type="submit"
        class="w-full bg-indigo-600 text-white py-2 px-4 rounded-md hover:bg-indigo-700 focus:ring-2 focus:ring-indigo-500">
        Iniciar Sesión
      </button>
    </form>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      email: '',
      password: '',
    };
  },
  methods: {
    async login() {
      try {
        const response = await axios.post('/api/login', {
          email: this.email,
          password: this.password,
        },

          { withCredentials: true }
        );
        localStorage.setItem('token', response.data.token); // Guarda el token
        this.$router.push('/tasks'); // Redirige al listado de tareas
      } catch (error) {
        console.log(error)
        alert('Error al iniciar sesión');
      }
    },
  },
};
</script>
