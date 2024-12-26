<script setup>

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

const close = () => {
  if (props.closeable) {
    emit('close');
  }
};
</script>

<template>
  <teleport to="body">
    <transition name="modal-transition">
      <!-- Root element is a div to avoid the Vue warning -->
      <div v-if="show" class="modal fade show d-block" tabindex="-1" aria-hidden="true">
        <!-- Modal Background -->
        <div class="modal-dialog modal-dialog-centered" :class="maxWidth">
          <div class="modal-content">
            <!-- Modal Header -->
            <div class="modal-header">
              <div class="d-flex">
                <div class="p-1 w-100">
                  <slot name="title" />
                </div>
                <div class="p-1 flex-shrink-1">
                  <button
                    v-if="props.closeable"
                    type="button"
                    class="btn-close"
                    data-bs-dismiss="modal"
                    aria-label="Close"
                    @click="close"
                  ></button>
                </div>
                </div>

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
      </div>
    </transition>
  </teleport>
</template>
