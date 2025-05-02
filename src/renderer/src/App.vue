<template>
  <div class="out-box">
    <transition name="slide-up">
      <div v-if="!active" class="setting-box">
        <div class="title">Dart Score</div>
        <div class="options">
          <div class="option-title">Players</div>
          <div class="option-value">
            <ion-icon
              :icon="chevronBackCircleOutline"
              class="icon"
              @click="change_index('players', player_option.length, false)"
            />
            <div class="value-text">{{ player_option[index.players] }}</div>
            <ion-icon
              :icon="chevronForwardCircleOutline"
              class="icon"
              @click="change_index('players', player_option.length, true)"
            />
          </div>
          <div class="option-title">Score</div>
          <div class="option-value">
            <ion-icon
              :icon="chevronBackCircleOutline"
              class="icon"
              @click="change_index('score', score_option.length, false)"
            />
            <div class="value-text">{{ score_option[index.score] }}</div>
            <ion-icon
              :icon="chevronForwardCircleOutline"
              class="icon"
              @click="change_index('score', score_option.length, true)"
            />
          </div>
          <div class="option-title">Round</div>
          <div class="option-value">
            <ion-icon
              :icon="chevronBackCircleOutline"
              class="icon"
              @click="change_index('round', round_option.length, false)"
            />
            <div class="value-text">{{ round_option[index.round] }}</div>
            <ion-icon
              :icon="chevronForwardCircleOutline"
              class="icon"
              @click="change_index('round', round_option.length, true)"
            />
          </div>
        </div>
        <div class="start-button" @click="start">START</div>
      </div>
    </transition>
    <div v-if="active" class="board-box">
      <Board :score="player[index.current_player].score" @update_score="score_computed" />
    </div>
    <div v-if="active" class="nav-box">
      <div class="overlap-1">
        <ion-icon :icon="reloadOutline" class="icon" @click="back()" />
        <ion-icon :icon="exitOutline" class="icon" @click="reset()" />
      </div>
      <div class="overlap-2">
        <div class="text-wrapper">Round {{ current_round }}/ {{ round_option[index.round] }}</div>
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
        :current="index.current_player"
      />
    </div>
  </div>
  <transition name="pop-up">
    <div v-if="show_winner" class="winner-box">
      <div class="text-wrapper">WINNER !!</div>
      <div class="player-wrapper">{{ winner }}</div>
      <ion-icon :icon="thumbsUp" class="icon" @click="show_winner = false" />
    </div>
  </transition>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import { IonIcon } from '@ionic/vue'
import {
  exitOutline,
  reloadOutline,
  chevronBackCircleOutline,
  chevronForwardCircleOutline,
  thumbsUp
} from 'ionicons/icons'
import Board from './components/Board.vue'
import Players from './components/Player.vue'
// const ipcHandle = () => window.electron.ipcRenderer.send('ping')
const active = ref(false)
const show_winner = ref(false)
const winner = ref('Jimmy, Jeff')
const score_board_lock = ref(false)
const player_option = ref([2, 3, 4])
const score_option = ref([301, 501, 701, 901])
const round_option = ref([1, 5, 10, 15, 20])
const index = ref({
  players: 2,
  score: 0,
  round: 2,
  current_player: 5
})
const current_round = ref(1)
const dart_count = ref(0)
const dart_record = ref([
  ['', ''],
  ['', ''],
  ['', '']
])

const player = computed(() => {
  let output = []
  for (let i = 0; i < player_option.value[index.value.players]; i++) {
    let tmp = {}
    tmp['index'] = i
    tmp['name'] = `Player ${i + 1}`
    tmp['score'] = score_option.value[index.value.score]
    tmp['detail'] = []
    output.push({ ...tmp })
  }
  return output
})

function change_index(key, total_length, add = true) {
  let tmp = index.value[key]
  if (add) {
    tmp += 1
    if (tmp == total_length) {
      tmp = 0
    }
  } else {
    tmp -= 1
    if (tmp < 0) {
      tmp = total_length - 1
    }
  }
  index.value[key] = tmp
}

function next_player() {
  const total = player_option.value[index.value.players]
  index.value.current_player += 1
  if (index.value.current_player == total) {
    current_round.value += 1
  }
  index.value.current_player = index.value.current_player % total
  // if (player.value[index.value.current_player].score != 0) {
  //   return index.value.current_player
  // } else {
  //   next_player()
  // }
  return index.value.current_player
}

function judge_game_over() {
  if (index.value.current_player == 0 && dart_count.value == 0) {
    console.log('Judge Game Over')
    const w = player.value.filter((item) => item.score === 0).map((item) => item.name)
    if (w.length > 0) {
      winner.value = w.toString()
      show_winner.value = true
      return
    }
    if (current_round.value - 1 == round_option.value[index.value.round]) {
      console.log('No one finish')
      return
    }
  }
  score_board_lock.value = false
}

