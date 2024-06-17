<script setup>
import { useRoute, useRouter } from 'vue-router';
const route = useRoute();
const router = useRouter();
// 需要手動調用 router.replace() 來改變當前的位置顯示對應頁面
router.replace(route.fullPath);
</script>

<template>
  <h2>404 Not Found.</h2>
  <p>path: /{{ route.params.pathMatch }}</p>
</template>
