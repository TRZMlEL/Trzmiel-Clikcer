<template>
  <div class="min-h-screen flex bg-zinc-900">
    <main class="flex-1 h-screen flex flex-col justify-between">
      <header class="h-1/6 flex items-center justify-center text-zinc-50">
        <h1 class="text-4xl font-bold mb-4 align-text-bottom translate-x-4 select-none">{{ count }}<span class="m-0 align-text-top">🍯</span></h1>
      </header>
      <div class="h-max flex items-center justify-center ">
        <div class="text-center flex-col flex gap-2">
          <img @click="click($event)" src="../img/bumblebee.png" class="select-none cursor-pointer hover:mix-blend-plus-lighter rounded-3xl h-48 active:scale-105 active:rotate-3 rotate-2" draggable="false">
          <transition-group name="fade" tag="div">
            <div
              v-for="(one, index) in ones"
              :key="one.id"
              :style="{ top: (one.y - 20) + 'px', left: one.x + 'px' }"
              class="absolute text-zinc-50 text-2xl font-bold animate-move-up select-none pointer-events-none"
            >
              +1
            </div>
          </transition-group>
          <input type="number" v-model="inputValue" @keyup.enter="addToCount" placeholder="Wprowadź liczbę">
        </div>
      </div>
      <div class="flex w-full h-1/5 gap-4 p-4">
        <div class="w-full h-full"><img src="../img/flowerField1.jpg" class="bg-green-800 block w-full h-full rounded-xl object-cover mix-blend-normal" /><p class="text-white font-semibold text-center align-middle relative -translate-y-20 select-none text-2xl">x1</p></div>
        <div class="w-full h-full"><img src="../img/flowerField2.jpg" class="bg-green-800 w-full h-full rounded-xl object-cover mix-blend-luminosity" /><p>X2</p></div>
        <div class="w-full h-full"><img src="../img/flowerField3.jpg" class="bg-green-800 w-full h-full rounded-xl object-cover mix-blend-luminosity" /><p>X3</p></div>
        <div class="w-full h-full"><img src="../img/flowerField4.jpg" class="bg-green-800 w-full h-full rounded-xl object-cover mix-blend-luminosity" /><p>X4</p></div>
      </div>
    </main>
    <aside class="w-2/6 h-screen bg-zinc-700 flex flex-col justify-start">
      <header class="h-1/6 flex items-center justify-center">
        <h1 class="text-4xl font-bold mb-4 text-white text-center p-4">SKLEP</h1>
      </header>
      <main class="grid grid-cols-2 grid-rows-3 gap-2 p-4 w-full h-5/6">
        <div class="bg-zinc-900 w-full h-full rounded-xl p-2">
          <img src="../img/buyBee.png" class="w-full h-2/3 rounded-lg" />
          <div class="h-1/3 w-full flex items-center justify-between">
            <p class="text-white">{{ beesCount }} bees</p>
            <button @click="buyBee" class="text-zinc-50 text-md hover:text-zinc-900 hover:bg-zinc-300 font-semibold bg-zinc-700 rounded-md px-6 py-2 select-none">
              <p class="align-text-bottom">BUY for {{ beeCost }}<span class="align-text-top">🍯</span></p>
            </button>
          </div>
        </div>
        <div class="bg-zinc-900 w-full h-full rounded-xl p-2">
          <img src="../img/buyHive.png" class="w-full h-2/3 rounded-lg" />
          <div class="h-1/3 w-full flex items-center justify-between">
            <p class="text-white">{{ hivesCount }} hives</p>
            <button @click="buyHive" class="text-zinc-50 text-md hover:text-zinc-900 hover:bg-zinc-300 font-semibold bg-zinc-700 rounded-md px-6 py-2 select-none">
              <p class="align-text-bottom">BUY for {{ hiveCost }}<span class="align-text-top">🍯</span></p>
            </button>
          </div>
        </div>
      </main>
    </aside>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import VueCookies from 'vue-cookies';

const count = ref(0);
const inputValue = ref('');
const ones = ref([]);
const beesCount = ref(0);
const hivesCount = ref(0);

const beeCost = ref(100);
const hiveCost = ref(500);

const loadCountFromCookies = () => {
  const savedCount = VueCookies.get('count');
  const savedBeesCount = VueCookies.get('beesCount');
  const savedHivesCount = VueCookies.get('hivesCount');
  if (savedCount) count.value = parseInt(savedCount, 10);
  if (savedBeesCount) beesCount.value = parseInt(savedBeesCount, 10);
  if (savedHivesCount) hivesCount.value = parseInt(savedHivesCount, 10);

  updateCosts();
};

const saveCountToCookies = () => {
  VueCookies.set('count', count.value, '1d');
  VueCookies.set('beesCount', beesCount.value, '1d');
  VueCookies.set('hivesCount', hivesCount.value, '1d');
};

const updateCosts = () => {
  beeCost.value = 100 * (beesCount.value + 1);
  hiveCost.value = 500 * (hivesCount.value + 1);
};

const click = (event) => {
  count.value += 1;
  saveCountToCookies();
  const id = Date.now();
  const { clientX: x, clientY: y } = event;

  ones.value.push({ id, x, y });

  setTimeout(() => {
    ones.value = ones.value.filter(one => one.id !== id);
  }, 1000);
};

const buyBee = () => {
  if (count.value >= beeCost.value) {
    count.value -= beeCost.value;
    beesCount.value += 1;
    updateCosts();
    saveCountToCookies();
  } else {
    alert("Nie masz wystarczająco miodu złotko!");
  }
};

const buyHive = () => {
  if (count.value >= hiveCost.value) {
    count.value -= hiveCost.value;
    hivesCount.value += 1;
    updateCosts();
    saveCountToCookies();
  } else {
    alert("Nie masz wystarczająco miodu złotko!");
  }
};

setInterval(() => {
  count.value += beesCount.value * 1 + hivesCount.value * 10;
  saveCountToCookies();
}, 1000);

onMounted(() => {
  loadCountFromCookies();
});
</script>

<style scoped>
@keyframes moveUp {
  0% {
    opacity: 1;
    transform: translateY(0);
  }
  100% {
    opacity: 0;
    transform: translateY(-50px);
  }
}

.animate-move-up {
  animation: moveUp 1s ease-in-out;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 1s;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}
</style>