<template>
  <div id="board">
    <template v-for="(ele, index) in points">
      <div
        :class="index % 2 == 0 ? 'area area-odd' : 'area area-even'"
        :style="board_style(index)"
        @click="update(ele, 1)"
      ></div>
      <div
        :class="index % 2 == 0 ? 'double-area double-area-odd' : 'double-area double-area-even'"
        :style="board_style(index)"
        @click="update(ele, 2)"
      >
        {{ ele }}
      </div>
      <div
        :class="index % 2 == 0 ? 'triple-area triple-area-odd' : 'triple-area triple-area-even'"
        :style="board_style(index)"
        @click="update(ele, 3)"
      ></div>
    </template>
    <!-- <div class="bulleye_border"></div> -->
    <div class="bulleye"></div>
    <div class="bulleye-center" @click="update(50, 1)">50</div>
    <div class="area-zero" @click="update(0, 0)">0</div>
  </div>
</template>
<script setup>
import { ref } from 'vue'
const emit = defineEmits(['update_score'])
const points = ref([20, 1, 18, 4, 13, 6, 10, 15, 2, 17, 3, 19, 7, 16, 8, 11, 14, 9, 12, 5])

function board_style(i) {
  console.log(i)
  return {
    transform: `rotate(${Number.parseInt(i) * 18}deg)`,
    '-webkit-transform': `rotate(${i * 20} deg)`
  }
}

function update(s, m) {
  let result = Number.parseInt(s) * m
  emit('update_score', result)
}
</script>

<style lang="less">
#board {
  position: absolute;
  top: 0;
  left: 0;
  width: 550px;
  height: 550px;
  //   background: rgba(azure, 0.1);
  border-radius: 20px;
  margin: 20px;
  font-family: Georgia, serif;
}

.area {
  z-index: 0;
  position: absolute;
  width: 550px;
  height: 550px;
  top: 0;
  left: 0;
  border: thick double black;
  transform-origin: center;
  clip-path: polygon(50% 50%, 42% 0, 58% 0);

  &-odd {
    background: snow;
  }

  &-even {
    background: #181818;
  }
}

.double-area {
  z-index: 1;
  position: absolute;
  width: 550px;
  height: 550px;
  top: 0;
  left: 0;
  text-align: center;
  font-size: 30px;
  font-weight: 600;
  border: 1mm solid black;
  clip-path: polygon(43.5% 10%, 56.5% 10%, 58% 0, 42% 0);

  &-odd {
    color: goldenrod;
    background: midnightblue;
  }

  &-even {
    color: midnightblue;
    background: goldenrod;
  }
}

.triple-area {
  z-index: 1;
  position: absolute;
  width: 550px;
  height: 550px;
  top: 0;
  left: 0;
  border: 3mm solid black;
  clip-path: polygon(46% 25%, 54% 25%, 55% 20%, 45% 20%);
  &-even {
    background: goldenrod;
  }
  &-odd {
    background: midnightblue;
  }
}

.bulleye {
  z-index: 1;
  width: 80px;
  height: 80px;
  position: absolute;
  top: 235px;
  left: 235px;
  background: midnightblue;
  -webkit-clip-path: circle(50% at 50% 50%);
}

.bulleye-center {
  z-index: 2;
  width: 60px;
  height: 60px;
  position: absolute;
  top: 245px;
  left: 245px;
  text-align: center;
  font-size: 35px;
  font-weight: 600;
  color: midnightblue;
  background: goldenrod;
  -webkit-clip-path: circle(50% at 50% 50%);
}

.area-zero {
  z-index: 1;
  width: 60px;
  height: 60px;
  position: absolute;
  top: 480px;
  left: 10px;
  text-align: center;
  font-size: 35px;
  font-weight: 600;
  color: goldenrod;
  background: midnightblue;
  border: 1mm solid black;
  border-radius: 10px;
}

.area:hover,
.double-area:hover,
.triple-area:hover,
.bulleye-center:hover,
.area-zero:hover {
  background-color: chocolate;
  cursor: pointer;
}
</style>
