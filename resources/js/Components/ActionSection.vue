<script setup>
import SectionTitle from './SectionTitle.vue';
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

        <div class="container-fluid min-vh-100 overflow-y-auto">
            <div class="card bg-white shadow p-3">
                <slot name="content" />
            </div>
        </div>
    </div>
</template>
