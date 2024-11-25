<template>
  <!-- <img alt="logo" class="logo" src="./assets/electron.svg" />
  <div class="creator">Powered by electron-vite</div>
  <div class="text">
    Build an Electron app with
    <span class="vue">Vue</span>
  </div>
  <p class="tip">Please try pressing <code>F12</code> to open the devTool</p>
  <div class="actions">
    <div class="action">
      <a href="https://electron-vite.org/" target="_blank" rel="noreferrer">Documentation</a>
    </div>
    <div class="action">
      <a target="_blank" rel="noreferrer" @click="ipcHandle">Send IPC</a>
    </div>
  </div> -->
  <Board @update_score="roundHandle" />
  <Players :player="player" :current-round="current_round" :current-player="player_index" />
</template>

<script setup>
import { ref, onMounted } from 'vue'
import Board from './components/Board.vue'
import Players from './components/Player.vue'
// const ipcHandle = () => window.electron.ipcRenderer.send('ping')
const current_round = ref(1)
const player_index = ref(0)
const dart_count = ref(0)
const player = ref([
  {
    index: 0,
    name: 'Jimmy',
    score: 0,
    finished: false,
    detail: []
  },
  {
    index: 1,
    name: '無敵破壞王',
    score: 0,
    finished: false,
    detail: []
  },
  {
    index: 2,
    name: '一劍開天門',
    score: 0,
    finished: false,
    detail: []
  },
  {
    index: 3,
    name: 'Dark Night',
    score: 0,
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
.control-button {
  position: absolute;
  left: 0;
  bottom: 0;
  z-index: 100;
}
</style>
