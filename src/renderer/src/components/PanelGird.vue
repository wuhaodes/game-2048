<template>
  <div class="panel-gird-wrapper">
    <div class="panel-gird" ref="panelGird">
      <div class="gird-back-wrapper">
        <div class="grid-row" v-for="(girdRow, rowIndex) in girdList" :key="rowIndex">
          <div
            class="gird-item"
            :data-value="gird"
            v-for="(gird, colIdx) in girdRow"
            :key="gird + colIdx"
          >
            {{ gird }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { onMounted, ref } from "vue";
let girdList = ref([
  [0, 0, 0, 0],
  [0, 0, 0, 0],
  [0, 0, 0, 0],
  [0, 0, 0, 0],
]);

function addNewNumber() {
  let emptyCells: { row: number; col: number }[] = [];
  for (let i = 0; i < 4; i++) {
    for (let j = 0; j < 4; j++) {
      if (girdList.value[i][j] === 0) {
        emptyCells.push({ row: i, col: j });
      }
    }
  }
  if (emptyCells.length === 0) {
    return false;
  }
  let randomIndex = Math.floor(Math.random() * emptyCells.length);
  let randomCell = emptyCells[randomIndex];
  let randomValue = Math.random() < 0.9 ? 2 : 4;
  girdList.value[randomCell.row][randomCell.col] = randomValue;
  return true;
}

function onStep() {
  addNewNumber();
  addNewNumber();
}

function onKeyDown(event: KeyboardEvent) {
  let keyCode = event.keyCode;
  let gird = girdList.value;
  let moved = false;
  switch (keyCode) {
    case 37: // left
      for (let i = 0; i < 4; i++) {
        for (let j = 0; j < 3; j++) {
          if (gird[i][j] === gird[i][j + 1] && gird[i][j]!== 0) {
            gird[i][j] *= 2;
            gird[i][j + 1] = 0;
            moved = true;
          }
        }
      }
      break;
    case 38: // up
      for (let i = 0; i < 3; i++) {
        for (let j = 0; j < 4; j++) {
          if (gird[i][j] === gird[i + 1][j] && gird[i][j]!== 0) {
            gird[i][j] *= 2;
            gird[i + 1][j] = 0;
            moved = true;
          }
        }
      }
      break;
    case 39: // right
      for (let i = 0; i < 4; i++) {
        for (let j = 3; j > 0; j--) {
          if (gird[i][j] === gird[i][j - 1] && gird[i][j]!== 0) {
            gird[i][j] *= 2;
            gird[i][j - 1] = 0;
            moved = true;
          }
        }
      }
      break;
    case 40: // down
      for (let i = 3; i > 0; i--) {
        for (let j = 0; j < 4; j++) {
          if (gird[i][j] === gird[i - 1][j] && gird[i][j]!== 0) {
            gird[i][j] *= 2;
            gird[i - 1][j] = 0;
            moved = true;
          }
        }
      }
      break;
    default:
      break;
  }
  if (moved) {    
    for (let i = 0; i < 4; i++) {
      for (let j = 0; j < 4; j++) {
        if (gird[i][j] === 0) {
          let emptyCells: { row: number; col: number }[] = [];
          for (let k = 0; k < 4; k++) {
            for (let l = 0; l < 4; l++) {
              if (gird[k][l] === 0) {
                emptyCells.push({ row: k, col: l });
              }
            }
          }
          if (emptyCells.length === 0) {
            return false;
          }
          let randomIndex = Math.floor(Math.random() * emptyCells.length);
          let randomCell = emptyCells[randomIndex];
          let randomValue = Math.random() < 0.9 ? 2 : 4;
          gird[randomCell.row][randomCell.col] = randomValue;
        }
      }
    }
  }
  girdList.value = gird;
}
onMounted(() => {
  window.addEventListener("keydown", onKeyDown);
})
onStep()
</script>

<style scoped lang="scss">
.panel-gird-wrapper {
  margin-top: 1rem;
  cursor: none;
  width: 100%;
  .panel-gird {
    width: 100%;
    padding-bottom: 100%;
    position: relative;
    border: none;
    line-height: normal;
    outline: none;
    cursor: none;

    .gird-back-wrapper {
      font-size: 0;
      position: absolute;
      left: 0;
      top: 0;
      width: 100%;
      height: 100%;
      box-sizing: border-box;
      background-color: #bbada0;
      border-radius: 0.25rem;
      padding: 1rem;
      overflow: hidden;
      display: flex;
      flex-direction: column;
      align-items: center;

      .grid-row {
        display: flex;
        align-items: center;
        justify-content: center;
        flex: 1;
        width: 100%;
        margin: 0.5rem 1rem;

        .gird-item {
          flex: 1;
          height: 100%;
          background-color: rgba(238, 228, 218, 0.35);
          border-radius: 0.25rem;
          margin-top: 1rem;
          margin-right: 1rem;
          font-size: 3rem;
          display: inline-flex;
          align-items: center;
          justify-content: center;
          user-select: none;
          color: #f9f6f2;
          font-weight: bold;
          cursor: none;

          &.move-right {
            transform: translateX(calc(100% + 1rem));
          }

          &.move-left {
            transform: translateX(calc(-100% - 1rem));
          }

          &.move-top {
            transform: translateY(calc(100% + 1rem));
          }

          &.move-bottom {
            transform: translateY(calc(-100% - 1rem));
          }

          &[data-value="0"] {
            font-size: 0;
          }

          &[data-value="2"] {
            background-color: #eee4da;
            color: #776e65;
          }

          &[data-value="4"] {
            background-color: #ede0c8;
            color: #776e65;
          }

          &[data-value="8"] {
            background-color: #f2b179;
          }

          &[data-value="16"] {
            background-color: #f59563;
          }

          &[data-value="32"] {
            background-color: #f67c5f;
          }

          &[data-value="64"] {
            background-color: #f65e3b;
          }

          &:nth-child(1),
          &:nth-child(2),
          &:nth-child(3),
          &:nth-child(4) {
            margin-top: 0;
          }

          &:nth-child(4),
          &:nth-child(8),
          &:nth-child(12),
          &:nth-child(16) {
            margin-right: 0;
          }
        }

        &:first-child {
          margin-top: 0;
        }
        &:last-child {
          margin-bottom: 0;
        }
      }
    }
  }
}
</style>
