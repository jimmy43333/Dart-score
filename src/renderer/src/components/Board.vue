<template>
  <div class="board">
    <div v-for="(ele, index) in points" :key="index">
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
    </div>
    <!-- <div class="bulleye_border"></div> -->
    <div class="bulleye">
      <div class="bulleye-center" @click="update(50, 1)">50</div>
    </div>
    <div class="area-zero" @click="update(0, 0)">0</div>
  </div>
</template>
<script setup>
import { ref } from 'vue'
const emit = defineEmits(['update_score'])
const points = ref([20, 1, 18, 4, 13, 6, 10, 15, 2, 17, 3, 19, 7, 16, 8, 11, 14, 9, 12, 5])
// const points = ref([20, 1])
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
.board {
  width: 100%;
  height: 100%;
  max-width: 600px;
  max-height: 600px;
  background-color: black;
  -webkit-clip-path: circle(50% at 50% 50%);
}

.area {
  font-family: Georgia, serif;
  z-index: 0;
  position: absolute;
  width: 100%;
  height: 100%;
  max-width: 600px;
  max-height: 600px;
  transform-origin: center;
  clip-path: polygon(50% 48%, 43% 2%, 57% 2%);

  &-odd {
    background: snow;
  }

  &-even {
    background: black;
  }
}

.double-area {
  z-index: 1;
  position: absolute;
  width: 100%;
  height: 100%;
  max-width: 600px;
  max-height: 600px;
  text-align: center;
  display: flex;
  justify-content: center;
  font-size: 30px;
  font-weight: 600;
  clip-path: polygon(44.25% 10%, 55.75% 10%, 57% 2%, 43% 2%);

  &-odd {
    color: goldenrod;
    background: midnightblue;
  }

  &-even {
    color: midnightblue;
    background: rgb(137, 104, 19);
  }
}

.triple-area {
  z-index: 1;
  position: absolute;
  width: 100%;
  height: 100%;
  max-width: 600px;
  max-height: 600px;
  border: 3mm solid black;
  clip-path: polygon(46.75% 25%, 53.25% 25%, 54% 20%, 46% 20%);
  &-even {
    background: rgb(137, 104, 19);
  }
  &-odd {
    background: midnightblue;
  }
}

.bulleye {
  z-index: 1;
  width: 100%;
  height: 100%;
  max-width: 600px;
  max-height: 600px;
  position: absolute;
  background: black;
  -webkit-clip-path: circle(8% at 50% 50%);
  display: flex;
  justify-content: center;
  align-items: center;
}

.bulleye-center {
  z-index: 10;
  width: 60px;
  height: 60px;
  position: relative;
  text-align: center;
  font-size: 35px;
  font-weight: 600;
  color: goldenrod;
  background: rgba(red, 0.6);
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
  background-color: rgb(33, 103, 63);
  cursor: pointer;
}
</style>
