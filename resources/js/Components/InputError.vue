<script setup>
defineProps({
  message: String,
});
</script>

<template>
  <div v-show="message">
    <p class="small text-danger">
      {{ message }}
    </p>
  </div>
</template>
