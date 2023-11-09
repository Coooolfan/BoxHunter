<template>
    <div class="biggest">
        <div v-for="item, index in Boxes" class="boxes-in-hands-des">
            <Box :color="color" :size="index" :picked="isPicked(index)" @data-from-child="passDataToGrandfather(index)">
            </Box>
            <span class="boxes-last">{{ "剩余" + Boxes[index] + "个" }}</span>
        </div>

    </div>
</template>

<script>
import Box from './Box.vue';
export default {
    name: 'Hands',
    components: {
        Box
    },
    data() {
        return {
        }
    },
    props: {
        Boxes: {
            type: Array,
            required: true,
            default: () => [2, 2, 2]
        },
        color: {
            type: String,
            required: true,
            // 枚举值
            validator: (value) => {
                return ['orange', 'darkred'].includes(value)
            }
        },
        pickedIndex: {
            type: Number,
            required: true,
            default: -1
        }
    },
    methods: {
        passDataToGrandfather(index) {
            this.$emit('data-from-child', {
                "source": "Hands",
                "color": this.color,
                "index": index
            });
        },
        isPicked(index) {
            return this.pickedIndex === index;
        }
    }
}
</script>

<style scoped>
.boxes-in-hands-des {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
}

.boxes-last {
    margin-left: 15px;
    font-weight: bolder;
}
</style>
