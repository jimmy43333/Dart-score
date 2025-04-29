<template>
  <div class="out-box">
    <div class="board-box">
      <Board @update_score="round_handle" />
    </div>
    <div class="nav-box">
      <div class="overlap-1">
        <ion-icon :icon="reloadOutline" class="icon" @click="back()" />
        <ion-icon :icon="exitOutline" class="icon" @click="reset()" />
      </div>
      <div class="overlap-2">
        <div class="text-wrapper">Round {{ current_round }}/10</div>
      </div>
      <div class="overlap-3">
        <div class="text-wrapper">{{ dart_record[0][0] }} {{ dart_record[0][1] }}</div>
      </div>
      <div class="overlap-3">
        <div class="text-wrapper">{{ dart_record[1][0] }} {{ dart_record[1][1] }}</div>
      </div>
      <div class="overlap-3">
        <div class="text-wrapper">{{ dart_record[2][0] }} {{ dart_record[2][1] }}</div>
      </div>
    </div>
    <div class="player-box">
      <Players
        v-for="obj in player"
        :key="obj.index"
        :name="obj.name"
        :score="obj.score"
        :index="obj.index"
        :current="player_index"
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
const dart_record = ref([
  ['', ''],
  ['', ''],
  ['', '']
])
const player = ref([
  {
    index: 0,
    name: 'Jimmy',
    score: 30,
    finished: false,
    detail: []
  },
  {
    index: 1,
    name: 'Jeff',
    score: 301,
    finished: false,
    detail: []
  },
  {
    index: 2,
    name: 'HAHAHA',
    score: 301,
    finished: false,
    detail: []
  },
  {
    index: 3,
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

function game_over() {
  if (current_round.value == 10 && player_index.value == 3) {
    console.log('No one finish')
  }
  if (player.value[player_index.value].score == 0) {
    console.log('Game over')
  }
}

function round_handle(s, m) {
  // console.log(s, m)
  if (dart_count.value == 0) {
    dart_record.value = [
      ['', ''],
      ['', ''],
      ['', '']
    ]
  }
  if (s == 50) {
    dart_record.value[dart_count.value][0] = 'Bull Eye !!!'
  } else if (m == 1) {
    dart_record.value[dart_count.value][0] = 'Single'
    dart_record.value[dart_count.value][1] = s
  } else if (m == 2) {
    dart_record.value[dart_count.value][0] = 'Double'
    dart_record.value[dart_count.value][1] = s
  } else if (m == 3) {
    dart_record.value[dart_count.value][0] = 'Triple'
    dart_record.value[dart_count.value][1] = s
  }
  const score = s * m
  dart_count.value += 1
  let reserve = player.value[player_index.value].score
  let reserve_index = player_index.value
  player.value[player_index.value].score -= score
  if (player.value[player_index.value].score < 0) {
    setTimeout(() => {
      player.value[reserve_index].score = reserve
    }, 2000)
  }
  let round_index = current_round.value - 1
  player.value[player_index.value].detail[round_index][dart_count.value] = score

  game_over()

  if (dart_count.value == 3) {
    next_player()
    dart_count.value = 0
  }
}

function back() {
  console.log('back')
}

function reset() {
  console.log('reset')
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
  align-items: end;
}
</style>
