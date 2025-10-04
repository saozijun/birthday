<template>
  <div class="cat-viewer-container">
    <div class="background-decorations">
      <div class="blur-circle circle1"></div>
      <div class="blur-circle circle2"></div>
    </div>

    <div class="main-content">
      <div class="cat-window">
        <img :src="catImage" alt="一只可爱的猫咪" class="cat-image" :style="mMImg"/>

        <div class="interactive-item cake-item" @click="showBirthdayMessage">
          <img src="../assets/images/dg.png" alt="生日蛋糕" />
          <span class="tooltip">点击蛋糕</span>
        </div>

        <div class="interactive-item gift-item" @click="showConfessionMessage">
          <img src="../assets/images/lwh.png" alt="礼物盒" />
          <span class="tooltip">点击礼物</span>
        </div>

        <transition name="fade">
          <div v-if="showMessage" class="dialog-box">
            <p ref="messageText">
              {{ typedMessage }}<span v-if="isTyping" class="typing-cursor">|</span>
            </p>
            <div class="dialog-arrow"></div>
          </div>
        </transition>
      </div>

      <div v-if="showConfessionButtons" class="confession-buttons">
        <button
          @click="handleAccept"
          class="btn accept-btn"
          :class="{ enlarged: refusalCount > 0 }"
          :style="acceptButtonStyle"
        >
          接受
        </button>

        <button @click="handleReject" class="btn reject-btn" :class="{ shrunken: refusalCount > 0 }">
          {{ rejectButtonText }}
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import m1 from "../assets/images/mm.gif";
import m2 from "../assets/images/m2.gif";
import m3 from "../assets/images/m3.gif";
import m4 from "../assets/images/m4.gif";
import m5 from "../assets/images/m5.jpg";
const emit = defineEmits(["next-screen", "previous-screen"]);

// 猫咪图片状态
const catImages = {
  normal: m1, // 正常
  happy: m4, // 开心
  sad: m3, // 难过
  surprised: m5, // 惊讶
};
const isStart = ref(false)
const catImage = ref(catImages.normal);

// 对话框相关
const showMessage = ref(false);
const currentMessage = ref("");
const typedMessage = ref("");
const isTyping = ref(false);
const messageText = ref(null);

let typingTimeout = null;

const typeMessage = (
  text,
  callback = () => { }
) => {
  if (typingTimeout) clearTimeout(typingTimeout);
  currentMessage.value = text;
  typedMessage.value = "";
  showMessage.value = true;
  isTyping.value = true;
  let i = 0;
  const typeChar = () => {
    if (i < currentMessage.value.length) {
      typedMessage.value += currentMessage.value.charAt(i);
      i++;
      typingTimeout = setTimeout(typeChar, 70); // 打字速度
    } else {
      isTyping.value = false;
      setTimeout(() => {
        showMessage.value = false;
        callback();
      }, 2000); // 消息显示2秒后消失
    }
  };
  typeChar();
};

const showBirthdayMessage = () => {
  if (showMessage.value || isStart.value) return;
  catImage.value = catImages.happy;
  setTimeout(()=>{
    catImage.value = catImages.normal;
  },3000)
  typeMessage("祝你生日快乐");
};

// 告白相关
const showConfessionButtons = ref(false);
const refusalCount = ref(0);
const rejectButtonTexts = ["拒绝", "不要点这里", "哒咩", "求你了", "呜呜呜..."];

const rejectButtonText = computed(() => {
  return rejectButtonTexts[
    Math.min(refusalCount.value, rejectButtonTexts.length - 1)
  ];
});

// NEW: COMPUTED PROPERTY FOR DYNAMIC BUTTON STYLE
const acceptButtonStyle = computed(() => {
  const scale = 1 + Math.min(refusalCount.value, 3) * 0.3;
  return {
    transform: `scale(${scale})`,
    transition: 'transform 0.3s ease'
  };
});

const mMImg = computed(() => {
  const scale = 1 + Math.min(refusalCount.value, 3) * 0.3;
  return {
    left: catImages.normal == catImage.value ? '54%' : '50%',
    transform: `translateX(-50%) scale(${scale})`,
  };
});
const showConfessionMessage = () => {
  if (showMessage.value) return;
  isStart.value = false
  catImage.value = catImages.surprised;
  typeMessage("其实……我好像有点喜欢你……", () => {
    showConfessionButtons.value = true;
  });
};

const handleAccept = () => {
  catImage.value = catImages.happy;
  showConfessionButtons.value = false;
  typeMessage("太好啦！最喜欢你了！");
  setTimeout(() => {
    emit("next-screen");
  }, 1800);
};

const handleReject = () => {
  refusalCount.value++;
  catImage.value = catImages.sad;
};

// 导航
const goNext = () => emit("next-screen");
const goPrevious = () => emit("previous-screen");
</script>

