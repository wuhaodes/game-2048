<template>
  <div class="panel-wrapper">
    <div class="panel-title">2048</div>
    <div class="panel-info">
      <div class="panel-score">分数: {{ score }}</div>
      <div class="panel-step">步数: {{ step }}</div>
    </div>
    <panel-gird v-model:score="score" v-model:step="step"></panel-gird>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import PanelGird from './PanelGird.vue'

const score = ref(0)
const step = ref(0)
</script>

<style lang="scss" scoped>
.panel-wrapper {
  min-width: 24rem;
  margin: 0 auto;
  box-sizing: border-box;

  .panel-title {
    width: 100%;
    box-sizing: border-box;
    text-align: center;
    font-size: 2rem;
  }

  .panel-score {
    font-size: 1rem;
  }

  .panel-step {
    font-size: 1rem;
  }

  .panel-info {
    margin-top: 1rem;
    width: 100%;
    box-sizing: border-box;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
}
</style>
