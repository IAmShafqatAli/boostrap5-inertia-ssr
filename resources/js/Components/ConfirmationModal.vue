<script setup>
import { defineProps, defineEmits } from 'vue';

// Emitting 'close' event to parent component
const emit = defineEmits(['close']);

// Define props
defineProps({
  show: {
    type: Boolean,
    default: false,
  },
  maxWidth: {
    type: String,
    default: '2xl',
  },
  closeable: {
    type: Boolean,
    default: true,
  },
});

// Method to close the modal
const close = () => {
  emit('close');
};
</script>

<template>
  <div v-if="show" class="modal fade show" tabindex="-1" aria-hidden="true" style="display: block !important;">
    <!-- Modal Dialog -->
    <div class="modal-dialog modal-dialog-centered" :class="`modal-${maxWidth}`">
      <!-- Modal Content -->
      <div class="modal-content">
        <!-- Modal Header -->
        <div class="modal-header">
          <h5 class="modal-title">
            <slot name="title" />
          </h5>
          <!-- Close Button -->
          <button v-if="closeable" type="button" class="btn-close" aria-label="Close" @click="close"></button>
        </div>

        <!-- Modal Body -->
        <div class="modal-body">
          <slot name="content" />
        </div>

        <!-- Modal Footer -->
        <div class="modal-footer">
          <slot name="footer" />
        </div>
      </div>
    </div>

    <!-- Modal Background (Backdrop) -->
    <div v-if="closeable" class="modal-backdrop fade show" @click="close"></div>
  </div>
</template>
