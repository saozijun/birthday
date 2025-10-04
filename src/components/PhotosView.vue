<template>
  <main @touchstart="handleTouchStart" @touchmove="handleTouchMove" @touchend="handleTouchEnd">
    <ul class="slider">
      <li v-for="item in items" :key="item.title" class="item" :style="{ backgroundImage: `url(${item.img})` }">
        <div class="content">
          <h2 class="title">{{ item.title }}</h2>
          <p class="description">{{ item.description }}</p>
        </div>
      </li>
    </ul>

    <nav class="nav">
      <div class="btn" @click="prev"></div>
      <div class="btn" @click="next"></div>
    </nav>


    <div v-if="showContinueButton" @click="goNext" class="continue-btn animate-fade-in">
      <button type="button" class="btn2">Continue</button>
    </div>

  </main>
</template>

<script setup>
import { ref } from 'vue'

// Original carousel data
const originItems = [
  {
    title: 'Lossless Youths',
    img: 'https://cdn.mos.cms.futurecdn.net/dP3N4qnEZ4tCTCLq59iysd.jpg',
    description:
      'Lorem ipsum, dolor sit amet consectetur adipisicing elit. Tempore fuga voluptatum, iure corporis inventore praesentium nisi. Id laboriosam ipsam enim.',
  },
  {
    title: 'Estrange Bond',
    img: 'https://i.redd.it/tc0aqpv92pn21.jpg',
    description:
      'Lorem ipsum, dolor sit amet consectetur adipisicing elit. Tempore fuga voluptatum, iure corporis inventore praesentium nisi. Id laboriosam ipsam enim.',
  },
  {
    title: 'The Gate Keeper',
    img: 'https://wharferj.files.wordpress.com/2015/11/bio_north.jpg',
    description:
      'Lorem ipsum, dolor sit amet consectetur adipisicing elit. Tempore fuga voluptatum, iure corporis inventore praesentium nisi. Id laboriosam ipsam enim.',
  },
  {
    title: 'Last Trace Of Us',
    img: 'https://images7.alphacoders.com/878/878663.jpg',
    description:
      'Lorem ipsum, dolor sit amet consectetur adipisicing elit. Tempore fuga voluptatum, iure corporis inventore praesentium nisi. Id laboriosam ipsam enim.',
  },
  {
    title: 'The Migration',
    img: 'https://da.se/app/uploads/2015/09/simon-december1994.jpg',
    description:
      'Lorem ipsum, dolor sit amet consectetur adipisicing elit. Tempore fuga voluptatum, iure corporis inventore praesentium nisi. Id laboriosam ipsam enim.',
  },
]

const items = ref([...originItems])
const showContinueButton = ref(false)

const updateButtonState = () => {
  if (showContinueButton.value) return

  if (
    items.value.length >= 2 &&
    items.value[1].title === originItems[originItems.length - 1].title
  ) {
    showContinueButton.value = true
  }
}

const next = () => {
  const first = items.value.shift()
  items.value.push(first)
  updateButtonState()
}

const prev = () => {
  const last = items.value.pop()
  items.value.unshift(last)
  updateButtonState()
}

const emit = defineEmits(['next-screen'])
function goNext() {
  emit('next-screen')
}

// Logic for touch/swipe controls
const touchStartX = ref(0)
const touchMoveX = ref(0)
const swipeThreshold = 50 // Minimum pixels to be considered a swipe

const handleTouchStart = (e) => {
  touchStartX.value = e.touches[0].clientX
  touchMoveX.value = 0
}

const handleTouchMove = (e) => {
  if (touchStartX.value === 0) return
  touchMoveX.value = e.touches[0].clientX - touchStartX.value
}

const handleTouchEnd = () => {
  if (Math.abs(touchMoveX.value) > swipeThreshold) {
    if (touchMoveX.value < 0) {
      next()
    } else {
      prev()
    }
  }
  // Reset values
  touchStartX.value = 0
  touchMoveX.value = 0
}
</script>

<style scoped>
main {
  position: relative;
  width: 100%;
  height: 100vh;
  height: 100dvh;
  overflow: hidden;
}

