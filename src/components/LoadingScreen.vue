<template>
  <div class="loading-container-dynamic">
    <div class="balloon-background">
      <div v-for="n in 10" :key="'balloon-'+n" class="balloon"
           :style="{
             left: Math.random() * 100 + 'vw',
             width: (60 + Math.random() * 60) + 'px',
             height: (80 + Math.random() * 80) + 'px',
             animationDuration: (20 + Math.random() * 15) + 's',
             animationDelay: Math.random() * 10 + 's'
           }">
      </div>
    </div>

    <div class="content-wrapper-dynamic">
      <div class="cake-icon">
        🎂
      </div>

      <div class="loading-text">
        <span v-for="(char, i) in loadingChars" :key="i"
              class="wave-char"
              :style="{ animationDelay: (i * 0.1) + 's' }">
          {{ char === ' ' ? '\u00A0' : char }}
        </span>
      </div>

      <p class="subtitle-dynamic">
        正在为你准备生日惊喜...
      </p>

      <div class="progress-bar-container">
        <div class="progress-bar-glow"></div>
        <div class="progress-bar-sparkle">✨</div>
      </div>
    </div>
  </div>
</template>

<script setup>
const loadingText = "Loading...";
const loadingChars = loadingText.split('');
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700&family=Montserrat:wght@300;400&display=swap');

/* --- 主容器 --- */
.loading-container-dynamic {
  position: fixed;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 50;
  overflow: hidden;
  background: #fefcf9; /* 奶油白背景 */
  font-family: 'Montserrat', sans-serif;
}

/* --- 动态气球背景 --- */
.balloon-background {
  position: absolute;
  width: 100%;
  height: 100%;
  pointer-events: none;
}

@keyframes rise-and-sway {
  0% { transform: translateY(100vh) rotate(0deg); opacity: 0; }
  10% { opacity: 0.6; }
  90% { opacity: 0.6; }
  100% { transform: translateY(-100px) rotate(20deg); opacity: 0; }
}

.balloon {
  position: absolute;
  bottom: -150px; /* 从屏幕外开始 */
  border: 2px solid rgba(213, 189, 175, 0.5); /* 香槟金描边 */
  border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%; /* 气球形状 */
  animation: rise-and-sway linear infinite;
  transform: rotate(-20deg);
}
.balloon::before {
  content: '';
  position: absolute;
  bottom: -10px;
  left: 50%;
  width: 2px;
  height: 15px;
  background: rgba(213, 189, 175, 0.5);
  transform: translateX(-50%);
}

/* --- 内容容器 --- */
.content-wrapper-dynamic {
  text-align: center;
  position: relative;
  z-index: 10;
  animation: content-appear 1.2s ease-out;
}

@keyframes content-appear {
  from { opacity: 0; transform: scale(0.95); }
  to { opacity: 1; transform: scale(1); }
}

/* --- 旋转生日蛋糕 --- */
.cake-icon {
  font-size: 6rem;
  line-height: 1;
  animation: spin-and-bob 5s infinite ease-in-out;
  filter: drop-shadow(0 4px 10px rgba(0,0,0,0.1));
}

@keyframes spin-and-bob {
  0% { transform: rotate(0deg) translateY(0px); }
  25% { transform: rotate(90deg) translateY(-5px); }
  50% { transform: rotate(180deg) translateY(0px); }
  75% { transform: rotate(270deg) translateY(5px); }
  100% { transform: rotate(360deg) translateY(0px); }
}

/* --- 波浪加载文字 --- */
.loading-text {
  margin-top: 2rem;
  font-family: 'Playfair Display', serif;
  font-size: 2.5rem;
  font-weight: 700;
  color: #3a3a3a;
  white-space: pre;
}

@keyframes wave {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-12px); }
}

.wave-char {
  display: inline-block;
  animation: wave 1.8s infinite cubic-bezier(0.45, 0.05, 0.55, 0.95);
}

/* --- 副标题 --- */
.subtitle-dynamic {
  margin-top: 1rem;
  font-size: 1.1rem;
  color: #888;
  letter-spacing: 0.5px;
}

/* --- 动态进度条 --- */
.progress-bar-container {
  width: 280px;
  height: 6px;
  background-color: #f0e9e4;
  border-radius: 6px;
  margin: 3rem auto 0;
  position: relative;
  overflow: hidden;
}

@keyframes fill-progress {
  from { transform: translateX(-100%); }
  to { transform: translateX(0%); }
}

.progress-bar-glow {
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, #f9e79f, #f7d794, #e8b387); /* 温暖的香槟金渐变 */
  border-radius: 6px;
  animation: fill-progress 3s infinite ease-in-out;
}

@keyframes sparkle-move {
  0% { left: 0%; opacity: 0; }
  10% { opacity: 1; }
  90% { opacity: 1; }
  100% { left: 100%; opacity: 0; }
}

.progress-bar-sparkle {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  font-size: 1.2rem;
  color: #d5bdaf;
  animation: sparkle-move 3s infinite ease-in-out;
}

</style>