function score_computed(s, m) {
  if (score_board_lock.value) {
    return
  }
  if (dart_count.value == 0) {
    dart_record.value = [
      ['', ''],
      ['', ''],
      ['', '']
    ]
  }
  if (s == 50) {
    dart_record.value[dart_count.value][0] = 'Bull Eye'
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
  let reserve = player.value[index.value.current_player].score
  let reserve_index = index.value.current_player
  player.value[index.value.current_player].score -= score
  //let round_index = current_round.value - 1
  //player.value[index.value.current_player].detail[round_index][dart_count.value] = score

  if (player.value[index.value.current_player].score < 0) {
    score_board_lock.value = true
    setTimeout(() => {
      player.value[reserve_index].score = reserve
      next_player()
      dart_count.value = 0
      judge_game_over()
    }, 2000)
  } else if (player.value[index.value.current_player].score == 0) {
    score_board_lock.value = true
    setTimeout(() => {
      next_player()
      dart_count.value = 0
      judge_game_over()
    }, 2000)
  } else if (dart_count.value == 3) {
    score_board_lock.value = true
    next_player()
    dart_count.value = 0
    judge_game_over()
  }
}

function back() {
  if (dart_count.value > 0) {
    dart_count.value -= 1
    let s = 0
    if (dart_record.value[dart_count.value][0] == 'Bull Eye') {
      s = 50
    } else if (dart_record.value[dart_count.value][0] == 'Single') {
      s = dart_record.value[dart_count.value][1]
    } else if (dart_record.value[dart_count.value][0] == 'Double') {
      s = dart_record.value[dart_count.value][1] * 2
    } else if (dart_record.value[dart_count.value][0] == 'Triple') {
      s = dart_record.value[dart_count.value][1] * 3
    }
    player.value[index.value.current_player].score += s
    dart_record.value[dart_count.value] = ['', '']
    console.log(player.value[index.value.current_player].score)
  }
}

function reset() {
  active.value = false
  current_round.value = 1
  dart_count.value = 0
  dart_record.value = [
    ['', ''],
    ['', ''],
    ['', '']
  ]
  index.value.current_player = 5
  for (const key in player.value) {
    if ('score' in player.value[key]) {
      player.value[key].score = score_option.value[index.value.score]
    }
  }
  score_board_lock.value = false
}

function start() {
  active.value = true
  index.value.current_player = 0
}

onMounted(() => {
  console.log('Mounted')
})
</script>
<style lang="less">
.out-box {
  display: flex;
  background-color: antiquewhite;
  justify-content: center;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.setting-box {
  width: 100%;
  height: 80%;
  position: absolute;
  top: 0;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;

  .title {
    height: 100px;
    font-size: 60px;
    font-weight: 600;
    letter-spacing: 5px;
    background-color: rgba(#ffffff, 0.1);
    border-radius: 20px;
    padding-left: 20px;
    padding-right: 20px;
  }

  .options {
    padding: 20px;
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 8px;
  }

  .option-title {
    background-color: rgba(#e5a9ff, 0.1);
    font-size: 30px;
    padding: 10px;
    text-align: center;
    font-weight: 600;
    border-radius: 20px;
  }

  .option-value {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 200px;
  }

  .value-text {
    font-size: 30px;
    line-height: 60px;
    font-weight: 600;
    text-align: center;
    background-color: #3d3c3c;
    width: 200px;
    border-radius: 20px;
  }

  .icon {
    width: 100px;
    font-size: 3em;
    margin: 5px;
    color: rgba(#e5a9ff, 0.3);
    background-color: rgba(#3d3c3c, 0.8);
    border-radius: 15px;
    padding: 3px;
    cursor: pointer;
    &:hover {
      transform: scale(1.2);
    }
  }

  .start-button {
    font-size: 30px;
    font-weight: 600;
    letter-spacing: 5px;
    background-color: #999999;
    margin: 20px;
    padding: 10px;
    border-radius: 10px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    &:hover {
      background: repeating-radial-gradient(circle, rgba(#c3dc99, 0.3) 20%, rgba(#e5a9ff, 0.3) 40%);
      background-size: 100% 100%;
      animation: gradientSizeAnimation 2s ease infinite;
      background-position: 50% 50%;
    }
  }
}

.winner-box {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  background-color: rgba(#111111, 0.4);
  backdrop-filter: blur(20px);
  z-index: 90;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;

  .text-wrapper {
    font-size: 70px;
    font-weight: 600;
    color: orange;
    background-color: rgba(tomato, 0.2);
    margin: 20px;
    padding: 10px;
    border-radius: 10px;
    width: 60%;
    height: 80px;
    text-align: center;
  }

  .player-wrapper {
    font-size: 40px;
  }

  .icon {
    font-size: 3em;
    margin: 50px;
    color: azure;
    &:hover {
      transform: scale(1.5);
    }
  }
}

.board-box {
  width: 65%;
  height: 65%;
  position: absolute;
  top: 20px;
  left: 20px;
  background-color: rgba(93, 93, 93, 0.2);
  border-radius: 50px;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  animation: slideLeft 1s ease forwards;
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
  animation: slideRight 1s ease forwards;
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

/* 定義漸變動畫 */
@keyframes gradientSizeAnimation {
  0% {
    background-size: 100% 100%;
  }
  50% {
    background-size: 250% 250%;
  }
  100% {
    background-size: 500% 500%;
  }
}

@keyframes slideLeft {
  from {
    transform: translateX(-100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

@keyframes slideRight {
  from {
    transform: translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

/* Entering (optional) */
.slide-up-enter-from {
  transform: translateY(-100%);
  opacity: 0;
}
.slide-up-enter-to {
  transform: translateY(0);
  opacity: 1;
}
.slide-up-enter-active {
  transition: all 1s ease;
}

/* Leaving */
.slide-up-leave-from {
  transform: translateY(0);
  opacity: 1;
}
.slide-up-leave-to {
  transform: translateY(-100%);
  opacity: 0;
}
.slide-up-leave-active {
  transition: all 1s ease;
}
</style>
