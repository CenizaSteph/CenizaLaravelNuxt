<template>
    <label :for="props.for" v-if="props.label" class="text-green-800">{{ props.label }}</label>
</template>

<script setup lang="ts">
const props = defineProps({
    for: {
        type: String,
        required: false,
    },
    label: {
        type: String,
        required: false,
    },
})
</script>