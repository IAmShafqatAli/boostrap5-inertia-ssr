<script setup>
import { computed, onMounted, onUnmounted, watch } from 'vue';

const props = defineProps({
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

const emit = defineEmits(['close']);

watch(() => props.show, (newVal) => {
  if (newVal) {
    document.body.style.overflow = 'hidden';
  } else {
    document.body.style.overflow = null;
  }
});

const close = () => {
  if (props.closeable) {
    emit('close');
  }
};

const closeOnEscape = (e) => {
  if (e.key === 'Escape' && props.show) {
    close();
  }
};

onMounted(() => document.addEventListener('keydown', closeOnEscape));

onUnmounted(() => {
  document.removeEventListener('keydown', closeOnEscape);
  document.body.style.overflow = null;
});

const maxWidthClass = computed(() => {
  return {
    sm: 'max-w-sm',
    md: 'max-w-md',
    lg: 'max-w-lg',
    xl: 'max-w-xl',
    '2xl': 'max-w-2xl',
  }[props.maxWidth] || '';
});
</script>

<template>
  <teleport to="body">
    <transition name="modal-transition">
      <!-- Ensure this is a div (element root) -->
      <div v-if="show" class="modal fade show d-block" tabindex="-1" aria-hidden="true">
        <!-- Modal Background -->
        <div class="modal-dialog modal-dialog-centered" :class="maxWidthClass">
          <div class="modal-content">
            <!-- Modal Header -->
            <div class="modal-header">
              <button
                v-if="props.closeable"
                type="button"
                class="btn-close"
                data-bs-dismiss="modal"
                aria-label="Close"
                @click="close"
              ></button>
              <slot name="title"/>
            </div>

            <!-- Modal Body -->
            <div class="modal-body">
              <slot name="content"/>
            </div>

            <!-- Modal Footer -->
            <div class="modal-footer">
              <slot name="footer"/>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </teleport>
</template>
