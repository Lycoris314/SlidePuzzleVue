<script setup>

import { ref, computed } from "vue";

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
        return p.x == p.correctX && p.y == p.correctY })
})



function movePanel(panel) {
    const empX = emptyPanel.value.x;
    const empY = emptyPanel.value.y;
    if(isClear.value){
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
    // emptyPanel.value.x = panel.x;
    // emptyPanel.value.y = panel.y;
    // panel.x = empX;
    // panel.y = empY;
}

function shuffle() {

    count.value=0;

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
    //console.log(panels.value[r1],r2)
    exchange(panels.value[r1], panels.value[r2]);
}

function exchange(p, q) {
    let a, b;
    a = p.x; p.x = q.x; q.x = a;
    b = p.y; p.y = q.y; q.y = b;
    //console.log(p.x,q.x,p.y,q.y)
}


</script>

<template>
    <div class="field">

        <div v-for="panel in panels" class="panel" :style="{
            'left': panel.x * 100 / colN + '%',
            'top': panel.y * 100 / rowN + '%',
            'width': 100 / colN + '%',
            'height': 100 / rowN + '%'
        }" @click="movePanel(panel)">{{ panel.num }}</div>

        <div class="emptyPanel" :class="{ visible: isClear }" :style="{
            'left': emptyPanel.x * 100 / colN + '%',
            'top': emptyPanel.y * 100 / rowN + '%',
            'width': 100 / colN + '%',
            'height': 100 / rowN + '%'
        }">{{ emptyPanel.num }}</div>
    </div>

    <p>列数：<input type="number" v-model="col" max="10"></p>
    <p>行数：<input type="number" v-model="row" max="10"></p>

    <button @click="shuffle">Shuffle</button>
    <p>count:{{ count }}</p>
    <p v-if="isClear">Clear!!</p>
</template>

<style scoped>
.field {
    height: 500px;
    width: 500px;
    position: relative;
}

.panel,
.emptyPanel {
    background-color: pink;
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
</style>