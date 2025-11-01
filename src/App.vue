<script>
import AppHeader from "@/components/AppHeader.vue"; 

export default {
  components: {
    AppHeader, 
  },
};
</script>

<template>
  <div id="app">
    <!-- Thanh tiêu đề hoặc header của ứng dụng -->
    <AppHeader />
    
    <div class="container mt-3">
      <!-- router-view là nơi các trang như ContactBook.vue được render -->
      <router-view />
    </div>
  </div>
</template>

<style>
.page {
  max-width: 400px; 
  margin: auto;     
}
</style>
