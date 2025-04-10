<script setup>
import {ref, watch} from 'vue'
import {useRouter, useRoute} from 'vue-router'
import Menubar from 'primevue/menubar'
import Toast from 'primevue/toast';

const router = useRouter()
const route = useRoute()
const isAuthenticated = ref(false)

watch(() => route.path, (newPath) => {
  const requiresAuth = route.matched.some(record => record.meta.requiresAuth)
  const auth = localStorage.getItem('accessToken')

  if (requiresAuth && !auth) {
    router.push('/auth')
  }

  isAuthenticated.value = !!auth
})

// Пункты меню
const items = ref([
  {
    label: 'Главная',
    icon: 'pi pi-home',
    command: () => router.push('/'),
    route: '/'
  },
  {
    label: 'Авторизация',
    icon: 'pi pi-sign-in',
    command: () => router.push('/auth'),
    route: '/auth',
    visible: () => !isAuthenticated.value
  },
  {
    label: 'Профиль',
    icon: 'pi pi-user',
    command: () => router.push('/profile'),
    route: '/profile',
    visible: () => isAuthenticated.value
  },
  {
    label: 'Стена',
    icon: 'pi pi-comments',
    command: () => router.push('/wall'),
    route: '/wall',
    visible: () => isAuthenticated.value
  }
])

// Подсветка активного пункта меню
const activeMenuItem = ref(null)
watch(
    () => route.path,
    (newPath) => {
      activeMenuItem.value = items.value.find((item) => item.route === newPath)
    },
    {immediate: true}
)
</script>

<template>
  <div class="app-container">
    <!-- Навигационное меню -->
    <Menubar :model="items" class="mb-4">
      <template #start>
        <span class="text-xl font-bold text-primary">Work & Study</span>
      </template>
      <template #item="{ item }">
        <div
            :class="['p-menuitem', { 'active-menu-item': item === activeMenuItem }]"
            @click="item.command()"
        >
          <span :class="item.icon"></span>
          <span class="ml-2">{{ item.label }}</span>
        </div>
      </template>
    </Menubar>

    <!-- Основное содержимое -->
    <div class="content">
      <router-view></router-view>
    </div>
    <Toast />
  </div>
</template>

<style>
.app-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.content {
  flex: 1;
  padding: 1rem;
}

/* Стили для PrimeVue компонентов */
.p-menubar {
  border-radius: 0;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  padding: 0.5rem;
}

.p-menuitem > span {
  margin-right: 0.5rem; /* Отступ между иконкой и текстом */
}

.p-menuitem {
  padding: 0.25rem 1.5rem; /* Отступы сверху/снизу и слева/справа */
}

/* Зелёная тема для кнопок */
.p-button {
  background: var(--primary-color) !important;
  border-color: var(--primary-color) !important;
}

/* Подсветка активного пункта меню */
.active-menu-item {
  background-color: var(--primary-color);
  color: var(--primary-color-text);
}

:root {
  --primary-color: #4CAF50;
  --primary-color-text: #ffffff;
}
</style>