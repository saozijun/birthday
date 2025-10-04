<template>
  <div class="final-container">
    <canvas id="heart-canvas"></canvas>

    <div class="final-content-wrapper">
      <div id="text-container">
        <h1 class="final-main-title"></h1>
        <p class="final-message-line"></p>
        <p class="final-message-line"></p>
        <p class="final-message-line"></p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted } from 'vue';

const emit = defineEmits(['next-screen', 'previous-screen']);

const titleText = "生日快乐";
const messageLines = [
  "这是我们故事的最后一章，",
  "但爱，未完待续...",
  "愿未来的每一天，都如此刻般絢爛。"
];

onMounted(() => {
  const canvas = document.getElementById('heart-canvas');
  const ctx = canvas.getContext('2d');
  let particles = [];
  
  // 【MOBILE OPTIMIZATION】检查是否为移动设备屏幕
  const isMobile = window.innerWidth <= 768;

  // 【MOBILE OPTIMIZATION】根据设备类型设置不同的粒子参数
  const particleConfig = {
    explosionDensity: isMobile ? 0.1 : 0.05,   // 移动端爆炸粒子更稀疏
    explosionParticles: isMobile ? 2 : 4,      // 每个点的粒子数更少
    floatingParticleLimit: isMobile ? 80 : 200, // 浮动粒子总数上限更低
    floatingParticleGeneration: isMobile ? 1 : 3 // 每次生成浮动粒子的数量更少
  };

  const resizeCanvas = () => {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
  };
  window.addEventListener('resize', resizeCanvas);
  resizeCanvas();

  function drawHeartParticle(x, y, size, alpha) {
    ctx.beginPath();
    const topCurveHeight = size * 0.3;
    ctx.moveTo(x, y + topCurveHeight);
    ctx.bezierCurveTo(x, y, x - size / 2, y, x - size / 2, y + topCurveHeight);
    ctx.bezierCurveTo(x - size / 2, y + (size + topCurveHeight) / 2, x, y + (size + topCurveHeight) / 2, x, y + size);
    ctx.bezierCurveTo(x, y + (size + topCurveHeight) / 2, x + size / 2, y + (size + topCurveHeight) / 2, x + size / 2, y + topCurveHeight);
    ctx.bezierCurveTo(x + size / 2, y, x, y, x, y + topCurveHeight);
    ctx.closePath();
    ctx.fillStyle = `rgba(255, 105, 180, ${alpha})`;
    ctx.shadowBlur = size * 0.8;
    ctx.shadowColor = `rgba(255, 105, 180, ${alpha * 0.7})`;
    ctx.fill();
  }

  const heartPath = (t) => {
    // 【MOBILE OPTIMIZATION】在移动端稍微放大爱心轮廓
    const scaleFactor = isMobile ? 0.25 : 0.2;
    const scale = Math.min(canvas.width, canvas.height) * scaleFactor;
    const x = canvas.width / 2 + scale * (16 * Math.pow(Math.sin(t), 3));
    const y = canvas.height / 2.2 + scale * -(13 * Math.cos(t) - 5 * Math.cos(2*t) - 2 * Math.cos(3*t) - Math.cos(4*t));
    return { x, y };
  };
  
  let progress = 0;
  function drawHeartStroke() {
    if (progress > 2 * Math.PI) {
      explodeIntoParticles();
      return;
    }
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.beginPath();
    for (let t = 0; t < progress; t += 0.01) {
      const point = heartPath(t);
      if (t === 0) ctx.moveTo(point.x, point.y);
      else ctx.lineTo(point.x, point.y);
    }
    ctx.strokeStyle = 'rgba(255, 105, 180, 0.8)';
    ctx.lineWidth = 3;
    ctx.shadowBlur = 15;
    ctx.shadowColor = 'rgba(255, 105, 180, 0.8)';
    ctx.stroke();
    progress += 0.05;
    requestAnimationFrame(drawHeartStroke);
  }

  function explodeIntoParticles() {
    for (let t = 0; t < 2 * Math.PI; t += particleConfig.explosionDensity) {
      const pos = heartPath(t);
      for (let i = 0; i < particleConfig.explosionParticles; i++) {
        particles.push({
          x: pos.x, y: pos.y, vx: (Math.random() - 0.5) * 5, vy: (Math.random() - 0.5) * 5,
          initialSize: 1, targetSize: Math.random() * 8 + 8, alpha: 0, life: 0,
          maxLife: Math.random() * 200 + 100
        });
      }
    }
    animateParticles();
  }

  function createFloatingParticle() {
    const side = Math.floor(Math.random() * 4);
    let x, y, vx, vy;
    switch (side) {
      case 0: x = Math.random() * canvas.width; y = -20; vx = (Math.random() - 0.5) * 0.5; vy = Math.random() * 0.5 + 0.1; break;
      case 1: x = canvas.width + 20; y = Math.random() * canvas.height; vx = -(Math.random() * 0.5 + 0.1); vy = (Math.random() - 0.5) * 0.5; break;
      case 2: x = Math.random() * canvas.width; y = canvas.height + 20; vx = (Math.random() - 0.5) * 0.5; vy = -(Math.random() * 0.5 + 0.1); break;
      case 3: x = -20; y = Math.random() * canvas.height; vx = Math.random() * 0.5 + 0.1; vy = (Math.random() - 0.5) * 0.5; break;
    }
    particles.push({
      x, y, vx, vy, initialSize: Math.random() * 1 + 0.5, targetSize: Math.random() * 8 + 6,
      alpha: 0, life: 0, maxLife: Math.random() * 400 + 300
    });
  }

  function animateParticles() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    if (particles.length < particleConfig.floatingParticleLimit) {
      for(let i = 0; i < particleConfig.floatingParticleGeneration; i++) { createFloatingParticle(); }
    }
    for (let i = particles.length - 1; i >= 0; i--) {
      const p = particles[i];
      p.x += p.vx; p.y += p.vy; p.life++;
      const lifeProgress = p.life / p.maxLife;
      let currentAlpha, currentSize;
      if (lifeProgress < 0.2) {
        const fadeInProgress = lifeProgress / 0.2;
        currentAlpha = fadeInProgress;
        currentSize = p.initialSize + (p.targetSize - p.initialSize) * fadeInProgress;
      } else if (lifeProgress > 0.8) {
        const fadeOutProgress = (lifeProgress - 0.8) / 0.2;
        currentAlpha = 1 - fadeOutProgress;
        currentSize = p.targetSize - (p.targetSize - p.initialSize) * fadeOutProgress * 0.5;
      } else {
        currentAlpha = 1;
        currentSize = p.targetSize;
      }
      p.alpha = currentAlpha; p.size = currentSize;
      if (p.life >= p.maxLife) {
        particles.splice(i, 1);
        continue;
      }
      if (p.alpha > 0.05 && p.size > 0.5) {
        drawHeartParticle(p.x, p.y, p.size, p.alpha);
      }
    }
    requestAnimationFrame(animateParticles);
  }

  async function typeCharByChar() {
    const container = document.getElementById('text-container');
    const elements = [
      container.querySelector('.final-main-title'),
      ...container.querySelectorAll('.final-message-line')
    ];
    const texts = [titleText, ...messageLines];
    for (let i = 0; i < elements.length; i++) {
      const el = elements[i];
      const text = texts[i];
      el.innerHTML = '';
      for (let charIndex = 0; charIndex < text.length; charIndex++) {
        const charContainer = document.createElement('span');
        charContainer.className = 'char-container';
        const char = text[charIndex];
        const charSpan = document.createElement('span');
        charSpan.className = 'char-fade-in';
        charSpan.textContent = char;
        charContainer.appendChild(charSpan);
        const heartSpan = document.createElement('span');
        heartSpan.className = 'trailing-heart';
        heartSpan.innerHTML = '♥';
        heartSpan.setAttribute('aria-hidden', 'true');
        charContainer.appendChild(heartSpan);
        el.appendChild(charContainer);
        await new Promise(resolve => setTimeout(resolve, 120));
      }
      if (i < elements.length - 1) {
        await new Promise(resolve => setTimeout(resolve, 500));
      }
    }
  }
  
  setTimeout(() => {
    drawHeartStroke();
    setTimeout(typeCharByChar, 1000);
  }, 500);
});

