<template>
  <div class="w-full h-screen bg-cover bg-center ov" :style="{ backgroundImage: `url(${backgroundImage})` }">
    <div class="absolute inset-0 bg-black bg-opacity-20"></div>
    <div class="relative w-full h-full flex flex-col items-center justify-center text-white p-4">
      <div class="text-center z-10">
        <p v-for="line in visibleLines" :key="line.id"
          class="text-3xl sm:text-4xl lg:text-5xl font-bold mb-8 animate-letter-by-letter" v-html="line.html">
        </p>
      </div>


      <div class="absolute inset-0 overflow-hidden pointer-events-none z-0">
        <div v-for="glow in glowingElements" :key="glow.id" class="glowing-element" :style="glow.style"></div>
      </div>

      <div class="absolute bottom-0 w-full h-full overflow-hidden">
        <img v-for="balloon in balloons" :key="balloon.id" :src="balloon.src" class="absolute animate-rise-and-sway"
          :style="balloon.style">
      </div>

      <button v-if="showContinue" @click="goNext" class="continue-btn animate-fade-in">
        Continue
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
// Your image imports
import hkBg from '@/assets/images/hk-bg.jpg'
import q1 from '@/assets/images/q6.png'
import q2 from '@/assets/images/q7.png'
import q3 from '@/assets/images/q8.png'
import q4 from '@/assets/images/q9.png'
import q5 from '@/assets/images/q2.png'
import q6 from '@/assets/images/q3.webp'

const emit = defineEmits(['next-screen']);

const backgroundImage = ref(hkBg);

const lines = [
  "Wishing you a day filled with happiness",
  "and a year filled with joy.",
  "May all your dreams turn into reality.",
  "Happy Birthday!",
];

// MODIFIED: visibleLines now stores objects to handle the HTML
const visibleLines = ref([]);
const currentLineIndex = ref(0);
const showContinue = ref(false);

const balloonImages = [q1, q2, q3, q4, q5, q6];
const balloons = ref([]);
const glowingElements = ref([]);

function showNextLine() {
  if (currentLineIndex.value < lines.length) {
    const lineText = lines[currentLineIndex.value];

    // MODIFIED: Split line into characters and wrap each in a span with a delay
    const lineHtml = lineText.split('').map((char, index) => {
      // Give space a span too, so timing is consistent
      if (char === ' ') {
        return ' ';
      }
      // Stagger the animation of each letter
      return `<span style="animation-delay: ${index * 50}ms">${char}</span>`;
    }).join('');

    visibleLines.value.push({
      id: currentLineIndex.value,
      html: lineHtml
    });

    currentLineIndex.value++;
    setTimeout(showNextLine, 2500); // Increased delay to allow for animation
  } else {
    setTimeout(() => {
      showContinue.value = true;
    }, 1000);
  }
}


function createBalloons() {
  for (let i = 0; i < 15; i++) {
    const balloonSrc = balloonImages[Math.floor(Math.random() * balloonImages.length)];
    const size = Math.random() * 60 + 20;
    const swayAmount = (Math.random() - 0.5) * 60;

    balloons.value.push({
      id: i,
      src: balloonSrc,
      style: {
        left: `${Math.random() * 95}%`,
        width: `${size}px`,
        height: 'auto',
        animationDuration: `${Math.random() * 10 + 15}s`,
        animationDelay: `${Math.random() * 15}s`,
        '--sway-amount': `${swayAmount}px`,
      }
    });
  }
}

function createGlowingElements() {
  for (let i = 0; i < 50; i++) {
    const size = Math.random() * 3 + 1;
    glowingElements.value.push({
      id: i,
      style: {
        left: `${Math.random() * 100}%`,
        top: `${Math.random() * 100}%`,
        width: `${size}px`,
        height: `${size}px`,
        animationDuration: `${Math.random() * 8 + 5}s`,
        animationDelay: `${Math.random() * 10}s`,
        opacity: `${Math.random() * 0.7 + 0.3}`,
      }
    });
  }
}


function goNext() {
  emit('next-screen');
}

onMounted(() => {
  showNextLine();
  createBalloons();
  createGlowingElements();
});
</script>

<style>
/* Unchanged balloon and glow animations */
@keyframes rise-and-sway {
  0% { transform: translateY(100vh) translateX(0); opacity: 1; }
  25% { transform: translateY(75vh) translateX(var(--sway-amount)); }
  50% { transform: translateY(50vh) translateX(calc(var(--sway-amount) * -1)); }
  75% { transform: translateY(25vh) translateX(var(--sway-amount)); }
  100% { transform: translateY(-200px) translateX(0); opacity: 0; }
}
.animate-rise-and-sway {
  animation-name: rise-and-sway;
  animation-timing-function: ease-in-out;
  animation-iteration-count: infinite;
  animation-fill-mode: backwards;
}
.glowing-element {
  position: absolute;
  background-color: #ffd700;
  border-radius: 50%;
  box-shadow: 0 0 5px #ffd700, 0 0 10px #ffd700, 0 0 15px rgba(255, 215, 0, 0.5);
  animation: glow-float linear infinite;
  pointer-events: none;
}
@keyframes glow-float {
  0% { transform: translate(0, 0) scale(1) rotate(0deg); opacity: 0; }
  20% { opacity: 0.8; }
  100% { transform: translate(calc(var(--sway-amount, 0px) * 2), -100vh) scale(0.5) rotate(720deg); opacity: 0; }
}


/* MODIFIED: New letter-by-letter text animation */
@keyframes letter-reveal {
  0% {
    opacity: 0;
    transform: translateY(30px) scale(0.8) skew(10deg);
  }
  100% {
    opacity: 1;
    transform: translateY(0) scale(1) skew(0deg);
  }
}

/* NEW: Shimmer sweep animation */
@keyframes shimmer-sweep {
  0% {
    transform: translateX(-100%) skew(-20deg);
  }
  100% {
    transform: translateX(100%) skew(-20deg);
  }
}

.animate-letter-by-letter {
  position: relative; /* Needed for the ::after pseudo-element */
  color: #ffffff;
  text-shadow:
    0 0 10px rgba(215, 230, 255, 0.8),
    0 0 20px rgba(123, 189, 254, 0.6);
}

/* MODIFIED: Target spans inside the paragraph */
.animate-letter-by-letter > span {
  display: inline-block; /* Essential for transforms to work */
  opacity: 0; /* Start hidden */
  animation-name: letter-reveal;
  animation-duration: 0.8s;
  animation-timing-function: cubic-bezier(0.25, 0.46, 0.45, 0.94);
  animation-fill-mode: forwards; /* Stay visible after animation */
}


/* Unchanged button styles */
@keyframes fadeIn {
  from { opacity: 0; transform: scale(0.95); }
  to { opacity: 1; transform: scale(1); }
}
.animate-fade-in {
  animation: fadeIn 0.5s ease-out forwards;
}
.continue-btn {
  position: absolute;
  bottom: 2rem;
  right: 2rem;
  z-index: 5;
  padding: 0.8rem 1.8rem;
  background: #ffffff;
  color: #000000;
  font-weight: bold;
  font-size: 1rem;
  border: none;
  border-radius: 50px;
  cursor: pointer;
  user-select: none;
  transition: all 0.3s ease;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.2);
}

.continue-btn:hover {
  background-color: #f0f0f0;
  transform: scale(1.05);
}

</style>