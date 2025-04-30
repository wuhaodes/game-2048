<template>
  <div class="game-container">
    <div class="game-header">
      <h1>2048</h1>
      <div class="score-container">
        <div class="score" style="font-size: 35px;">分数: {{ score }}</div>
      </div>
    </div>
    
    <div v-if="gameOver" class="game-over">
      <button @click="initGame">重新开始</button>
    </div>
    
    <div class="grid-container">
      <div class="grid-row" v-for="(row, rowIndex) in grid" :key="rowIndex">
        <div 
          class="grid-cell" 
          v-for="(cell, colIndex) in row" 
          :key="colIndex"
          :class="`tile-${cell}`"
        >
          {{ cell !== 0 ? cell : '' }}
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'

const grid = ref<number[][]>([
  [0, 0, 0, 0],
  [0, 0, 0, 0],
  [0, 0, 0, 0],
  [0, 0, 0, 0]
])
const score = ref(0)
const gameOver = ref(false)

// 初始化游戏
function initGame() {
  grid.value = [
    [0, 0, 0, 0],
    [0, 0, 0, 0],
    [0, 0, 0, 0],
    [0, 0, 0, 0]
  ]
  score.value = 0
  gameOver.value = false
  addRandomTile()
  addRandomTile()
}

// 随机生成新数字(2或4)
function addRandomTile() {
  const emptyCells: [number, number][] = []
  
  grid.value.forEach((row, i) => {
    row.forEach((cell, j) => {
      if (cell === 0) emptyCells.push([i, j])
    })
  })
  
  if (emptyCells.length > 0) {
    const [i, j] = emptyCells[Math.floor(Math.random() * emptyCells.length)]
    grid.value[i][j] = Math.random() < 0.9 ? 2 : 4
  }
}

// 移动合并逻辑
function moveTiles(direction: 'up' | 'down' | 'left' | 'right') {
  let moved = false
  const newGrid = JSON.parse(JSON.stringify(grid.value))
  
  // 检查游戏是否结束
  if (checkGameOver()) {
    return
  }
  
  // 处理移动方向
  const processLine = (line: number[]) => {
    let filtered = line.filter(val => val !== 0)
    
    // 合并相同数字
    for (let i = 0; i < filtered.length - 1; i++) {
      if (filtered[i] === filtered[i + 1]) {
        filtered[i] *= 2
        filtered[i + 1] = 0
        score.value += filtered[i]
        moved = true
      }
    }
    
    filtered = filtered.filter(val => val !== 0)
    while (filtered.length < 4) filtered.push(0)
    return filtered
  }
  
  // 根据方向处理网格
  for (let i = 0; i < 4; i++) {
    let line: number[] = []
    
    // 获取当前行/列
    if (direction === 'left') line = newGrid[i]
    else if (direction === 'right') line = [...newGrid[i]].reverse()
    else if (direction === 'up') line = [newGrid[0][i], newGrid[1][i], newGrid[2][i], newGrid[3][i]]
    else if (direction === 'down') line = [newGrid[3][i], newGrid[2][i], newGrid[1][i], newGrid[0][i]]
    
    const processedLine = processLine(line)
    
    // 将处理后的行/列放回网格
    if (direction === 'left') {
      if (JSON.stringify(newGrid[i]) !== JSON.stringify(processedLine)) moved = true
      newGrid[i] = processedLine
    } else if (direction === 'right') {
      const reversed = [...processedLine].reverse()
      if (JSON.stringify(newGrid[i]) !== JSON.stringify(reversed)) moved = true
      newGrid[i] = reversed
    } else if (direction === 'up') {
      for (let j = 0; j < 4; j++) {
        if (newGrid[j][i] !== processedLine[j]) moved = true
        newGrid[j][i] = processedLine[j]
      }
    } else if (direction === 'down') {
      for (let j = 0; j < 4; j++) {
        if (newGrid[3 - j][i] !== processedLine[j]) moved = true
        newGrid[3 - j][i] = processedLine[j]
      }
    }
  }
  
  if (moved) {
    grid.value = newGrid
    addRandomTile()
  }
}

// 键盘控制
function handleKeyDown(e: KeyboardEvent) {
  if (gameOver.value) return
  
  switch (e.key) {
    case 'ArrowUp': moveTiles('up'); break
    case 'ArrowDown': moveTiles('down'); break
    case 'ArrowLeft': moveTiles('left'); break
    case 'ArrowRight': moveTiles('right'); break
  }
}

// 检查游戏是否结束
function checkGameOver() {
  // 检查是否有空格
  for (let i = 0; i < 4; i++) {
    for (let j = 0; j < 4; j++) {
      if (grid.value[i][j] === 0) {
        return false
      }
    }
  }
  
  // 检查是否有相邻相同数字
  for (let i = 0; i < 4; i++) {
    for (let j = 0; j < 4; j++) {
      if (j < 3 && grid.value[i][j] === grid.value[i][j + 1]) {
        return false
      }
      if (i < 3 && grid.value[i][j] === grid.value[i + 1][j]) {
        return false
      }
    }
  }
  
  gameOver.value = true
  return true
}

onMounted(() => {
  initGame()
  window.addEventListener('keydown', handleKeyDown)
})
</script>

<style scoped>
.game-container {
  width: 100%;
  max-width: 500px;
  margin: 0 auto;
  padding: 20px;
}

.game-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.grid-container {
  background-color: #bbada0;
  border-radius: 6px;
  padding: 15px;
}

.grid-row {
  display: flex;
  margin-bottom: 15px;
}

.grid-row:last-child {
  margin-bottom: 0;
}

.grid-cell {
  width: 100px;
  height: 100px;
  margin-right: 15px;
  background-color: rgba(238, 228, 218, 0.35);
  border-radius: 3px;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 35px;
  font-weight: bold;
  color: #776e65;
}

.grid-cell:last-child {
  margin-right: 0;
}

.tile-2 { background-color: #eee4da; font-size: 35px; }
.tile-4 { background-color: #ede0c8; font-size: 35px; }
.tile-8 { background-color: #f2b179; color: #f9f6f2; font-size: 35px; }
.tile-16 { background-color: #f59563; color: #f9f6f2; font-size: 35px; }
.tile-32 { background-color: #f67c5f; color: #f9f6f2; }
.tile-64 { background-color: #f65e3b; color: #f9f6f2; }
.tile-128 { background-color: #edcf72; color: #f9f6f2; font-size: 40px; }
.tile-256 { background-color: #edcc61; color: #f9f6f2; font-size: 40px; }
.tile-512 { background-color: #edc850; color: #f9f6f2; font-size: 40px; }
.tile-1024 { background-color: #edc53f; color: #f9f6f2; font-size: 35px; }
.tile-2048 { background-color: #edc22e; color: #f9f6f2; font-size: 35px; }

.game-over {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: rgba(0, 0, 0, 0.8);
  color: white;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
  font-size: 24px;
  z-index: 100;
}

.game-over button {
  margin-top: 10px;
  padding: 8px 16px;
  background: #8f7a66;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
</style>
