
<template>
    <div class="Board">
        <div v-for="(item, line) in BoardData" class="Boardline">
            <Box v-for="(item_j, column) in item" :color="getBoxDetail(item_j).color" :size="getBoxDetail(item_j).size"
                :picked="isPicked(line,column)" @data-from-child="passDataToGrandfather(line, column)">
            </Box>
        </div>
    </div>
</template>

<script>
import Box from './Box.vue';
export default {
    name: 'Checkerboard',
    components: {
        Box
    },
    data() {
        return {
            BoxPicked: [
                [false, false, false],
                [false, false, false],
                [false, false, false]
            ]
        }
    },
    methods: {
        getBoxDetail(source) {
            var color = "none";
            var size = -1;
            if (source[0] === "0") {
                color = "orange";
            } else if (source[0] === "1") {
                color = "darkred";
            }
            if (source.length === 2) {
                size = parseInt(source[1]);
            }
            return {
                "color": color,
                "size": size
            }
        },
        passDataToGrandfather(line, column) {
            this.$emit('data-from-child', {
                "source": "Board",
                "line": line,
                "column": column,
                "color": this.getBoxDetail(this.BoardData[line][column]).color,
            });
        },
        isPicked(line,column) {
            if(this.pickedIndex === -1)
                return false;

            return parseInt(this.pickedIndex[0])===line && parseInt(this.pickedIndex[1]===column)
        }
    },
    props: {
        BoardData: {
            type: Array,
            required: true,
            default: () => [
            ]
        },
        pickedIndex: {
            type: String,
            required: true,
            default: -1
        }
    }

}
</script>

<style scoped>
.Board {
    margin: 50px;
}

.Boardline {
    display: flex;
    /* 横向 */
    flex-direction: row;
}
</style>
