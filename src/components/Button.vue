<template>
    <button @click="$emit('button-clicked')" :style="{ background: color }" class="btn">{{ text }}</button>
</template>

<script>
export default {
    name: 'Button',
    props: {
        text: String,
        color: String,
    },
    emits: ['button-clicked']
}
</script>
