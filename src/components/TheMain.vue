<script setup>

import { ref, computed, onMounted } from "vue";

onMounted(() => { document.onselectstart = () => false; })


class Panel {
    num;
    x;
    y;
    correctX;
    correctY;
    constructor(num, x, y) {
        this.num = num;
        this.x = x;
        this.y = y;
        this.correctX = x;
        this.correctY = y;
    }
}

const col = ref(3);
const row = ref(3);

const colN = ref(3);
const rowN = ref(3);

const imgNo = ref(0);

const count = ref(0);

const p1 = new Panel(1, 0, 0);
const p2 = new Panel(2, 1, 0);
const p3 = new Panel(3, 2, 0);
const p4 = new Panel(4, 0, 1);
const p5 = new Panel(5, 1, 1);
const p6 = new Panel(6, 2, 1);
const p7 = new Panel(7, 0, 2);
const p8 = new Panel(8, 1, 2);
const p9 = new Panel(9, 2, 2);




const panels = ref([p1, p2, p3, p4, p5, p6, p7, p8]);

const emptyPanel = ref(p9);

const isClear = computed(() => {
    return panels.value.every((p) => {
        return p.x == p.correctX && p.y == p.correctY
    })
})



function movePanel(panel) {
    const empX = emptyPanel.value.x;
    const empY = emptyPanel.value.y;
    if (isClear.value) {
        return;
    }

    if (empX == panel.x && empY > panel.y) {

        emptyPanel.value.y = panel.y;
        for (let p of panels.value) {
            if (p.x == empX && p.y < empY && panel.y <= p.y) {
                p.y++;
            }
        }
        count.value++;
    } else if (empX == panel.x && empY < panel.y) {
        emptyPanel.value.y = panel.y;
        for (let p of panels.value) {
            if (p.x == empX && p.y > empY && panel.y >= p.y) {
                p.y--;
            }
        }
        count.value++;
    } else if (empY == panel.y && empX > panel.x) {

        emptyPanel.value.x = panel.x;
        for (let p of panels.value) {
            if (p.y == empY && p.x < empX && panel.x <= p.x) {
                p.x++;
            }
        }
        count.value++;
    } else if (empY == panel.y && empX < panel.x) {
        emptyPanel.value.x = panel.x;
        for (let p of panels.value) {
            if (p.y == empY && p.x > empX && panel.x >= p.x) {
                p.x--;
            }
        }
        count.value++;
    }
}

function shuffle() {

    count.value = 0;

    colN.value = col.value;
    rowN.value = row.value;

    panels.value = [];
    let n = 0;
    for (let r = 0; r < row.value; r++) {
        for (let c = 0; c < col.value; c++) {

            if (c + 1 == col.value && r + 1 == row.value) {
                break;
            }
            n++;
            panels.value.push(new Panel(n, c, r))
        }
    }
    console.log(col.value, row.value);
    emptyPanel.value = new Panel(col.value * row.value, col.value - 1, row.value - 1)

    for (let i = 0; i < 100; i++) {
        randomExchange();
    }
}

function randomExchange() {
    const length = panels.value.length;

    const r1 = Math.floor(Math.random() * length);
    const r2 = (r1 + Math.floor(Math.random() * (length - 1)) + 1) % length;

    exchange(panels.value[r1], panels.value[r2]);
}

function exchange(p, q) {
    let a, b;
    a = p.x; p.x = q.x; q.x = a;
    b = p.y; p.y = q.y; q.y = b;
}

const panelStyle = (panel) => {
    return {
        "left": panel.x * 100 / colN.value + '%',
        'top': panel.y * 100 / rowN.value + '%',
        'width': 100 / colN.value + '%',
        'height': 100 / rowN.value + '%',
        'background-image': `url("/valid_${imgNo.value}.jpg")`,
        "background-position": panel.correctX * 100 / (colN.value - 1) + '% ' + panel.correctY * 100 / (rowN.value - 1) + '%'
    }
    //"url('../assets/valid_3.jpg')"だとなぜかダメ。
}


</script>

<template>
    <div class="container">
        <div class="field">

            <div v-for="panel in panels" class="panel" :style=panelStyle(panel) @click="movePanel(panel)">{{ panel.num
                }}
            </div>

            <div class="emptyPanel" :class="{ visible: isClear }" :style=panelStyle(emptyPanel)>{{ emptyPanel.num }}
            </div>
        </div>
        <div class="flex"><button class="shuffle" @click="shuffle">Shuffle!</button>
            <button class="imgChange" @click="imgNo = (imgNo + 1) % 4">画像切り替え</button>
        </div>
        <div class="flex">
            <span>列数：<input class="num" type="number" v-model="col" max="10"></span>
            <span>行数：<input class="num" type="number" v-model="row" max="10"></span>
        </div>


        <p class="count">count:{{ count }}</p>
        <!-- <p v-if="isClear">Clear!!</p> -->
    </div>
</template>

<style scoped>
.container {
    width: max-content;
    margin: 0 auto;
    position: relative;
}

.field {
    height: min(500px, 70vh);
    width: min(500px, 70vh);
    position: relative;
    border: 5px solid silver;
}

.panel,
.emptyPanel {
    background-size: min(500px, 70vh) min(500px, 70vh);
    display: grid;
    place-content: center;
    position: absolute;
    transition: 0.5s;
}

.emptyPanel {
    opacity: 0;
}

.visible {
    opacity: 1;
}

input.num {
    width: 40px;
}

p {
    text-align: center;
}

.flex {
    display: flex;
    gap: 30px;
    justify-content: center;
    margin-top: 15px;
}

button {
    cursor: pointer;
    background-color: rgb(53, 120, 178);
    color: white;
    border: none;
    border-radius: 5px;
    padding: 2px 5px;
}

p.count {
    position: absolute;
    top: -60px;
    left: calc(50% - 30px);
    font-size: 1.2rem;
}
</style>