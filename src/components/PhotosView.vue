<template>
  <main>
    <ul class="slider">
      <li
        v-for="item in items"
        :key="item.title"
        class="item"
        :style="{ backgroundImage: `url(${item.img})` }"
      >
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
    
    <div v-if="showContinueButton" class="continue-btn" @click="goNext">Continue</div>
  </main>
</template>

<script setup>
// CHANGED: Removed `computed` from imports
import { ref } from 'vue'

// 原始轮播数据
const originItems = [
  {
    title: 'Lossless Youths',
    img: 'https://cdn.mos.cms.futurecdn.net/dP3N4qnEZ4tCTCLq59iysd.jpg',
    description:
      'Lorem ipsum, dolor sit amet consectetur adipisicing elit. Tempore fuga voluptatum, iure corporis inventore praesentium nisi. Id laboriosam ipsam enim.'
  },
  {
    title: 'Estrange Bond',
    img: 'https://i.redd.it/tc0aqpv92pn21.jpg',
    description:
      'Lorem ipsum, dolor sit amet consectetur adipisicing elit. Tempore fuga voluptatum, iure corporis inventore praesentium nisi. Id laboriosam ipsam enim.'
  },
  {
    title: 'The Gate Keeper',
    img: 'https://wharferj.files.wordpress.com/2015/11/bio_north.jpg',
    description:
      'Lorem ipsum, dolor sit amet consectetur adipisicing elit. Tempore fuga voluptatum, iure corporis inventore praesentium nisi. Id laboriosam ipsam enim.'
  },
  {
    title: 'Last Trace Of Us',
    img: 'https://images7.alphacoders.com/878/878663.jpg',
    description:
      'Lorem ipsum, dolor sit amet consectetur adipisicing elit. Tempore fuga voluptatum, iure corporis inventore praesentium nisi. Id laboriosam ipsam enim.'
  },
  {
    title: 'The Migration',
    img: 'https://da.se/app/uploads/2015/09/simon-december1994.jpg',
    description:
      'Lorem ipsum, dolor sit amet consectetur adipisicing elit. Tempore fuga voluptatum, iure corporis inventore praesentium nisi. Id laboriosam ipsam enim.'
  }
]

const items = ref([...originItems])

const showContinueButton = ref(false)

const updateButtonState = () => {
  if (showContinueButton.value) return

  if (items.value.length >= 2 && items.value[1].title === originItems[originItems.length - 1].title) {
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
</script>

<style scoped>
main {
  position: relative;
  width: 100%;
  height: 100vh;
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
  /* 确保 content 在蒙版之上 */
  z-index: 2;
  display: none;

  /* 增加毛玻璃效果，提升质感 */
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

.content button:hover {
  background-color: rgba(255, 255, 255, 1);
  color: black;
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

/* NEW: Styles for the Continue button */
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