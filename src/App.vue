<template>
    <div class="page">
        <p class="title"> Box Hunter</p>
        <div class="GameTable">
            <Hands color="orange" :Boxes=Boxes0 @data-from-child="receiveDataFromChild"
                :pickedIndex=BoxInHandsPickedStatus0>
            </Hands>
            <Checkerboard :BoardData=BoardData @data-from-child="receiveDataFromChild" :pickedIndex=BoxInBoardPickedStatus>
            </Checkerboard>
            <Hands color="darkred" :Boxes=Boxes1 @data-from-child="receiveDataFromChild"
                :pickedIndex=BoxInHandsPickedStatus1>
            </Hands>
        </div>
        <p class="warn">{{ warn }}</p>
        <p :style="{ color: msgColor }" class="msg">{{ msg }}</p>
        <button v-if="this.msg.includes('胜利') || this.msg.includes('平局')" class="button" @click="refresh">重新开始</button>
        <hr style="width: 100%;">
        <p class="rule_title">规则介绍</p>
        <p class="rule_des" v-for="item in des">{{ item }}</p>
    </div>
    <footer>
        <p>此项目开源于 <a href="https://github.com/Coooolfan/BoxHunter">https://github.com/Coooolfan/BoxHunter</a></p>
    </footer>
</template>

