<template>
  <section class="game-board" aria-live="polite">
    <div class="progress-bar" aria-hidden="true">
      <div class="progress-fill" :style="{ width: progress + '%' }"></div>
    </div>

    <template v-if="!finished && question">
      <div class="question-box">
        <div class="question-meta">
          <span>第 {{ round }} / {{ totalRounds }} 題</span>
          <span>{{ modeName }}</span>
        </div>
        <div class="question-title">{{ question.title }}</div>
        <div class="question-prompt">
          {{ question.prompt }}
          <button class="icon-btn small" type="button" @click="$emit('replay-sound')" aria-label="重播發音">🔊</button>
        </div>
        <div class="question-sub">{{ question.sub }}</div>
      </div>

      <div class="options-grid icon-grid">
        <button
          v-for="option in question.options"
          :key="option.id"
          class="option-btn icon-option"
          :class="{
            correct: answered && option.value === question.answer,
            wrong: answered && option.value === selectedOption && option.value !== question.answer,
            'correct-flash': cheerActive && option.value === question.answer
          }"
          type="button"
          @click="$emit('answer', option.value)"
          :disabled="answered"
          :aria-label="option.label"
        >
          <span class="option-emoji" aria-hidden="true">{{ option.icon }}</span>
          <span class="option-symbol">{{ option.symbol }}</span>
          <span class="option-hint">{{ option.hint }}</span>
        </button>
      </div>

      <div class="feedback-row">
        <div class="feedback-box" :class="feedbackType">{{ feedback }}</div>
        <div class="toolbar">
          <button class="ghost-btn" type="button" @click="$emit('reset')">重新開始</button>
        </div>
      </div>

      <div class="cheer-layer" v-if="cheerActive">
        <div class="cheer-burst">
          <span v-for="n in 14" :key="n" class="sparkle" :style="sparkleStyle(n)">✨</span>
        </div>
      </div>
      <audio ref="cheerAudio" preload="auto" src="/sounds/cheer.mp3"></audio>
    </template>

    <template v-else>
      <div class="result-card">
        <div class="result-sub">探險完成</div>
        <div class="result-score">{{ score }} 分</div>
        <p class="result-text">這一輪圖卡任務完成了，可以再玩一次或換模式繼續練習。</p>
        <div class="result-grid">
          <div class="mini-stat"><span class="k">答對</span><span class="v">{{ correctCount }}</span></div>
          <div class="mini-stat"><span class="k">答錯</span><span class="v">{{ wrongCount }}</span></div>
          <div class="mini-stat"><span class="k">正確率</span><span class="v">{{ accuracy }}%</span></div>
        </div>
        <div class="toolbar">
          <button class="primary-btn" type="button" @click="$emit('reset')">再玩一次</button>
          <button class="ghost-btn" type="button" @click="$emit('mode', 'hearAndSelect')">繼續聽音辨位</button>
        </div>
      </div>
    </template>
  </section>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({
  progress: { type: Number, required: true },
  finished: { type: Boolean, required: true },
  question: { type: Object, default: null },
  round: { type: Number, required: true },
  totalRounds: { type: Number, required: true },
  modeName: { type: String, required: true },
  answered: { type: Boolean, required: true },
  selectedOption: { type: String, required: true },
  feedback: { type: String, required: true },
  feedbackType: { type: String, required: true },
  score: { type: Number, required: true },
  correctCount: { type: Number, required: true },
  wrongCount: { type: Number, required: true },
  accuracy: { type: Number, required: true }
})

const emit = defineEmits(['answer', 'next', 'reset', 'mode'])

const cheerActive = ref(false)
const cheerAudio = ref(null)

function sparkleStyle(n) {
  const angle = (n / 14) * Math.PI * 2
  const x = Math.cos(angle) * 90
  const y = Math.sin(angle) * 90
  return {
    '--x': `${x}px`,
    '--y': `${y}px`,
    animationDelay: `${n * 0.03}s`
  }
}

function triggerCheer() {
  cheerActive.value = true
  if (cheerAudio.value) {
    cheerAudio.value.currentTime = 0
    cheerAudio.value.play().catch(() => {})
  }
  setTimeout(() => {
    cheerActive.value = false
  }, 1000)
}

// 提供給父層在答對時呼叫
defineExpose({ triggerCheer })
</script>
