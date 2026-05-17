<template>
  <section class="hero">
    <div class="container hero-grid">
      <div>
        <div class="eyebrow">完成版兒童學習網站</div>
        <h1>把ㄅㄆㄇ，做成孩子能聽、能看、能玩的注音探險。</h1>
        <p>
          整個版面採大圖示與大按鈕設計，適合小朋友用手指點擊，並整合語音發音與聽音辨位練習，一次練習注音與生活詞語。[web:268]
        </p>
        <div class="hero-actions">
          <button class="primary-btn" type="button" @click="$emit('reset')">開始挑戰</button>
          <button class="ghost-btn" type="button" @click="$emit('mode', 'hearAndSelect')">聽音辨位模式</button>
        </div>
      </div>
      <aside class="hero-card">
        <div class="hero-card-head">
          <strong>今天認識的圖卡</strong>
          <span>看圖聽音一起學</span>
        </div>
        <div class="hero-letters">
          <div class="letter-bubble">🥟</div>
          <div class="letter-bubble">🍇</div>
          <div class="letter-bubble">🐱</div>
          <div class="letter-bubble">✈️</div>
          <div class="letter-bubble">🎂</div>
          <div class="letter-bubble">🐰</div>
        </div>
      </aside>
    </div>
  </section>
</template>

<script setup>
defineEmits(['reset', 'mode'])
</script>
