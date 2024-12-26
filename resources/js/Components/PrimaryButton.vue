<script setup>
defineProps({
  type: {
    type: String,
    default: 'submit',
  },
});
</script>

<template>
  <button :type="type" class="btn btn-primary d-inline-flex align-items-center px-4 py-2 text-white">
    <slot />
  </button>
</template>
