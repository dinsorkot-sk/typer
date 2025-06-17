<!-- file: components/Keyboard.vue -->
<template>
    <!-- Container หลักของคีย์บอร์ด -->
    <div class="inline-block rounded-xl border border-gray-300 bg-gray-200 p-2 shadow-lg sm:p-3">
        <!-- กล่องครอบสำหรับจัดแถวและระยะห่าง -->
        <div class="flex flex-col items-center gap-1.5 sm:gap-2">
            <!-- วนลูปสร้างแต่ละแถวของคีย์บอร์ด -->
            <div v-for="(row, rowIndex) in rows" :key="`row-${rowIndex}`" class="flex justify-center gap-1.5 sm:gap-2"
                :class="getRowIndent(rowIndex)">
                <!-- วนลูปสร้างแต่ละปุ่มในแถว -->
                <div v-for="keyId in row" :key="keyId" :class="[
                    getKeyBaseClasses(keyId),
                    getKeySizeClasses(keyId),
                    { 'key-pressed': pressedKey === keyId } // คลาสสำหรับตอนกด
                ]"
                    class="flex items-center justify-center rounded-md font-sans text-xs font-medium transition-all duration-75 sm:text-sm"
                    @mousedown="handleKeyClick(keyId)" @mouseup="pressedKey = null">
                    <!-- แสดงตัวอักษรหรือสัญลักษณ์ของคีย์ -->
                    <span class="text-center">{{ getKeyDisplay(keyId) }}</span>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'InteractiveKeyboard',
    data() {
        return {
            // ใช้ key ที่ไม่ซ้ำกันเป็น ID สำหรับแต่ละปุ่ม
            rows: [
                ['`', '1', '2', '3', '4', '5', '6', '7', '8', '9', '0', '-', '=', 'Backspace'],
                ['Tab', 'q', 'w', 'e', 'r', 't', 'y', 'u', 'i', 'o', 'p', '[', ']', '\\'],
                ['CapsLock', 'a', 's', 'd', 'f', 'g', 'h', 'j', 'k', 'l', ';', "'", 'Enter'],
                ['ShiftLeft', 'z', 'x', 'c', 'v', 'b', 'n', 'm', ',', '.', '/', 'ShiftRight'],
                ['ControlLeft', 'MetaLeft', 'AltLeft', 'Space', 'AltRight', 'MetaRight', 'ContextMenu', 'ControlRight'],
            ],
            // state สำหรับติดตามปุ่มที่ถูกกด
            pressedKey: null,
            // Map event.code ไปยัง keyId ของเรา เพื่อความแม่นยำ
            // event.code จะไม่สนใจ layout คีย์บอร์ด (เช่น Dvorak) และตัวพิมพ์ใหญ่/เล็ก
            keymap: {
                'Backquote': '`', 'Digit1': '1', 'Digit2': '2', 'Digit3': '3', 'Digit4': '4', 'Digit5': '5', 'Digit6': '6', 'Digit7': '7', 'Digit8': '8', 'Digit9': '9', 'Digit0': '0', 'Minus': '-', 'Equal': '=', 'Backspace': 'Backspace',
                'Tab': 'Tab', 'KeyQ': 'q', 'KeyW': 'w', 'KeyE': 'e', 'KeyR': 'r', 'KeyT': 't', 'KeyY': 'y', 'KeyU': 'u', 'KeyI': 'i', 'KeyO': 'o', 'KeyP': 'p', 'BracketLeft': '[', 'BracketRight': ']', 'Backslash': '\\',
                'CapsLock': 'CapsLock', 'KeyA': 'a', 'KeyS': 's', 'KeyD': 'd', 'KeyF': 'f', 'KeyG': 'g', 'KeyH': 'h', 'KeyJ': 'j', 'KeyK': 'k', 'KeyL': 'l', 'Semicolon': ';', 'Quote': "'", 'Enter': 'Enter',
                'ShiftLeft': 'ShiftLeft', 'KeyZ': 'z', 'KeyX': 'x', 'KeyC': 'c', 'KeyV': 'v', 'KeyB': 'b', 'KeyN': 'n', 'KeyM': 'm', 'Comma': ',', 'Period': '.', 'Slash': '/', 'ShiftRight': 'ShiftRight',
                'ControlLeft': 'ControlLeft', 'MetaLeft': 'MetaLeft', 'AltLeft': 'AltLeft', 'Space': 'Space', 'AltRight': 'AltRight', 'MetaRight': 'MetaRight', 'ContextMenu': 'ContextMenu', 'ControlRight': 'ControlRight'
            },
        };
    },
    mounted() {
        // เพิ่ม event listeners เมื่อ component ถูก mount
        window.addEventListener('keydown', this.handleKeyDown);
        window.addEventListener('keyup', this.handleKeyUp);
    },
    beforeUnmount() {
        // **สำคัญ**: ใน Vue 3/Nuxt 3 ใช้ beforeUnmount แทน beforeDestroy
        // ลบ event listeners เพื่อป้องกัน memory leaks
        window.removeEventListener('keydown', this.handleKeyDown);
        window.removeEventListener('keyup', this.handleKeyUp);
    },
    methods: {
        handleKeyDown(event) {
            // หยุดการทำงานปกติของ Browser (เช่น กด F5, Ctrl+R)
            // event.preventDefault();

            const keyId = this.keymap[event.code];
            if (keyId) {
                this.pressedKey = keyId;
                // ส่ง event ไปยัง parent component
                this.$emit('key-pressed', {
                    key: keyId,
                    display: this.getKeyDisplay(keyId),
                    code: event.code,
                    timestamp: new Date().toISOString()
                });
            }
        },

        handleKeyUp(event) {
            // event.preventDefault();
            const keyId = this.keymap[event.code];
            // ตรวจสอบว่าปุ่มที่ปล่อยเป็นปุ่มเดียวกับที่กดค้างไว้
            if (keyId && this.pressedKey === keyId) {
                this.pressedKey = null;
            }
        },

        handleKeyClick(keyId) {
            // ทำให้การคลิกเมาส์มี effect เหมือนการกดคีย์บอร์ด
            this.pressedKey = keyId;
            this.$emit('key-pressed', {
                key: keyId,
                display: this.getKeyDisplay(keyId),
                code: `mouse_click_${keyId}`,
                timestamp: new Date().toISOString()
            });
            // อาจจะ set timeout เพื่อยกเลิก pressedKey หลังคลิก
            // setTimeout(() => { this.pressedKey = null; }, 100);
        },

        getKeyBaseClasses(keyId) {
            // Special keys (สีเทา)
            if (['Tab', 'CapsLock', 'Enter', 'Backspace', 'ContextMenu'].includes(keyId) || keyId.includes('Shift') || keyId.includes('Control') || keyId.includes('Alt') || keyId.includes('Meta')) {
                return 'bg-gray-300 text-gray-800 border-b-2 border-gray-400';
            }
            // Space bar
            if (keyId === 'Space') {
                return 'bg-gray-300 text-gray-800 border-b-2 border-gray-400';
            }
            // Regular keys (สีขาว)
            return 'bg-white text-gray-700 border-b-2 border-gray-300';
        },

        getKeySizeClasses(keyId) {
            const baseSize = 'h-10 w-10 sm:h-12 sm:w-12';
            switch (keyId) {
                case 'Backspace': return 'w-24';
                case 'Tab': return 'w-20';
                case 'CapsLock': return 'w-24';
                case 'Enter': return 'w-28';
                case 'ShiftLeft': return 'w-32';
                case 'ShiftRight': return 'w-32';
                case 'Space': return 'flex-grow min-w-[250px]';
                default: return baseSize;
            }
        },

        getRowIndent(rowIndex) {
            // ไม่มีการเยื้องสำหรับแถวแรกและแถวตัวเลข
            if (rowIndex === 0) return 'ml-[0px]'; // Numbers
            if (rowIndex === 1) return 'ml-[20px]'; // QWERTY
            if (rowIndex === 2) return 'ml-[30px]'; // ASDF
            if (rowIndex === 3) return 'ml-[45px]'; // ZXCV
            return '';
        },

        getKeyDisplay(keyId) {
            const displayMap = {
                'ShiftLeft': 'Shift', 'ShiftRight': 'Shift',
                'ControlLeft': 'Ctrl', 'ControlRight': 'Ctrl',
                'AltLeft': 'Alt', 'AltRight': 'Alt',
                'MetaLeft': 'Win', 'MetaRight': 'Win',
                'CapsLock': 'Caps',
                'Enter': '↵',
                'Backspace': '⌫',
                'ContextMenu': '☰',
                'Space': '',
            };
            return displayMap[keyId] || keyId;
        },
    },
};
</script>

<style scoped>
/* สไตล์สำหรับปุ่มเมื่อถูกกด */
.key-pressed {
    /* ใช้ transform เพื่อทำให้ปุ่มดูเหมือนยุบลงไป */
    transform: translateY(2px);
}
</style>