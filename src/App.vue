<template>
    <CalendarComp :date="date" />
</template>

<script setup>
import { ref } from 'vue';
import CalendarComp from './components/CalendarComp.vue';

const date = ref();
// date.value = new Date('1999-12-31');
</script>

<style>
:root {
    --color-black: #000;
    --color-white: #fff;
    --border-default: 2px solid transparent;
    --border-active: 2px solid var(--color-black);

    width: 100%;
}
</style>
