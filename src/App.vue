<template>
  <div class="min-h-screen bg-black text-white flex items-center justify-center">
    <!-- BGM Audio Elements -->
    <audio ref="bgmHome" src="/audio/home.mp3" loop></audio>
    <audio ref="bgmCard" src="/audio/home.mp3" loop></audio>
    <audio ref="bgmPhotos" src="/audio/home.mp3" loop></audio>
    <audio ref="bgmCat" src="/audio/home.mp3" loop></audio>
    <audio ref="bgmFinal" src="/audio/home.mp3" loop></audio>

    <!-- Loading Screen -->
    <div v-if="loading" class="fixed inset-0 bg-black flex items-center justify-center z-50">
      <div class="text-center">
        <div class="text-4xl mb-4 animate-spin">🎂</div>
        <div class="text-2xl mb-4">
          <span v-for="(char, i) in 'Loading...'" :key="i" class="inline-block animate-bounce"
            :style="{ animationDelay: i * 0.1 + 's' }">
            {{ char === ' ' ? '\u00A0' : char }}
          </span>
        </div>
        <div class="text-pink-400">🎈 生日惊喜准备中... 🎁</div>
      </div>
    </div>

    <!-- Main Component -->
    <component :is="currentComponent" @next-screen="nextScreen" @previous-screen="previousScreen"
      @continue="() => startLoading(() => nextScreen())" :countdown="countdown" :photos="photos" />
  </div>
</template>

<script setup>
import { ref, reactive, onMounted, computed } from 'vue'
import HomeScreen from './components/HomeScreen.vue'
import CardView from './components/CardView.vue'
import PhotosView from './components/PhotosView.vue'
import CatView from './components/CatView.vue'
import FinalView from './components/FinalView.vue'
import LoadingScreen from './components/LoadingScreen.vue'

// Screen definitions
const SCREENS = {
  HOME: 'home',
  CARD: 'card',
  PHOTOS: 'photos',
  CAT: 'cat',
  FINAL: 'final',
  LOADING: 'loading'
}

// State management
const screen = ref(SCREENS.HOME)
const loading = ref(false)
const countdown = ref(getCountdown())

// Photo data
const photos = reactive([
  { src: '/images/photo1.jpg', caption: '第一次见面，你的笑容好美' },
  { src: '/images/photo2.jpg', caption: '一起吃饭，一起看电影' },
  { src: '/images/photo3.jpg', caption: '那些美好的回忆' },
  { src: '/images/photo4.jpg', caption: '你在我心中最美的样子' },
  { src: '/images/photo5.jpg', caption: '未来的每一天，都想和你一起度过' },
])

// Audio references
const bgmHome = ref(null)
const bgmCard = ref(null)
const bgmPhotos = ref(null)
const bgmCat = ref(null)
const bgmFinal = ref(null)

// Countdown calculation
function getCountdown() {
  const now = new Date()
  const testMode = true
  if (testMode) {
    const target = new Date(now.getTime() + 10000)
    const total = Math.max(0, target - now)
    return { total, days: 0, hours: 0, minutes: 0, seconds: Math.floor(total / 1000), target }
  } else {
    const year = now.getFullYear()
    let target = new Date(year, 11, 11, 0, 0, 0)
    if (now > target) target.setFullYear(year + 1)
    const total = Math.max(0, target - now)
    return {
      total,
      days: Math.floor(total / (1000 * 60 * 60 * 24)),
      hours: Math.floor((total / (1000 * 60 * 60)) % 24),
      minutes: Math.floor((total / (1000 * 60)) % 60),
      seconds: Math.floor((total / 1000) % 60),
      target
    }
  }
}

onMounted(() => {
  const timer = setInterval(() => {
    countdown.value = getCountdown()
  }, 1000)

  playBGM('home')
})

// BGM management
function playBGM(screenName) {
  ;[bgmHome, bgmCard, bgmPhotos, bgmCat, bgmFinal].forEach(audio => {
    if (audio.value) {
      audio.value.pause()
      audio.value.currentTime = 0
    }
  })
  const audioMap = { home: bgmHome, card: bgmCard, photos: bgmPhotos, cat: bgmCat, final: bgmFinal }
  const targetAudio = audioMap[screenName]
  if (targetAudio?.value) targetAudio.value.play().catch(() => console.log('Autoplay prevented for', screenName))
}

// Screen navigation
function nextScreen() {
  const screenOrder = [SCREENS.HOME, SCREENS.CARD, SCREENS.PHOTOS, SCREENS.CAT, SCREENS.FINAL]
  const currentIndex = screenOrder.indexOf(screen.value)
  if (currentIndex < screenOrder.length - 1) {
    // FIX: Stop all music when entering the loading state.
    playBGM('loading')
    screen.value = SCREENS.LOADING
    setTimeout(() => {
      const nextScreenValue = screenOrder[currentIndex + 1]
      screen.value = nextScreenValue
      playBGM(nextScreenValue)
    }, 3000)
  }
}

function previousScreen() {
  const screenOrder = [SCREENS.HOME, SCREENS.CARD, SCREENS.PHOTOS, SCREENS.CAT, SCREENS.FINAL]
  const currentIndex = screenOrder.indexOf(screen.value)
  if (currentIndex > 0) {
    // FIX: Stop all music when entering the loading state.
    playBGM('loading')
    screen.value = SCREENS.LOADING
    setTimeout(() => {
      const prevScreenValue = screenOrder[currentIndex - 1]
      screen.value = prevScreenValue
      playBGM(prevScreenValue)
    }, 3000)
  }
}

// Loading management
function startLoading(callback) {
  loading.value = true
  const duration = 3000
  setTimeout(() => {
    loading.value = false
    if (callback) callback()
  }, duration)
}

// Current component computed property
const currentComponent = computed(() => {
  switch (screen.value) {
    case SCREENS.HOME: return HomeScreen
    case SCREENS.CARD: return CardView
    case SCREENS.PHOTOS: return PhotosView
    case SCREENS.CAT: return CatView
    case SCREENS.FINAL: return FinalView
    case SCREENS.LOADING: return LoadingScreen
    default: return HomeScreen
  }
})
</script>

<style scoped>
@keyframes bounce {

  0%,
  20%,
  50%,
  80%,
  100% {
    transform: translateY(0);
  }

  40% {
    transform: translateY(-10px);
  }

  60% {
    transform: translateY(-5px);
  }
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(360deg);
  }
}

.animate-bounce {
  animation: bounce 1s infinite;
}

.animate-spin {
  animation: spin 2s linear infinite;
}
</style>
