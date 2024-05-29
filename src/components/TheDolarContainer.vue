<template>
  <div
    class="sm:w-3/4 md:w-2/3 lg:w-1/2 h-fit w-full m-auto pt-6 pb-12 text-white fixed bottom-0 right-0 left-0 top-0 text-center bg-neutral-900 rounded-2xl"
  >
    <h1
      id="title"
      style="line-height: normal; background-clip: text !important"
      class="gradient-animation min-[680px]:text-7xl text-5xl font-semibold text-transparent"
    >
      Dolar hoy
    </h1>

    <div class="my-10 w-fit mx-auto text-left">
      <h1
        class="sm:max-w-lg max-w-56 mb-3 lg:text-3xl sm:text-2xl min-[310px]:text-1xl min-[200px]:text-2xl text-neutral-300"
      >
        BCV:

        <span class="text-white lg:text-4xl sm:text-3xl break-all">{{
          bcvPrice
        }}</span>
        bs.
      </h1>
      <h1
        class="sm:max-w-lg max-w-56 lg:text-3xl sm:text-2xl min-[310px]:text-1xl min-[200px]:text-2xl text-neutral-300"
      >
        DolarToday:
        <span class="text-white lg:text-4xl sm:text-3xl break-all">{{
          dolarTodayPrice
        }}</span>
        bs.
      </h1>
    </div>
  </div>

  <div class="fixed bottom-5 right-0 left-0 m-auto sm:w-1/2 w-full">
    <input
      class="sm:w-10/12 w-9/12 h-12 rounded-3xl px-6 py-3 outline-none bg-neutral-900 placeholder:text-neutral-400 border-4 border-neutral-950 border-solid caret-white text-white font-medium"
      placeholder="Ingresa un número"
      type="number"
      v-model="inputValue"
    />
    <button
      @click="onClick"
      class="gradient-animation sm:w-1/6 w-1/5 h-10 absolute sm:right-0 right-2 bottom-0 top-0 m-auto rounded-3xl md:text-sm text-xs text-white font-medium"
    >
      Convertir
    </button>
  </div>
</template>

<script setup>
import { computed, ref } from "vue";
import { getMonitor } from "consulta-dolar-venezuela";
import axios from "axios";

const dollarData = ref({});
const dollarCalculations = ref({});
const inputValue = ref(1);

const show = ref(true);
const dollar = ref(null);

async function getDollarLibrary() {
  dollar.value = await getMonitor();
  assignData(dollar.value)
}

async function getDollarMyApi() {
  try {
    const { data } = await axios.get(
      "http://localhost:3000/api/dollars/dollar"
    );

    assignData(data.data);
  } catch (error) {
    assignData(dollar.value);
  }
}

function assignData(data) {

  const dolarToday =  data["dolar-today"] ?? 0 !== 0 ? data["dolar-today"] : 0

  dollarData.value.bcv = data?.bcv;
  dollarData.value.dolarToday = dolarToday;
 

  dollarCalculations.value = {
    dolarToday: dollarData.value?.dolarToday?.price,
    bcv: dollarData.value?.bcv?.price,
  };
}

function onClick() {
  dollarCalculations.value.dolarToday =
  dollarData.value.dolarToday.price * inputValue.value;
  dollarCalculations.value.bcv = dollarData.value.bcv.price * inputValue.value;

  show.value = !show.value;
}

const dolarTodayPrice = computed(() => {
  return dollarCalculations.value?.dolarToday?.toFixed(2);
});

const bcvPrice = computed(() => {
  return dollarCalculations.value?.bcv?.toFixed(2);
});

getDollarLibrary();
getDollarMyApi();
</script>

<style scoped>
.gradient-animation {
  background: linear-gradient(-45deg, #ee7752, #e73c7e, #23a6d5, #23d5ab);
  background-size: 400% 400%;
  animation: gradient 15s ease infinite;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

@keyframes gradient {
  0% {
    background-position: right;
  }
  50% {
    background-position: left;
  }
  100% {
    background-position: right;
  }
}

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
</style>
