<template>
    <span class="outer-rectangle" :class="picked_color" @click="sendDataToParent">
        <span :class="['inner-square', color, 'size-' + size]"></span>
    </span>
</template>

<script >
export default {
    name: 'Box',
    computed: {
        picked_color() {
            return this.picked ? 'picked_' + this.color : ''
        }
    },
    data() {
        return {
        }
    },
    props: {
        color: {
            type: String,
            required: true,
            // 枚举值
            validator: (value) => {
                return ['orange', 'darkred', 'none'].includes(value)
            }
        },
        size: {
            type: Number,
            required: true,
            // 自定义校验规则
            validator: (value) => {
                return value >= -1 && value <= 2
            }
        },
        picked: {
            type: Boolean,
            required: true,
            default: false
        }
    },
    methods: {
        // 向上层传递点击事件
        sendDataToParent() {
            this.$emit('data-from-child', 'Hello, msg from Box!');
        }
    }
}
</script>
<style scoped>
.outer-rectangle {
    width: 50px;
    height: 50px;
    border-radius: 5px;
    background-color: lightgray;
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 2px;
}

.picked_orange {
    box-shadow: 0 0 2px 2px orange,
        inset 0 0 8px 5px rgba(255, 255, 255, 0.8);
}

.picked_darkred {
    box-shadow: 0 0 2px 2px darkred,
        inset 0 0 8px 5px rgba(255, 255, 255, 0.8);
}

.inner-square {
    border-radius: 3px;
}

.orange {
    background-color: orange;
}

.darkred {
    background-color: darkred;
}

.size-0 {
    padding: 5px;
}

.size-1 {
    padding: 12px;
}

.size-2 {
    padding: 18px;
}
</style>
