<script setup>
defineProps({
  type: {
    type: String,
    default: 'button',
  },
});
</script>

<template>
  <button :type="type" class="btn btn-danger d-inline-flex align-items-center justify-center px-4 py-2">
    <slot />
  </button>
</template>
