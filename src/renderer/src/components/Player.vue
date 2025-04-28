<template>
  <div class="player" :style="get_style()">
    <ion-icon :icon="person" class="icon" />
    <div class="text-wrapper">{{ name }}</div>
    <div class="score-wrapper">{{ score }}</div>
    <div class="rectangle" />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { IonIcon } from '@ionic/vue'
import { person } from 'ionicons/icons'
const props = defineProps({
  index: String,
  name: String,
  current: Boolean,
  score: Number
})

function get_style() {
  if (props.index == '1') {
    return 'background-color: #571313;'
  } else if (props.index == '2') {
    return 'background-color: #305514;'
  } else if (props.index == '3') {
    return 'background-color: #a99608;'
  } else if (props.index == '4') {
    return 'background-color: #2d2d59;'
  }
}
</script>
<style lang="less">
.player {
  border-radius: 10px 10px 0px 0px;
  height: 170px;
  position: relative;
  width: 150px;

  .text-wrapper {
    position: absolute;
    top: 10px;
    left: -10px;
    color: #dedccf;
    font-size: 20px;
    font-weight: 600;
    line-height: 30px;
    width: 150px;
    text-align: right;
    white-space: nowrap;
  }

  .score-wrapper {
    color: #dddbce;
    font-family: 'Itim-Regular', Helvetica;
    font-size: 64px;
    font-weight: 400;
    left: 1px;
    letter-spacing: 0;
    line-height: 20px;
    position: absolute;
    text-align: center;
    top: 90px;
    width: 150px;
  }

  .rectangle {
    background-color: #d9d9d9;
    border-radius: 20px;
    height: 6px;
    left: 20px;
    position: absolute;
    top: 150px;
    width: 110px;
  }

  .icon {
    font-size: 1.8em;
    padding: 8px;
  }
}
</style>
