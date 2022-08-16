<script setup>
import { ref, onMounted, computed } from 'vue'
const wins = ref(0)
const draws = ref(0)
const losses = ref(0)

const choice = ref(null)
const computerChoice = ref(null)
const verdict = ref(null)

const outcomes = {
  камень: {
    камень: 'ничья',
    бумага: 'проиграл',
    ножницы: 'победил',
  },
  ножницы: {
    камень: 'проиграл',
    бумага: 'победил',
    ножницы: 'ничья',
  },
  бумага: {
    камень: 'победил',
    бумага: 'ничья',
    ножницы: 'проиграл',
  }
}
const winPercentage = computed(() => {
  const total = wins.value + draws.value + losses.value
  return total ? (wins.value / total) * 100 : 0
})

const play = c => {
  choice.value = c

  const choices = ['камень','ножницы','бумага']
  const random = Math.floor(Math.random() * choices.length)
  computerChoice.value = choices[random]

  const outcome = outcomes[c][computerChoice.value]

  if (outcome === 'победил') {
    wins.value++
    verdict.value = 'Ты победил!'
  }else if (outcome === 'проиграл') {
    losses.value++
    verdict.value = 'Ты проиграл :('
  }else {
    draws.value++
    verdict.value = 'Ничья...'
  }

  SaveGame()
} 

const SaveGame = () => {
  localStorage.setItem('победил', wins.value)
  localStorage.setItem('ничья', draws.value)
  localStorage.setItem('проиграл', losses.value)
}
const LoadGame = () => {
  wins.value = parseInt(localStorage.getItem('победил')) || 0
  draws.value = parseInt(localStorage.getItem('ничья')) || 0
  losses.value = parseInt(localStorage.getItem('проиграл')) || 0
}
const ResetRound =() => {
  choice.value = null
  computerChoice.value = null
  verdict.value = null
}

onMounted(() => {
  LoadGame()

  window.addEventListener('keypress', e => {
    if(e.key === 'r'){
      ResetRound()
    }
  })
})



</script>

<template>
  <div class='bg-black 700 text-white text-center min-h-screen flex flex-col'>
    <header class='container mx-auto p-6'>
      <h1 class='text-4xl font-bold text-pink-500'>Камень</h1>
      <h1 class='text-4xl font-bold text-yellow-500'>Ножницы</h1>
      <h1 class='text-4xl font-bold text-green-500'>Бумага</h1>
    </header>

    <main class="container mx-auto p-6 flex-1">
      <div v-if="choice === null" class="flex items-center justify-center -mx-6">
        <button 
          @click="play('камень')" 
          class="bg-white rounded-full shadow-lg w-64 p-12 mx-6
          transition-colors duration-300 hover:bg-pink-500">
            <img src="./assets/rock-hand.png" alt="Камень" class="w-full">
        </button>

         <button 
          @click="play('ножницы')" 
          class="bg-white rounded-full shadow-lg w-64 p-12 mx-6
          transition-colors duration-300 hover:bg-yellow-500">
            <img src="./assets/scissors-hand.png" alt="Бумага" class="w-full">
        </button>

        <button 
          @click="play('бумага')" 
          class="bg-white rounded-full shadow-lg w-64 p-12 mx-6
          transition-colors duration-300 hover:bg-green-500">
            <img src="./assets/paper-hand.png" alt="Ножницы" class="w-full">
        </button>
      </div>

      <div v-else>
        <div class="text-3xl mb-4">
          Ты выбрал <span class="text-pink-500">{{ choice }}</span>
        </div>

        <div class="text-3xl mb-4">
          Компьютер выбрал <span class="text-green-500">{{ computerChoice }}</span>
        </div>

        <div class="text-6xl mb-12">
          {{verdict}}
        </div>

        <button @click="ResetRound" class="bg-pink-500 text-lg py-2 px-4">Начать заново</button>
      </div>

      <div class="mt-12 text-3xl mb-4 text-green-500">
        Кол-во побед {{ wins }}
      </div>
      <div class="mt-12 text-3xl mb-4 text-white-500">
       Ничья {{ draws }}
      </div>
      <div class="mt-12 text-3xl mb-4 text-red-500">
        Кол-во проигрышей {{ losses }}
      </div>

      <div class="text-lg">
        Процент победы: {{ Math.round(winPercentage) }}%
      </div>
    </main>
  </div>
</template>