function goNext() { emit('next-screen'); }
function goPrevious() { emit('previous-screen'); }
</script>

<style>
/* Base Styling */
.final-container {
  width: 100%;
  /* 【MOBILE OPTIMIZATION】使用 svh 兼容移动端浏览器 UI 栏 */
  height: 100svh; 
  margin: 0;
  padding: 0;
  background-color: #020210;
  overflow: hidden;
  position: relative;
  font-family: 'Times New Roman', Times, serif, 'Hiragina Sans GB', 'Microsoft YaHei', sans-serif;
}
#heart-canvas {
  position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 1;
}
.final-content-wrapper {
  display: flex; flex-direction: column; align-items: center; justify-content: center;
  height: 100%; position: relative; z-index: 2; text-align: center;
  /* 左右增加 padding, 避免内容贴边 */
  padding: 0 1rem;
  box-sizing: border-box;
}
#text-container {
  display: flex; flex-direction: column; align-items: center; text-align: center;
}
.final-main-title, .final-message-line {
  white-space: nowrap;
}

/* Text Styling */
.final-main-title {
  font-size: 3.5rem; font-weight: bold; color: #ffffff; margin-bottom: 2.5rem;
  letter-spacing: 5px; text-shadow: 0 0 15px rgba(255, 255, 255, 0.4), 0 0 30px rgba(255, 180, 210, 0.6);
}
.final-message-line {
  font-size: 1.6rem; color: #f0f0f0; margin: 0.9rem 0; text-shadow: 0 0 4px rgba(255, 255, 255, 0.15);
}

