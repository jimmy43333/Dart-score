<template>
  <div :class="get_class()" :style="get_style()">
    <ion-icon :icon="person" class="icon" />
    <div class="text-wrapper">{{ name }}</div>
    <div class="score-wrapper">{{ score }}</div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { IonIcon } from '@ionic/vue'
import { person } from 'ionicons/icons'
const props = defineProps({
  index: Number,
  name: String,
  current: Boolean,
  score: Number,
  current: Number
})

function get_class() {
  if (props.score < 0) {
    return 'player player-break'
  } else if (props.index == props.current) {
    return 'player player-current'
  } else {
    return 'player'
  }
}

function get_style() {
  if (props.index == 0) {
    return 'background-color: #571313;'
  } else if (props.index == 1) {
    return 'background-color: #305514;'
  } else if (props.index == 2) {
    return 'background-color: #a99608;'
  } else if (props.index == 3) {
    return 'background-color: #2d2d59;'
  }
}
</script>
<style lang="less">
.player {
  border-radius: 10px 10px 0px 0px;
  position: relative;
  width: 150px;
  height: 170px;
  display: flex;
  flex-direction: column;
  align-items: center;

  .text-wrapper {
    position: relative;
    color: #dedccf;
    font-size: 20px;
    font-weight: 600;
    line-height: 50px;
    width: 100%;
    display: flex;
    justify-content: center;
    white-space: nowrap;
    padding-bottom: 5px;
  }

  .score-wrapper {
    position: relative;
    color: #dddbce;
    font-size: 64px;
    font-weight: 600;
    line-height: 50px;
    text-align: center;
    padding-bottom: 20px;
  }

  .icon {
    font-size: 2em;
    padding: 8px;
  }
}

.player-current {
  height: 200px;
  box-shadow: 0 0px 20px rgb(254, 255, 236);
}

.player-break {
  height: 200px;
}

.player-break::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  background-image: url('../assets/break.png');
  background-size: cover;
  background-repeat: no-repeat;
  z-index: 1;
}
</style>
