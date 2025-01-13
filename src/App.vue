<script setup>
import { RouterLink, RouterView, useRouter } from 'vue-router';
import { ref } from 'vue';
import { Bars3Icon, XMarkIcon } from "@heroicons/vue/24/solid";

// Variables reactivas
const isAuthenticated = ref(false);
const router = useRouter();

// Función para verificar si el usuario está autenticado
const checkAuth = () => {
  isAuthenticated.value = !!localStorage.getItem('token');
  console.log('Estado autenticado:', isAuthenticated.value); // Para depurar
};

// Inicializar el estado de autenticación
checkAuth();

// Detectar cambios en localStorage mediante un evento
window.addEventListener('storage', () => {
  checkAuth();
});

// Método para cerrar sesión
const logout = () => {
  localStorage.removeItem('token'); // Elimina el token
  checkAuth(); // Actualiza el estado de autenticación
  router.push('/login'); // Redirige al login
};
</script>

<template>
  <header class="bg-gray-900 w-full">
    <nav class="mx-auto flex max-w-full items-center justify-between p-8 lg:px-12" aria-label="Global">
      <!-- Logo -->
      <div class="flex lg:flex-1">
        <a href="#" class="-m-1.5 p-1.5">
          <span class="sr-only">Proyecto Tasks</span>
          <img class="h-8 w-auto" src="https://tailwindui.com/plus/img/logos/mark.svg?color=indigo&shade=500" alt="Logo" />
        </a>
      </div>

      <!-- Navegación (Desktop) -->
      <div class="hidden lg:flex lg:gap-x-12">
        <RouterLink v-if="isAuthenticated" to="/tasks" class="text-sm font-semibold text-white hover:text-gray-400">
          Listado de Tareas
        </RouterLink>
        <button v-if="isAuthenticated" @click="logout" class="text-sm font-semibold text-white hover:text-gray-400">
          Cerrar Sesión
        </button>
        <RouterLink v-if="!isAuthenticated" to="/login" class="text-sm font-semibold text-white hover:text-gray-400">
          Iniciar Sesión
        </RouterLink>
        <RouterLink v-if="!isAuthenticated" to="/register" class="text-sm font-semibold text-white hover:text-gray-400">
          Registrarse
        </RouterLink>
      </div>
    </nav>
  </header>

  <main class="container mx-auto mt-8 w-full">
    <RouterView />
  </main>
</template>