/* Layout & Animation */
.char-container {
  position: relative;
  display: inline-block;
  vertical-align: baseline; 
}
.char-fade-in {
  display: inline-block;
  animation: appearScaleBlur 0.8s forwards cubic-bezier(0.23, 1, 0.32, 1);
  opacity: 0; filter: blur(8px); transform: scale(0.8);
}
.trailing-heart {
  position: absolute;
  top: 0; left: 0; right: 0; bottom: 0;
  pointer-events: none;
  display: flex; align-items: center; justify-content: center;
  color: #ff8ab4;
  text-shadow: 0 0 8px #ff8ab4, 0 0 15px #ff8ab4;
  font-size: 1em;
  opacity: 0;
  animation: heartFlameTrail 0.7s forwards ease-out;
  animation-delay: 0.25s;
}
@keyframes appearScaleBlur {
  0% { opacity: 0; filter: blur(8px); transform: scale(0.8) translateY(10px); }
  100% { opacity: 1; filter: blur(0); transform: scale(1) translateY(0); }
}
@keyframes heartFlameTrail {
  0% { opacity: 1; transform: scale(0.6) translateX(5px); filter: blur(2px); }
  50% { opacity: 0.9; transform: scale(1.1) translateX(18px); }
  100% { opacity: 0; transform: scale(0.5) translateX(35px); filter: blur(6px); }
}

.final-nav-button:hover:not(:disabled) {
  box-shadow: 0 0 22px rgba(255, 143, 180, 0.6);
  transform: translateY(-5px);
}
/* 【MOBILE OPTIMIZATION】触摸反馈 */
.final-nav-button:active:not(:disabled) {
  transform: translateY(1px) scale(0.97);
  box-shadow: 0 0 8px rgba(255, 143, 180, 0.4);
}
.final-nav-button:disabled {
  opacity: 0.3; cursor: not-allowed; box-shadow: none;
}

/* 【MOBILE OPTIMIZATION】媒体查询，针对小屏幕设备进行样式覆盖 */
@media (max-width: 768px) {
  .final-main-title {
    font-size: 2.5rem; /* 减小标题字体 */
    letter-spacing: 3px;
    margin-bottom: 2rem;
  }
  .final-message-line {
    font-size: 1.1rem; /* 减小正文字体 */
    margin: 0.6rem 0;
    /* 在移动端，过长的句子可以换行 */
    white-space: normal;
  }
  .final-button-container {
    margin-top: 3rem; /* 减小按钮与文字的间距 */
    gap: 1rem; /* 减小按钮间距 */
  }
  .final-nav-button {
    padding: 12px 24px; /* 减小按钮尺寸 */
    font-size: 1rem;
  }
}
</style>