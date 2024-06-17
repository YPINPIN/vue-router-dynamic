<script setup>
import { useRouter } from 'vue-router';
import { onMounted, ref } from 'vue';

const router = useRouter();
// 是否為 Admin
const isAdmin = ref(localStorage.getItem('isAdmin'));

// 紀錄刪除路由函數(router.addRoute() 返回的回調)
const rmRoutes = ref([]);
// 添加刪除路由函數
function addRmRoutes(fn) {
  rmRoutes.value.push(fn);
}
// 通過調用 router.addRoute() 返回的回調刪除路由，如果存在的話
function removeRoutes() {
  rmRoutes.value.forEach((fn) => fn());
}

function addNormalRoutes() {
  // 添加一級路由
  router.addRoute({
    path: '/about',
    name: 'About',
    component: () => import('@/views/About.vue'),
  });
  const removeRoute = router.addRoute({
    path: '/admin',
    name: 'Admin',
    component: () => import('@/views/AdminTip.vue'),
  });

  // // 使用 hasRoute 檢查路由是否存在
  // if (router.hasRoute('Admin')) {
  //   // 1. 可以通過使用 router.removeRoute() 按名稱刪除路由
  //   router.removeRoute('Admin');
  // }

  // // 3. 可以通過調用 `router.addRoute()` 返回的回調來刪除對應的路由
  // removeRoute();

  // 獲取一個包含所有路由配置的陣列
  console.log('allRoutes: ', router.getRoutes());
}

function addAdminRoutes() {
  // 2. 若重複添加相同 name 的路由，則會先刪除原路由，再添加新路由
  router.addRoute({
    path: '/admin',
    name: 'Admin',
    component: () => import('@/views/Admin.vue'),
  });

  // 紀錄刪除路由函數，方便切換權限時刪除路由
  addRmRoutes(
    // 添加巢狀路由
    router.addRoute('Admin', {
      path: '',
      name: 'AdminInfo',
      component: () => import('@/views/AdminInfo.vue'),
    })
  );
  addRmRoutes(
    router.addRoute('Admin', {
      path: 'settings',
      name: 'AdminSettings',
      component: () => import('@/views/AdminSettings.vue'),
    })
  );

  // 獲取一個包含所有路由配置的陣列
  console.log('allRoutes: ', router.getRoutes());
}

onMounted(() => {
  console.log('onMounted');
  isAdmin.value ? addAdminRoutes() : addNormalRoutes();
});

function login() {
  console.log('login');
  localStorage.setItem('isAdmin', true);
  isAdmin.value = true;
  // 刪除路由
  removeRoutes();
  addAdminRoutes();
  // 需要手動調用 router.replace() 來改變當前的位置顯示對應頁面
  router.replace(router.currentRoute.value.fullPath);
}
function logout() {
  console.log('logout');
  localStorage.removeItem('isAdmin');
  isAdmin.value = null;
  // 刪除路由
  removeRoutes();
  addNormalRoutes();
  // 需要手動調用 router.replace() 來改變當前的位置顯示對應頁面
  router.replace(router.currentRoute.value.fullPath);
}
</script>

<template>
  <h1>Hello Vue Router4!</h1>
  <p><strong>Current route path:</strong> {{ $route.fullPath }}</p>
  <button v-if="isAdmin" @click="logout">Admin Logout</button>
  <button v-else @click="login">Admin Login</button>
  <hr />

  <nav>
    <router-link to="/">Go to Home</router-link> |
    <router-link to="/about">Go to About</router-link> |
    <router-link to="/admin">Go to Admin</router-link>
    <template v-if="isAdmin">
      |
      <router-link to="/admin/settings">Go to AdminSettings</router-link>
    </template>
  </nav>

  <main>
    <router-view />
  </main>
</template>

<style scoped>
main {
  margin-top: 5px;
  padding: 10px;
  background-color: lightblue;
}
</style>
