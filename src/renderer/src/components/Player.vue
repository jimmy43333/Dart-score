<template>
  <div id="players">
    <div
      v-for="p in player"
      :key="p.index"
      :class="p.index == currentPlayer ? 'score-box score-current' : 'score-box'"
    >
      <div class="player-info">
        <span> {{ p.name }}</span>
        <span class="player-info-score"> {{ p.score }}</span>
      </div>
      <div class="score-table">
        <table>
          <tbody>
            <tr>
              <th v-for="ele in p.detail" :key="ele" :class="judge_td_style(ele[0])">
                {{ ele[0] }}
              </th>
            </tr>
            <tr v-for="index in 3" :key="index">
              <td v-for="ele in p.detail" :key="ele" :class="judge_td_style(ele[0])">
                {{ ele[index] }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
const props = defineProps({
  player: Array,
  currentRound: Number,
  currentPlayer: Number
})

function judge_td_style(index) {
  if (index == props.currentRound) {
    return 'th_back_on'
  }
}
</script>
<style lang="less">
#players {
  position: absolute;
  width: 800px;
  height: 800px;
  top: 0;
  right: 0;
  margin: 20px;
  display: flex;
  flex-direction: column;
}

.score-box {
  width: 100%;
  height: 170px;
  display: flex;
  background-color: rgba(aliceblue, 0.1);
  border-radius: 20px;
  padding: 20px;
  margin: 5px;
}

.score-current {
  box-shadow: inset 0 0 10px rgba(255, 150, 150, 0.8);
}

.player-info {
  width: 300px;
  display: flex;
  flex-direction: column;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  font-size: 30px;
  &-score {
    font-size: 40px;
    font-weight: 800;
  }
}

.score-table {
  width: 700px;
  height: 100%;
}

.th_back_on {
  background-color: rgba(goldenrod, 0.3);
}

table {
  font-family: 'Oswald', sans-serif;
  font-size: 15pt;
  border-collapse: separate;
  th {
    background-color: rgba(midnightblue, 0.5);
    color: #ffffff;
    width: 5vw;
    height: 20px;
    font-weight: 800;
  }

  td {
    background-color: #363636;
    color: #ffffff;
    width: 5vw;
    height: 30px;
    text-align: center;
    transition: all 0.3s ease-in-out;
  }
}
</style>
