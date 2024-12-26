<template>
  <label :class="classes" class="form-label">
    <span v-if="value">{{ value }}</span>
		<span v-else><slot></slot></span>
	</label>
</template>

<script>
  import { defineComponent } from 'vue'

	export default defineComponent({
		props: ['value', 'classes']
	})
</script>
