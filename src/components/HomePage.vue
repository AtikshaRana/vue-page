<template>
  <div>
    <div v-for="item in filteredItems" :key="item.id">
      <h3>{{ item.companyName }}</h3>
    
    </div>
  </div>
</template>

<script>
export default {
    name: 'HomePage',
};
</script>

