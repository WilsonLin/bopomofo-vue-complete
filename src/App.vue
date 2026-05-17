<template>
  <div class="app-shell">
    <HeaderBar :theme="theme" :mode-name="modeName" @toggle-theme="toggleTheme" />
    <main>
      <HeroSection @reset="resetGame" @mode="setMode" />
      <section class="container">
        <div class="game-grid">
          <ModeSidebar
            :modes="modes"
            :current-mode="currentMode"
            :score="score"
            :streak="streak"
            :accuracy="accuracy"
            @change-mode="setMode"
          />
          <div>
            <GameBoard
              ref="gameBoardRef"
              :progress="progress"
              :finished="finished"
              :question="question"
              :round="round"
              :total-rounds="totalRounds"
              :mode-name="modeName"
              :answered="answered"
              :selected-option="selectedOption"
              :feedback="feedback"
              :feedback-type="feedbackType"
              :score="score"
              :correct-count="correctCount"
              :wrong-count="wrongCount"
              :accuracy="accuracy"
              @answer="handleAnswer"
              @next="nextQuestion"
              @reset="resetGame"
              @mode="setMode"
              @replay-sound="replaySound"
            />
            <VoicePanel v-model="voiceOptions" />
          </div>
        </div>
      </section>
    </main>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'
import HeaderBar from './components/HeaderBar.vue'
import HeroSection from './components/HeroSection.vue'
import ModeSidebar from './components/ModeSidebar.vue'
import GameBoard from './components/GameBoard.vue'
import VoicePanel from './components/VoicePanel.vue'
import { bopomofoData, modes } from './data/bopomofo'

const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches
const theme = ref(prefersDark ? 'dark' : 'light')
document.documentElement.setAttribute('data-theme', theme.value)

const totalRounds = 10
const currentMode = ref('soundToSymbol')
const round = ref(1)
const score = ref(0)
const correctCount = ref(0)
const wrongCount = ref(0)
const streak = ref(0)
const answered = ref(false)
const finished = ref(false)
const selectedOption = ref('')
const feedback = ref('準備好了，選一張圖卡吧！')
const feedbackType = ref('')
const question = ref(null)
const lastTarget = ref(null)

const voiceOptions = ref({
  lang: 'zh-TW',
  rate: 1,
  pitch: 1,
  volume: 1
})

const gameBoardRef = ref(null)

function speak(text) {
  if (!('speechSynthesis' in window)) return
  window.speechSynthesis.cancel()
  const u = new SpeechSynthesisUtterance(text)
  u.lang = voiceOptions.value.lang
  u.rate = voiceOptions.value.rate
  u.pitch = voiceOptions.value.pitch
  u.volume = voiceOptions.value.volume
  window.speechSynthesis.speak(u)
}

function playSound(soundPath) {
  const audio = new Audio(soundPath)
  audio.volume = 0.7
  audio.play().catch(() => {})
}

function shuffle(array) {
  const arr = [...array]
  for (let i = arr.length - 1; i > 0; i -= 1) {
    const j = Math.floor(Math.random() * (i + 1))
    ;[arr[i], arr[j]] = [arr[j], arr[i]]
  }
  return arr
}

function randomDistinctItems(targetSymbol, count = 3) {
  return shuffle(bopomofoData.filter((item) => item.symbol !== targetSymbol)).slice(0, count)
}

function toIconOption(item, valueType) {
  return {
    id: `${item.symbol}-${valueType}`,
    value: valueType === 'symbol' ? item.symbol : item.word,
    symbol: item.symbol,
    icon: item.icon,
    hint: item.hint,
    label: `${item.word}，${item.hint}`
  }
}

