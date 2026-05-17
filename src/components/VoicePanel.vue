<template>
  <section class="voice-panel">
    <div class="panel-title">語音設定</div>
    <div class="voice-row">
      <label>
        語言
        <select v-model="voiceOptions.lang">
          <option value="zh-TW">國語（zh-TW）</option>
          <option value="zh-CN">中文（zh-CN）</option>
          <option value="en-US">English（en-US）</option>
        </select>
      </label>
      <label>
        語速 {{ voiceOptions.rate.toFixed(0.5) }}
        <input v-model.number="voiceOptions.rate" type="range" min="0.5" max="2" step="0.1" />
      </label>
      <label>
        音調 {{ voiceOptions.pitch.toFixed(1) }}
        <input v-model.number="voiceOptions.pitch" type="range" min="0.5" max="2" step="0.1" />
      </label>
      <label>
        音量 {{ voiceOptions.volume.toFixed(1) }}
        <input v-model.number="voiceOptions.volume" type="range" min="0.1" max="1" step="0.1" />
      </label>
    </div>
    <div class="voice-actions">
      <button class="ghost-btn" type="button" @click="testVoice">試聽</button>
      <button class="ghost-btn" type="button" @click="resetVoice">重設</button>
    </div>
  </section>
</template>

<script setup>
// ✅ 這裡就要把 watch 一起 import 進來
import { reactive, toRefs, watch } from 'vue'

const props = defineProps({
  modelValue: {
    type: Object,
    default: () => ({ lang: 'zh-TW', rate: 1, pitch: 1, volume: 1 })
  }
})
const emit = defineEmits(['update:modelValue'])

const voiceOptions = reactive({ ...props.modelValue })

function sync() {
  emit('update:modelValue', { ...voiceOptions })
}

function speak(text) {
  if (!('speechSynthesis' in window)) return
  window.speechSynthesis.cancel()
  const u = new SpeechSynthesisUtterance(text)
  u.lang = voiceOptions.lang
  u.rate = voiceOptions.rate
  u.pitch = voiceOptions.pitch
  u.volume = voiceOptions.volume
  window.speechSynthesis.speak(u)
}

function testVoice() {
  sync()
  speak('ㄅ，包子')
}

function resetVoice() {
  voiceOptions.lang = 'zh-TW'
  voiceOptions.rate = 1
  voiceOptions.pitch = 1
  voiceOptions.volume = 1
  sync()
}

// ✅ 現在 watch 就有被正確引入，不會報錯
watch(voiceOptions, sync, { deep: true })
</script>