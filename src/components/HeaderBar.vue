<template>
  <header class="topbar">
    <div class="container topbar-inner">
      <div class="brand" aria-label="ㄅㄆㄇ圖卡探險隊">
        <div class="brand-mark" aria-hidden="true">🎒</div>
        <div>
          <div class="brand-sub">Vue 3 兒童注音圖卡遊戲</div>
          <div class="brand-title">ㄅㄆㄇ圖卡探險隊</div>
        </div>
      </div>
      <div class="top-actions">
        <div class="chip">{{ modeName }}</div>
        <button class="icon-btn" type="button" @click="$emit('toggle-theme')" :aria-label="theme === 'dark' ? '切換成淺色模式' : '切換成深色模式'">
          {{ theme === 'dark' ? '☀️' : '🌙' }}
        </button>
      </div>
    </div>
  </header>
</template>

<script setup>
defineProps({
  theme: { type: String, required: true },
  modeName: { type: String, required: true }
})
defineEmits(['toggle-theme'])
</script>
