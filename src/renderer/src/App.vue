<template>
  <div class="out-box">
    <div class="board-box">
      <Board />
    </div>
    <div class="nav-box">
      <div class="overlap-1">
        <ion-icon :icon="reloadOutline" class="icon" />
        <ion-icon :icon="exitOutline" class="icon" />
      </div>
      <div class="overlap-2">
        <div class="text-wrapper">Round 10/10</div>
      </div>
      <div class="overlap-3">
        <div class="text-wrapper">Single 20</div>
      </div>
      <div class="overlap-3">
        <div class="text-wrapper">Tripple 10</div>
      </div>
      <div class="overlap-3">
        <div class="text-wrapper">Double 13</div>
      </div>
    </div>
    <div class="player-box">
      <Players
        v-for="obj in player"
        :key="obj.index"
        :name="obj.name"
        :score="obj.score"
        :index="obj.index"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { IonIcon } from '@ionic/vue'
import { exitOutline, reloadOutline } from 'ionicons/icons'
import Board from './components/Board.vue'
import Players from './components/Player.vue'
// const ipcHandle = () => window.electron.ipcRenderer.send('ping')
const current_round = ref(1)
const player_index = ref(0)
const dart_count = ref(0)
const player = ref([
  {
    index: 1,
    name: 'Jimmy',
    score: 301,
    finished: false,
    detail: []
  },
  {
    index: 2,
    name: 'Jeff',
    score: 301,
    finished: false,
    detail: []
  },
  {
    index: 3,
    name: 'HAHAHA',
    score: 301,
    finished: false,
    detail: []
  },
  {
    index: 4,
    name: 'Dark Night',
    score: 301,
    finished: false,
    detail: []
  }
])

function next_player() {
  player_index.value += 1
  if (player_index.value == 4) {
    current_round.value += 1
  }
  player_index.value = player_index.value % 4
  if (!player.value[player_index.value].finished) {
    return player_index.value
  } else {
    next_player()
  }
}

function roundHandle(score) {
  console.log(score)
  dart_count.value += 1
  player.value[player_index.value].score += score
  let round_index = current_round.value - 1
  player.value[player_index.value].detail[round_index][dart_count.value] = score

  if (dart_count.value == 3) {
    next_player()
    dart_count.value = 0
  }
}

onMounted(() => {
  let result = []
  for (let i = 1; i <= 10; i++) {
    result.push([i, '', '', ''])
  }
  for (let ele of player.value) {
    ele.detail = JSON.parse(JSON.stringify(result))
  }
})
</script>
<style lang="less">
.out-box {
  display: flex;
  background-color: antiquewhite;
  justify-content: center;
}

.board-box {
  width: 65%;
  height: 65%;
  position: absolute;
  top: 30px;
  left: 30px;
  background-color: rgb(93, 93, 93);
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
}

.nav-box {
  width: 30%;
  height: 65%;
  position: absolute;
  top: 30px;
  right: 0;
  display: flex;
  flex-direction: column;
  align-items: end;
}

.overlap-1 {
  background-color: #3d3c3c;
  border-radius: 10px 0px 0px 10px;
  width: 120px;
  height: 50px;
  padding: 10px;
  margin: 5px;
  margin-right: 0px;
  display: flex;
  justify-content: space-around;

  .icon {
    font-size: 2em;

    &:hover {
      transform: scale(1.2);
    }
  }
}

.overlap-2 {
  background-color: #3d3c3c;
  border-radius: 10px 0px 0px 10px;
  width: 170px;
  height: 70px;
  padding: 10px;
  margin: 5px;
  margin-right: 0px;
  margin-bottom: 30px;
}

.overlap-3 {
  background-color: #3d3c3c;
  border-radius: 10px 0px 0px 10px;
  width: 170px;
  height: 70px;
  padding: 10px;
  margin: 5px;
  margin-right: 0px;
}

.text-wrapper {
  color: #ffffff;
  font-family: 'Itim-Regular', Helvetica;
  font-size: 25px;
  font-weight: 600;
  letter-spacing: 0;
  line-height: 50px;
  white-space: nowrap;
  width: 71px;
}

.player-box {
  position: absolute;
  bottom: 0;
  width: 95%;
  display: flex;
  justify-content: space-around;
}
</style>
