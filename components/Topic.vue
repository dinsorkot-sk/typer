<!-- file: components/Topic.vue -->
<template>
    <p class="text-3xl text-gray-700 sm:text-4xl">
        Type the
        <span class="mx-1 inline-block rounded-md bg-blue-500 px-3 py-1 font-bold text-white">
            {{ targetKey || 'f' }}
        </span>
        key using your 
        <span class="mx-1 inline-block rounded-md bg-green-500 px-3 py-1 font-bold text-white">
            {{ finger || 'left index' }}
        </span>
        finger.
    </p>
    
    <!-- แสดงข้อมูลคีย์ที่กดล่าสุด -->
    <div v-if="lastPressedKey" class="mt-4 text-lg text-gray-600">
        <p>Last pressed: 
            <span class="font-bold text-blue-600">{{ lastPressedKey.display }}</span>
        </p>
        <p class="text-sm text-gray-500">{{ formatTime(lastPressedKey.timestamp) }}</p>
    </div>
</template>

<script>
export default {
    name: 'Topic',
    props: {
        targetKey: {
            type: String,
            default: 'f'
        },
        finger: {
            type: String,
            default: 'left index'
        },
        lastPressedKey: {
            type: Object,
            default: null
        }
    },
    methods: {
        formatTime(timestamp) {
            return new Date(timestamp).toLocaleTimeString();
        }
    }
};
</script>