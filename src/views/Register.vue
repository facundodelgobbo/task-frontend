<template>
  <div class="min-h-screen flex items-center justify-center bg-gray-100">
    <div class="max-w-md w-full bg-white p-6 rounded-lg shadow-md">
      <h2 class="text-2xl font-bold text-center mb-6">Regístrate</h2>
      <form @submit.prevent="handleRegister">
        <div class="mb-4">
          <label for="name" class="block text-sm font-medium text-gray-700">Nombre</label>
          <input
            id="name"
            type="text"
            v-model="form.name"
            required
            class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
          />
        </div>

        <div class="mb-4">
          <label for="email" class="block text-sm font-medium text-gray-700">Correo Electrónico</label>
          <input
            id="email"
            type="email"
            v-model="form.email"
            required
            class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
          />
        </div>

        <div class="mb-6">
          <label for="password" class="block text-sm font-medium text-gray-700">Contraseña</label>
          <input
            id="password"
            type="password"
            v-model="form.password"
            required
            class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
          />
        </div>

        <div class="mb-6">
          <label for="password_confirmation" class="block text-sm font-medium text-gray-700">Confirmar Contraseña</label>
          <input
            id="password_confirmation"
            type="password"
            v-model="form.password_confirmation"
            required
            class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
          />
        </div>

        <button
          type="submit"
          class="w-full bg-indigo-600 text-white py-2 px-4 rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500"
        >
          Registrarse
        </button>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';

const form = ref({
  name: '',
  email: '',
  password: '',
  password_confirmation: '',
});

const handleRegister = async () => {
  if (form.value.password !== form.value.password_confirmation) {
    alert('Las contraseñas no coinciden');
    return;
  }

  try {
    const response = await axios.post('/api/register', form.value); // Cambia la URL a tu backend
    alert(response.data.message); // Mostrar el mensaje de éxito
    // Redirige a login o a la página principal
  } catch (error) {
    if (error.response && error.response.data.errors) {
      // Aquí puedes acceder a los detalles de los errores de validación
      const errors = error.response.data.errors;
      console.error(errors); // Para ver qué campos están fallando
      alert(`Errores de validación: ${JSON.stringify(errors)}`);
    } else {
      alert('Ocurrió un error, intenta de nuevo.');
    }
  }
};
</script>
