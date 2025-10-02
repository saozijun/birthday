<template>
  <div class="w-full h-screen bg-gradient-to-br from-yellow-900 to-orange-900 flex flex-col items-center justify-center text-white">
    <!-- Component Title -->
    <h1 class="text-4xl font-bold mb-8">小猫视图组件</h1>
    
    <!-- Navigation Buttons -->
    <div class="flex gap-8">
      <button 
        @click="goPrevious"
        class="bg-gray-600 hover:bg-gray-700 text-white px-6 py-3 rounded-lg font-semibold transition-all duration-200">
        ← 上一页
      </button>
      
      <button 
        @click="goNext"
        class="bg-yellow-600 hover:bg-yellow-700 text-white px-6 py-3 rounded-lg font-semibold transition-all duration-200">
        下一页 →
      </button>
    </div>
  </div>
</template>

<script setup>
const emit = defineEmits(['next-screen', 'previous-screen'])

function goNext() {
  emit('next-screen')
}

function goPrevious() {
  emit('previous-screen')
}
</script>