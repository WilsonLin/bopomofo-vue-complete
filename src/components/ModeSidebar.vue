<template>
  <aside class="panel">
    <h2>學習模式</h2>
    <div class="mode-list">
      <button
        v-for="mode in modes"
        :key="mode.id"
        class="mode-btn"
        :class="{ active: currentMode === mode.id }"
        type="button"
        @click="$emit('change-mode', mode.id)"
      >
        <span class="mode-title">{{ mode.name }}</span>
        <span class="mode-desc">{{ mode.desc }}</span>
      </button>
    </div>

    <div class="stats-stack">
      <div class="stats-card">
        <span class="label">分數</span>
        <span class="value">{{ score }}</span>
      </div>
      <div class="stats-card">
        <span class="label">連續答對</span>
        <span class="value">{{ streak }}</span>
      </div>
      <div class="stats-card">
        <span class="label">正確率</span>
        <span class="value">{{ accuracy }}%</span>
      </div>
    </div>
  </aside>
</template>

<script setup>
defineProps({
  modes: { type: Array, required: true },
  currentMode: { type: String, required: true },
  score: { type: Number, required: true },
  streak: { type: Number, required: true },
  accuracy: { type: Number, required: true }
})
defineEmits(['change-mode'])
</script>
