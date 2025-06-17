<!-- file: pages/index.vue -->
<template>
    <!-- Container หลักของหน้า จัดให้อยู่กึ่งกลาง -->
    <div class="flex min-h-screen flex-col items-center justify-center bg-gray-50 p-4">

        <!-- ส่วนหัวเรื่อง "NEW KEY INTRODUCTION" -->
        <div class="mb-8 w-full max-w-4xl text-left">
            <h2 class="text-sm font-bold tracking-widest text-gray-500">
                NEW KEY INTRODUCTION
            </h2>
        </div>

        <!-- ส่วนเนื้อหาหลัก -->
        <main class="flex w-full max-w-4xl flex-col items-center gap-12 text-center">
            <!-- 
          เรียกใช้ Topic Component พร้อมส่ง props
        -->
            <Topic 
                :target-key="currentTargetKey"
                :finger="currentFinger"
                :last-pressed-key="lastPressedKey"
            />

            <!--
          เรียกใช้ Keyboard Component พร้อมรับ event
        -->
            <Keyboard @key-pressed="handleKeyPressed" />
        </main>

        <!-- เราเพิ่มส่วนของภาพมือเข้าไป เพื่อให้เหมือนต้นฉบับมากขึ้น -->
        <!-- <div class="pointer-events-none fixed bottom-0 left-1/2 -translate-x-1/2">
            <img src="/hands-overlay.png" alt="Hands overlay" class="w-full max-w-2xl opacity-80" />
        </div> -->

    </div>
</template>

<script>
// ไม่จำเป็นต้อง import เพราะ Nuxt.js 3 จัดการให้แล้ว
// แต่ถ้าใช้ Nuxt 2 หรือ Vue CLI ต้อง import components ก่อน
import Topic from '~/components/Topic.vue';
import Keyboard from '~/components/Keyboard.vue';

export default {
    name: 'TypingPage',
    data() {
        return {
            currentTargetKey: 'f',
            currentFinger: 'left index',
            lastPressedKey: null
        };
    },
    methods: {
        handleKeyPressed(keyData) {
            this.lastPressedKey = keyData;
            
            // อัปเดต target key และ finger ตามคีย์ที่กด
            this.updateTarget(keyData.key);
        },
        
        updateTarget(pressedKey) {
            // กำหนดคีย์เป้าหมายและนิ้วที่ใช้ตามคีย์ที่กด
            const keyMapping = {
                'f': { key: 'j', finger: 'right index' },
                'j': { key: 'd', finger: 'left middle' },
                'd': { key: 'k', finger: 'right middle' },
                'k': { key: 's', finger: 'left ring' },
                's': { key: 'l', finger: 'right ring' },
                'l': { key: 'a', finger: 'left pinky' },
                'a': { key: ';', finger: 'right pinky' },
                ';': { key: 'f', finger: 'left index' }
            };
            
            if (keyMapping[pressedKey]) {
                this.currentTargetKey = keyMapping[pressedKey].key;
                this.currentFinger = keyMapping[pressedKey].finger;
            }
        }
    }
};
</script>