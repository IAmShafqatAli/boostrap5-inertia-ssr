<script setup>
import { computed, useSlots } from 'vue';
import SectionTitle from './SectionTitle.vue';

defineEmits(['submitted']);

const hasActions = computed(() => !! useSlots().actions);
</script>

<template>
    <div class="container-fluid">
        <SectionTitle>
            <template #title>
                <slot name="title" />
            </template>
            <template #description>
                <slot name="description" />
            </template>
        </SectionTitle>

        <div class="card bg-white shadow">
            <form @submit.prevent="$emit('submitted')">
                <div
                    class="card-body"
                    :class="hasActions ? 'rounded' : 'rounded'"
                >
                    <div class="grid grid-cols-6 gap-6">
                        <slot name="form" />
                    </div>
                </div>

                <div v-if="hasActions" class="d-flex align-items-center justify-content-end px-4 py-3 rounded card-footer">
                    <slot name="actions" />
                </div>
            </form>
        </div>
    </div>
</template>