<script>
import Checkerboard from './components/checkerboard.vue'
import Hands from './components/Hands.vue'
export default {
    components: {
        Checkerboard,
        Hands
    },

    data() {
        return {
            msg: "橙方回合",
            warn: "",
            BoardData: [
                ["", "", ""],
                ["", "", ""],
                ["", "", ""]
            ],
            Boxes0: [2, 2, 2],
            Boxes1: [2, 2, 2],
            currentTurn: "orange",
            pickedBox: {
                "color": "none",
                "size": -1,
                "source": "none",
                "index": "-1",
            },
            BoxInHandsPickedStatus0: -1,
            BoxInHandsPickedStatus1: -1,
            BoxInBoardPickedStatus: "-1",
            des: ["每一回合每一方都可以在棋盘上放置一颗自己的棋子",
                "可以放置在空白处，也可以选择吃掉对方比自己小的棋子、并占据对方的位置",
                "吃掉的棋子不会归还对方"]

        }
    },
    mounted() {
        if (/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent) && Math.abs(window.orientation) !== 90) {
            // 移动设备
            this.warn = "您似乎正在使用移动设备，建议您以横屏模式继续";
        } else {
            // 不是移动设备
        }
    },
    methods: {
        receiveDataFromChild(data) {
            this.receivedData = data;
            if (data.color === this.currentTurn && data.source === "Hands") {
                if (data.color === "orange" && this.Boxes0[data.index] === 0)
                    return;
                if (data.color === "darkred" && this.Boxes1[data.index] === 0)
                    return;
                // 选中了自己的手棋且当前没有选中棋子且手棋不为0
                this.pickedBox.source = "Hands";
                this.pickedBox.index = data.index;
                this.pickedBox.color = data.color;
                this.pickedBox.size = data.index;
                this.updatePickedStatus();
            } else if (data.source === "Board" && (this.pickedBox.color === "orange" || this.pickedBox.color === "darkred")) {
                // 选中了棋盘上的格子且当前有选中棋子
                if (this.BoardData[data.line][data.column].length === 0) {
                    // 选中空的
                    this.BoardData[data.line][data.column] = this.getCodeFromPickedData();
                    if (this.currentTurn === "orange") {
                        this.Boxes0[parseInt(this.pickedBox.index)]--;
                    } else {
                        this.Boxes1[parseInt(this.pickedBox.index)]--;
                    }
                    this.cleanPickedData();
                    this.currentTurn = this.currentTurn === "orange" ? "darkred" : "orange";
                    this.msg = this.currentTurn === "orange" ? "橙方回合" : "红方回合";
                    this.checkEnd();
                } else if ((((this.BoardData[data.line][data.column][0] === '0') ? "orange" : "darkred") != this.currentTurn) && (this.pickedBox.size > parseInt(this.BoardData[data.line][data.column][1]))) {
                    // 选中对手的且比对手的大
                    this.BoardData[data.line][data.column] = this.getCodeFromPickedData();
                    if (this.currentTurn === "orange") {
                        this.Boxes0[parseInt(this.pickedBox.index)]--;
                    } else {
                        this.Boxes1[parseInt(this.pickedBox.index)]--;
                    }
                    this.cleanPickedData();
                    this.currentTurn = this.currentTurn === "orange" ? "darkred" : "orange";
                    this.msg = this.currentTurn === "orange" ? "橙方回合" : "红方回合";
                    this.checkEnd();
                }
            }
        },
        updatePickedStatus() {
            if (this.pickedBox.source === "none") {
                this.BoxInHandsPickedStatus0 = -1;
                this.BoxInHandsPickedStatus1 = -1;
                this.BoxInBoardPickedStatus = "-1";
                return;
            }
            if (this.pickedBox.source === "Hands") {
                if (this.pickedBox.color === "orange") {
                    this.BoxInHandsPickedStatus0 = this.pickedBox.index;
                } else if (this.pickedBox.color === "darkred") {
                    this.BoxInHandsPickedStatus1 = this.pickedBox.index;
                }
            } else if (this.pickedBox.source === "Board") {
                this.BoxInBoardPickedStatus = this.pickedBox.index;
            }
        },
        cleanPickedData() {
            this.pickedBox.source = "none";
            this.pickedBox.index = "-1";
            this.pickedBox.color = "none";
            this.pickedBox.size = -1;
            this.updatePickedStatus();
        },
        getCodeFromPickedData() {
            let res = "";
            if (this.pickedBox.color === "orange") {
                res += "0";
            } else if (this.pickedBox.color === "darkred") {
                res += "1";
            }
            if (this.pickedBox.size != -1) {
                res += this.pickedBox.size;
            }
            return res;
        },
        // 检查是否结束
        checkEnd() {
            // 检查是否有任何一行或者一列是相同颜色
            for (let i = 0; i < 3; i++) {
                if (this.BoardData[i][0][0] === this.BoardData[i][1][0] && this.BoardData[i][1][0] === this.BoardData[i][2][0]
                    && this.BoardData[i][0] !== "" && this.BoardData[i][1] !== "" && this.BoardData[i][2] !== "") {
                    this.msg = this.BoardData[i][0][0] === "0" ? "橙方胜利" : "红方胜利";
                    console.log(this.BoardData);
                    return;
                }
            }
            for (let i = 0; i < 3; i++) {
                if (this.BoardData[0][i][0] === this.BoardData[1][i][0] && this.BoardData[1][i][0] === this.BoardData[2][i][0]
                    && this.BoardData[0][i] !== "" && this.BoardData[1][i] !== "" && this.BoardData[2][i] !== "") {
                    this.msg = this.BoardData[0][i][0] === "0" ? "橙方胜利" : "红方胜利";
                    console.log(this.BoardData);
                    return;
                }
            }
            let count = 0;
            for (let i = 0; i < 3; i++) {
                for (let j = 0; j < 3; j++)
                    count += this.BoardData[i][j].length;
            }
            if (count === 9 * 2)
                this.msg = "平局";
        },
        refresh() {
            location.reload();
        }
    },
    computed: {
        msgColor() {
            if (this.msg === "红方回合")
                return "darkred";
            else if (this.msg === "橙方回合")
                return "orange";
            else if (this.msg === "红方胜利")
                return "darkred";
            else if (this.msg === "橙方胜利")
                return "orange";
            else
                return "black";
        }
    }


}
</script>

<style scoped>
.page {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

.GameTable {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    margin-top: 10vh;
}

.title {
    font-size: 50px;
    margin-top: 5vh;
}

.msg {
    font-size: 30px;
    text-align: center;
    margin-top: 10vh;
}

.rule_title {
    font-size: 30px;
    margin-top: 5vh;
}

.rule_des {
    font-size: 18px;
    color: gray;
}


.button {
    display: inline-block;
    padding: 12px 24px;
    font-size: 16px;
    font-weight: bold;
    text-align: center;
    text-decoration: none;
    border-radius: 4px;
    border: none;
    background-color: #4caf50;
    color: #fff;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

.button:hover {
    background-color: #45a049;
}

.button:active {
    background-color: #3e8e41;
}

.button:focus {
    outline: none;
    box-shadow: 0 0 0 2px rgba(0, 0, 0, 0.2);
}

footer {
    background-color: #f8f8f8;
    padding: 2px;
    text-align: center;
    font-size: 14px;
    color: #666;
}

footer a {
    color: #333;
    text-decoration: underline;
}

footer a:hover {
    color: #000;
}
</style>