function makeQuestion() {
  const mixed = currentMode.value === 'review'
  const mode = mixed
    ? Math.random() > 0.5
      ? 'soundToSymbol'
      : 'symbolToWord'
    : currentMode.value
  const target = shuffle(bopomofoData)[0]
  lastTarget.value = target
  const wrongChoices = randomDistinctItems(target.symbol)

  if (mode === 'soundToSymbol') {
    question.value = {
      type: mode,
      title: target.symbol,
      prompt: target.voiceText,
      sub: '請看提示，選出對應的圖卡。',
      answer: target.word,
      target,
      options: shuffle([toIconOption(target, 'word'), ...wrongChoices.map((item) => toIconOption(item, 'word'))])
    }
    speak(target.voiceText)
    return
  }

  if (mode === 'hearAndSelect') {
    question.value = {
      type: mode,
      title: '🔊 聽一聽',
      prompt: '請聽發音，選出正確的注音符號圖卡。',
      sub: '點喇叭可以重播。',
      answer: target.symbol,
      target,
      options: shuffle([toIconOption(target, 'symbol'), ...wrongChoices.map((item) => toIconOption(item, 'symbol'))])
    }
    speak(target.voiceText)
    return
  }

  question.value = {
    type: mode,
    title: target.symbol,
    prompt: '這個注音會對應到哪張圖卡？',
    sub: '請找出對應的生活詞語。',
    answer: target.word,
    target,
    options: shuffle([toIconOption(target, 'word'), ...wrongChoices.map((item) => toIconOption(item, 'word'))])
  }
}

function resetGame() {
  round.value = 1
  score.value = 0
  correctCount.value = 0
  wrongCount.value = 0
  streak.value = 0
  answered.value = false
  finished.value = false
  selectedOption.value = ''
  feedback.value = '準備好了，選一張圖卡吧！'
  feedbackType.value = ''
  makeQuestion()
}

function setMode(modeId) {
  currentMode.value = modeId
  resetGame()
}

function handleAnswer(option) {
  if (answered.value || finished.value) return
  selectedOption.value = option
  answered.value = true

  // 找到選中選項對應的項目，用於朗讀
  const selectedItem = bopomofoData.find((item) => {
    if (question.value.type === 'hearAndSelect') {
      return item.symbol === option
    }
    return item.word === option
  })

  const isCorrect = option === question.value.answer

  if (isCorrect) {
    score.value += 10
    correctCount.value += 1
    streak.value += 1
    feedback.value = `答對了！這題是 ${question.value.target.word}。`
    feedbackType.value = 'success'
    if (gameBoardRef.value && gameBoardRef.value.triggerCheer) {
      gameBoardRef.value.triggerCheer()
    }
  } else {
    wrongCount.value += 1
    streak.value = 0
    feedback.value = `這題答案是 ${question.value.target.word}，再玩一次就會更熟。`
    feedbackType.value = 'error'
  }

  // 朗讀選中的圖卡名稱
  if (selectedItem) {
    setTimeout(() => {
      speak(selectedItem.word)
    }, 300)
  }

  // 播放對應的音效
  if (isCorrect) {
    playSound('/sounds/correct.mp3')
  } else {
    playSound('/sounds/wrong.mp3')
  }

  // 自動跳到下一題 (延遲 2 秒)
  setTimeout(() => {
    nextQuestion()
  }, 2000)
}

function nextQuestion() {
  if (!answered.value) return
  if (round.value >= totalRounds) {
    finished.value = true
    return
  }
  round.value += 1
  answered.value = false
  selectedOption.value = ''
  feedback.value = '下一題來了，繼續選圖卡。'
  feedbackType.value = ''
  makeQuestion()
}

function toggleTheme() {
  theme.value = theme.value === 'dark' ? 'light' : 'dark'
  document.documentElement.setAttribute('data-theme', theme.value)
}

function replaySound() {
  if (lastTarget.value) {
    speak(lastTarget.value.voiceText)
  }
}

const progress = computed(() => ((round.value - 1 + (answered.value ? 1 : 0)) / totalRounds) * 100)
const accuracy = computed(() => {
  const total = correctCount.value + wrongCount.value
  return total ? Math.round((correctCount.value / total) * 100) : 0
})
const modeName = computed(() => modes.find((m) => m.id === currentMode.value)?.name || '練習')

resetGame()
</script>
