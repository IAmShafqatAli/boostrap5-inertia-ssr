<template>
    <app-layout title="Contracts">
<!--        $page.props.jetstream.canUpdateProfileInformation-->
        <template #header>
            <h2 class="h4 font-weight-bold">
              List of All contracts
            </h2>
        </template>

    </app-layout>
</template>

<script>
import {defineComponent} from 'vue'
import AppLayout from '@/Layouts/AppLayout.vue'

export default defineComponent({
    props: ['contracts'],
    components: {
        AppLayout,
    },
})
</script>