<style scoped>
/* 全局和容器样式 */
.cat-viewer-container {
  width: 100%;
  height: 100vh;
  background: url("../assets/images/c-bg.png") no-repeat center center;
  background-size: cover;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: white;
  position: relative;
  overflow: hidden;
}

.main-content {
  position: relative;
  z-index: 10;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

/* 背景装饰 */
.background-decorations {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
  opacity: 0.3;
}

.blur-circle {
  position: absolute;
  border-radius: 50%;
  mix-blend-mode: screen;
  filter: blur(80px);
  animation: pulse 5s infinite ease-in-out alternate;
}

.circle1 {
  width: 384px;
  height: 384px;
  background-color: #fcd34d;
  top: 25%;
  left: 25%;
}

.circle2 {
  width: 320px;
  height: 320px;
  background-color: #fb923c;
  bottom: 25%;
  right: 25%;
  animation-delay: 2.5s;
}

/* 标题 */
.component-title {
  font-size: 3rem;
  font-weight: 800;
  margin-bottom: 3rem;
  text-shadow: 2px 2px 8px rgba(0, 0, 0, 0.4);
  letter-spacing: 0.1em;
}

/* 猫咪窗口 */
.cat-window {
  position: relative;
  /* Added for better item positioning */
  width: 600px;
  height: 400px;
  padding: 1.5rem;
  display: flex;
  align-items: flex-end;
  justify-content: center;
}

/* 猫咪图片 */
.cat-image {
  position: absolute;
  bottom: 0rem;
  left: 54%;
  transform: translateX(-50%);
  height: 280px;
  object-fit: contain;
  filter: drop-shadow(0 10px 15px rgba(0, 0, 0, 0.3));
  z-index: 10;
  transition: all 0.3s ease-in-out;
}

.bounce-slight {
  animation: bounce-slight 2s infinite ease-in-out;
}

/* 可交互物品 */
.interactive-item {
  position: absolute;
  bottom: 0rem;
  cursor: pointer;
  z-index: 20;
}

.interactive-item img {
  width: 150px;
  object-fit: contain;
  transition: transform 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  filter: drop-shadow(0 4px 6px rgba(0, 0, 0, 0.2));
}

.interactive-item:hover img {
  transform: scale(1.1);
}

.interactive-item:active img {
  transform: scale(0.95);
}

.cake-item {
  left: 0rem;
}

.gift-item {
  right: 1rem;
}

/* 物品提示文字 */
.tooltip {
  position: absolute;
  bottom: -1.5rem;
  left: 50%;
  transform: translateX(-50%);
  font-size: 0.875rem;
  color: rgb(0, 0, 0);
  font-weight: 500;
  opacity: 0;
  transition: opacity 0.2s ease-in-out;
  pointer-events: none;
  white-space: nowrap;
}

.interactive-item:hover .tooltip {
  opacity: 1;
}

/* 对话框 */
.dialog-box {
  position: absolute;
  top: 6rem;
  left: 50%;
  transform: translateX(-50%) translateY(-2rem);
  background-color: white;
  color: #2d3748;
  padding: 0.75rem 1.5rem;
  border-radius: 9999px;
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.2);
  pointer-events: none;
  z-index: 30;
  animation: bubble-up 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.dialog-box p {
  font-weight: 600;
  white-space: nowrap;
  overflow: hidden;
}

.typing-cursor {
  animation: blink 0.7s infinite;
}

.dialog-arrow {
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%) translateY(50%) rotate(45deg);
  width: 1rem;
  height: 1rem;
  background-color: white;
}

/*
  ===============================================
  ============ NEW BUTTON STYLES START ============
  ===============================================
*/

/* 告白按钮容器 */
.confession-buttons {
  display: flex;
  gap: 2rem;
  margin-top: 3rem;
  z-index: 200;
  position: fixed;
  bottom: 8rem;
  left: 50%;
  transform: translateX(-50%);
  perspective: 800px;
  /* Add perspective for 3D effects */
}

/* 按钮通用样式 (高级感基础) */
.btn {
  padding: 1rem 2rem;
  border-radius: 50px;
  font-weight: bold;
  font-size: 1.1rem;
  border: 1px solid rgba(255, 255, 255, 0.2);
  cursor: pointer;
  color: white;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  text-shadow: 0 1px 3px rgba(0, 0, 0, 0.3);
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2),
    inset 0 2px 2px rgba(255, 255, 255, 0.2);
  /* Smoother, more nuanced transition */
  transition: transform 0.3s cubic-bezier(0.2, 1, 0.3, 1),
    box-shadow 0.3s ease-out, background-color 0.3s ease;
}

.btn:hover {
  transform: translateY(-4px) scale(1.05);
  /* Lifts up on hover */
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.3);
}