.item {
  width: 200px;
  height: 300px;
  list-style-type: none;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  z-index: 1;
  background-position: center;
  background-size: cover;
  border-radius: 20px;
  box-shadow: 0 20px 30px rgba(255, 255, 255, 0.3) inset;
  transition: transform 0.1s, left 0.75s, top 0.75s, width 0.75s, height 0.75s;
}

.item:nth-child(1),
.item:nth-child(2) {
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  transform: none;
  border-radius: 0;
  box-shadow: none;
  opacity: 1;
}

.item:nth-child(3) {
  left: 50%;
}

.item:nth-child(4) {
  left: calc(50% + 220px);
}

.item:nth-child(5) {
  left: calc(50% + 440px);
}

.item:nth-child(6) {
  left: calc(50% + 660px);
  opacity: 0;
}

.content {
  width: min(30vw, 400px);
  position: absolute;
  top: 50%;
  left: 5rem;
  transform: translateY(-50%);
  font: 400 1rem 'Helvetica Neue', Helvetica, sans-serif;
  color: white;
  text-shadow: 0 1px 5px rgba(0, 0, 0, 0.4);
  z-index: 2;
  display: none;
  padding: 2rem;
  background: rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border-radius: 10px;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.item:nth-of-type(2) .content {
  display: block;
}

.content .title {
  font-family: 'Arial Black', Gadget, sans-serif;
  font-size: clamp(1.5rem, 3vw, 2.5rem);
  text-transform: uppercase;
  margin-bottom: 0.5rem;
}

.content .description {
  line-height: 1.6;
  margin: 1rem 0 2rem;
  font-size: 0.9rem;
  font-weight: 300;
}

.nav {
  position: absolute;
  bottom: 2rem;
  left: 50%;
  transform: translateX(-50%);
  z-index: 5;
  user-select: none;
  display: flex;
  align-items: center;
  gap: 1rem;
}

.nav .btn {
  position: relative;
  background-color: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  width: 50px;
  height: 50px;
  border-radius: 50%;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: background-color 0.3s ease;
  backdrop-filter: blur(5px);
  -webkit-backdrop-filter: blur(5px);
}

.nav .btn:hover {
  background-color: rgba(255, 255, 255, 0.3);
}

.nav .btn::before {
  content: '';
  position: absolute;
  width: 10px;
  height: 10px;
  border-top: 2px solid white;
  border-left: 2px solid white;
}

.nav .btn:first-child::before {
  transform: translateX(2px) rotate(-45deg);
}

.nav .btn:last-child::before {
  transform: translateX(-2px) rotate(135deg);
}

.continue-btn {
  position: absolute;
  bottom: 2rem;
  right: 2rem;
  z-index: 5;
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

/* Styles for Mobile Devices */
@media (max-width: 768px) {

  .item:nth-child(3),
  .item:nth-child(4),
  .item:nth-child(5) {
    opacity: 0;
    left: 50%;
    transform: translate(-50%) scale(0.9);
  }

  .content {
    left: 50%;
    top: auto;
    bottom: 25%;
    /* Raised slightly to avoid overlap with buttons */
    width: 90%;
    transform: translateX(-50%);
    padding: 1.5rem;
    text-align: center;
  }

  .content .title {
    font-size: clamp(1.2rem, 5vw, 1.8rem);
  }

  .content .description {
    font-size: 0.8rem;
    margin-bottom: 1rem;
  }

  /* --- NEW: Position nav to the bottom-left on mobile --- */
  .nav {
    left: 2rem;
    transform: none;
  }

  /* --- NEW: Position continue button to the bottom-right on mobile --- */
  .continue-btn {
    right: 2rem;
    left: auto;
    transform: none;
  }
}

.btn2 {
  outline: none;
  color: #DAA06D;
  padding: .6em;
  padding-left: 2em;
  padding-right: 2em;
  border: 2px dashed #DAA06D;
  border-radius: 15px;
  background-color: #EADDCA;
  box-shadow: 0 0 0 4px #EADDCA, 2px 2px 4px 2px rgba(0, 0, 0, 0.5);
  transition: .1s ease-in-out, .4s color;
}

.btn2:active {
  transform: translateX(0.1em) translateY(0.1em);
  box-shadow: 0 0 0 4px #EADDCA, 1.5px 1.5px 2.5px 1.5px rgba(0, 0, 0, 0.5);
}
</style>