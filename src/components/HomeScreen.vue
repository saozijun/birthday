<template>
  <div class="box">
    <div class="stars" id="stars1"></div>
    <div class="stars" id="stars2"></div>
    <div class="stars" id="stars3"></div>

    <div v-if="!showWishPopup && !isLaunching" class="main-content">
      <h1 class="title">Click to Generate Fireworks</h1>
      <div class="countdown">{{ countdownText }}</div>
      <button @click="startLaunch" class="launch-btn">
        <span>🚀 Launch the Celebration 🚀</span>
      </button>
    </div>

    <div v-if="showWishPopup" class="popup-overlay">
      <div class="popup">
        <h2 class="popup-title">🎂 Make a Birthday Wish 🎂</h2>
        <textarea 
          v-model="wishText" 
          placeholder="Write your heart's desire here..." 
          class="popup-textarea"
        ></textarea>
        <button @click="confirmWish" class="popup-btn">✨ Seal the Wish ✨</button>
      </div>
    </div>
  </div>

  <YH ref="yhComponentRef"/>
  <XK />
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import YH from './yh.vue'
import XK from './xk.vue'

const yhComponentRef = ref(null)
const emit = defineEmits(['next-screen'])

const countdownText = ref('')
let timer

// Popup control
const showWishPopup = ref(false)
const wishText = ref('')
const isLaunching = ref(false) // Controls hiding the original content

function updateCountdown() {
  const now = new Date()
  const target = new Date(`${now.getFullYear()}-12-11 00:00:00`)
  if (now > target) {
    target.setFullYear(target.getFullYear() + 1)
  }
  const diff = target - now

  if (diff <= 0) {
    clearInterval(timer)
    countdownText.value = "Happy Birthday!"
    return
  }

  const days = Math.floor(diff / (1000 * 60 * 60 * 24))
  const hours = Math.floor((diff / (1000 * 60 * 60)) % 24)
  const minutes = Math.floor((diff / (1000 * 60)) % 60)
  const seconds = Math.floor((diff / 1000) % 60)

  countdownText.value = `${days}d ${hours}h ${minutes}m ${seconds}s`
}

// Click launch button -> Hide content + Start YH + Show popup after a delay
function startLaunch() {
  isLaunching.value = true

  if (yhComponentRef.value) {
    yhComponentRef.value.setAutoLaunch(true)
  }

  setTimeout(() => {
    showWishPopup.value = true
  }, 10000) 
}

// Confirm wish button
function confirmWish() {
  if (!wishText.value.trim()) {
    alert("Don't be shy, make a wish! 🎉")
    return
  }

  showWishPopup.value = false
  
  // Transition to the next screen
  emit('next-screen')
}

onMounted(() => {
  updateCountdown()
  timer = setInterval(updateCountdown, 1000)
})

onUnmounted(() => {
  clearInterval(timer)
})
</script>

<style scoped>
.box {
  width: 100vw;
  height: 100vh;
  position: relative;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  color: white;
  text-align: center;
  font-family: 'Poppins', 'Segoe UI', sans-serif;
  z-index: 10;
}

@keyframes move-stars {
  from { transform: translateY(0px); }
  to   { transform: translateY(-2000px); }
}

.stars {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
  display: block;
  z-index: -1;
}

#stars1 {
  background: transparent url('https://www.script-tutorials.com/demos/360/images/stars.png') repeat top center;
  animation: move-stars 200s linear infinite;
}
#stars2 {
  background: transparent url('https://www.script-tutorials.com/demos/360/images/twinkling.png') repeat top center;
  animation: move-stars 100s linear infinite;
}
#stars3 {
  background: transparent url('https://www.script-tutorials.com/demos/360/images/clouds.png') repeat top center;
  animation: move-stars 50s linear infinite;
}

/* --- Main Content Styling --- */
.main-content {
  animation: fade-in 1.5s ease-in-out;
}

.title {
  font-size: 3.5rem;
  font-weight: 700;
  margin-bottom: 2rem;
  letter-spacing: 2px;
  background: linear-gradient(125deg, #ffd700, #ff8cbf, #e1a7ff);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  animation: shimmer 4s ease-in-out infinite alternate;
}

.countdown {
  font-size: 2.2rem;
  font-weight: 400;
  margin-bottom: 2.5rem;
  color: rgba(255, 255, 255, 0.85);
  text-shadow: 0 0 15px rgba(255, 192, 203, 0.5);
}

.launch-btn {
  background: rgba(255, 255, 255, 0.1);
  color: #fff;
  font-weight: bold;
  font-size: 1.2rem;
  padding: 1rem 2rem;
  border-radius: 50px;
  border: 2px solid rgba(255, 255, 255, 0.3);
  cursor: pointer;
  transition: all 0.3s ease;
  backdrop-filter: blur(5px);
  position: relative;
  overflow: hidden;
}

.launch-btn:before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(120deg, transparent, rgba(255, 255, 255, 0.4), transparent);
  transition: all 0.5s ease;
}

.launch-btn:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
  border-color: rgba(255, 192, 203, 0.7);
}

.launch-btn:hover:before {
  left: 100%;
}


/* --- Popup Styles (Glassmorphism) --- */
.popup-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.3);
  backdrop-filter: blur(10px);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 200;
}

.popup {
  background: rgba(30, 30, 47, 0.75);
  border: 1px solid rgba(255, 255, 255, 0.2);
  border-radius: 1.5rem;
  padding: 2.5rem;
  width: 90%;
  max-width: 520px;
  text-align: center;
  color: #fff;
  box-shadow: 0 15px 35px rgba(0,0,0,0.3);
  animation: popup-appear 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.popup-title {
  font-size: 1.8rem;
  margin-bottom: 1.5rem;
  font-weight: 600;
}

.popup-textarea {
  width: 100%;
  min-height: 120px;
  padding: 1rem;
  border-radius: 0.75rem;
  border: 1px solid rgba(255, 255, 255, 0.2);
  outline: none;
  margin-bottom: 1.5rem;
  font-size: 1.1rem;
  font-family: inherit;
  resize: vertical;
  background: rgba(0, 0, 0, 0.2);
  color: #fff;
  transition: border-color 0.3s, box-shadow 0.3s;
}

.popup-textarea::placeholder {
  color: rgba(255, 255, 255, 0.5);
}

.popup-textarea:focus {
  border-color: #ff8cbf;
  box-shadow: 0 0 15px rgba(255, 140, 191, 0.5);
}

.popup-btn {
  background: linear-gradient(135deg, #ff7eb3, #ffb07b);
  border: none;
  border-radius: 50px;
  padding: 0.8rem 2rem;
  font-size: 1.1rem;
  font-weight: bold;
  color: #fff;
  cursor: pointer;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  box-shadow: 0 5px 15px rgba(255, 126, 179, 0.3);
}

.popup-btn:hover {
  transform: scale(1.05) translateY(-2px);
  box-shadow: 0 8px 25px rgba(255, 126, 179, 0.5);
}

/* --- Keyframe Animations --- */
@keyframes fade-in {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}

@keyframes popup-appear {
  from { transform: scale(0.8) translateY(50px); opacity: 0; }
  to { transform: scale(1) translateY(0); opacity: 1; }
}

@keyframes shimmer {
  from {
    text-shadow: 0 0 15px #ffd700, 0 0 25px #ff8cbf;
  }
  to {
    text-shadow: 0 0 20px #e1a7ff, 0 0 35px #ffffff;
  }
}
</style>