.btn:active {
  transform: translateY(-1px) scale(0.98);
  /* Click effect */
}

/* 接受按钮 (诱人、发光) */
.accept-btn {
  background: linear-gradient(145deg, #22c55e, #16a34a);
}

.accept-btn:hover {
  background: linear-gradient(145deg, #34d399, #16a34a);
}

/* MODIFIED: When refusalCount > 0, the accept button becomes more attractive */
.accept-btn.enlarged {
  /* The transform is now handled by an inline style in Vue for progressive scaling */
  animation: glowing-accept 1.5s infinite alternate ease-in-out;
}

/* 拒绝按钮 (不稳定、试图逃跑) */
.reject-btn {
  background: linear-gradient(145deg, #ef4444, #dc2626);
}

.reject-btn:hover {
  background: linear-gradient(145deg, #f87171, #dc2626);
}

/* 当拒绝次数 > 0 时，拒绝按钮开始“无厘头”地逃跑 */
.reject-btn.shrunken {
  /* It's not just shrunken, it's trying to escape! */
  animation: run-away 0.6s infinite ease-in-out;
}

/*
  =============================================
  ============ NEW ANIMATIONS START ============
  =============================================
*/

@keyframes glowing-accept {
  0% {
    box-shadow: 0 0 5px #22c55e, 0 0 10px #22c55e, 0 0 15px #34d399,
      0 0 20px #34d399;
  }

  100% {
    box-shadow: 0 0 10px #22c55e, 0 0 20px #22c55e, 0 0 30px #34d399,
      0 0 40px #34d399;
  }
}

@keyframes run-away {
  0% {
    transform: scale(0.9) translate(0px, 0px) rotate(0deg);
    opacity: 0.9;
  }

  20% {
    transform: scale(0.85) translate(30px, -15px) rotate(5deg);
  }

  40% {
    transform: scale(0.9) translate(-25px, 20px) rotate(-3deg);
  }

  60% {
    transform: scale(0.8) translate(20px, 10px) rotate(2deg);
    opacity: 0.7;
  }

  80% {
    transform: scale(0.88) translate(-15px, -15px) rotate(-4deg);
  }

  100% {
    transform: scale(0.9) translate(0px, 0px) rotate(0deg);
    opacity: 0.9;
  }
}

/*
  =============================================
  ============ EXISTING ANIMATIONS ============
  =============================================
*/
/* 动画和过渡 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.slide-up-enter-active {
  transition: transform 0.5s cubic-bezier(0.215, 0.61, 0.355, 1),
    opacity 0.5s ease;
}

.slide-up-leave-active {
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.slide-up-enter-from,
.slide-up-leave-to {
  opacity: 0;
  transform: translateY(20px);
}

@keyframes pulse {
  0% {
    transform: scale(0.95);
  }

  100% {
    transform: scale(1.05);
  }
}

@keyframes bounce-slight {

  0%,
  100% {
    transform: translateX(-50%) translateY(0);
  }

  50% {
    transform: translateX(-50%) translateY(-10px);
  }
}

@keyframes bubble-up {
  0% {
    opacity: 0;
    transform: translateX(-50%) translateY(0);
  }

  100% {
    opacity: 1;
    transform: translateX(-50%) translateY(-2rem);
  }
}

@keyframes blink {
  50% {
    opacity: 0;
  }
}

/* ================================================= */
/* ============== MOBILE RESPONSIVE ============== */
/* ================================================= */
@media (max-width: 768px) {
  .cat-window {
    width: 95vw;
    max-width: 500px;
    height: auto;
    min-height: 350px;
  }

  .cat-image {
    height: 200px;
    /* Make cat image smaller */
  }

  .interactive-item img {
    width: 100px;
    /* Make item images smaller */
  }

  .cake-item {
    left: 1rem;
    /* Adjust item position */
  }

  .gift-item {
    right: 1rem;
    /* Adjust item position */
  }

  .tooltip {
    bottom: -1.2rem;
    font-size: 0.75rem;
  }

  .dialog-box {
    top: 9rem;
    /* Move dialog box up */
    padding: 0.5rem 1rem;
    max-width: 90vw;
  }

  .dialog-box p {
    font-size: 0.9rem;
  }

  .confession-buttons {
    bottom: 2rem;
    /* Adjust button position */
    gap: 1.5rem;
    /* Slightly more space for the wacky animations */
    flex-direction: column;
    /* Stack buttons vertically */
    align-items: center;
    width: 100%;
    /* Ensure it has width for child animations */
  }

  .btn {
    padding: 0.75rem 1.5rem;
    font-size: 1rem;
    width: 220px;
    justify-content: center;
  }

  .component-title {
    font-size: 2.5rem;
    /* Reduce title size */
    margin-bottom: 2rem;
  }
}
</style>