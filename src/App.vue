<script setup lang="ts">
import {useLocalStorage} from "@vueuse/core";
import {computed} from "vue";
import {type CounterData} from "@/utils/types.ts"
import Day from "@/components/Day.vue";

const data = useLocalStorage<CounterData>("data", { days: [] });

const days = data.value.days;

const most = computed(() => days.map(value => value.count).reduce((left, right) => Math.max(left, right)));

function reload() {
  while (!days.some(day => isToday(day.date))) {
    const latest = days[0];
    console.log(latest)
    days.unshift({
      date: latest ? getTomorrow(latest.date).valueOf() : new Date().valueOf(),
      count: 0,
    })
  }
}

function getTomorrow(date: number) {
  const d = new Date(date);
  d.setDate(d.getDate() + 1);
  return d
}

function isToday(date: number) {
  const today = new Date();
  const day = new Date(date);

  return day.getDate() == today.getDate() && day.getMonth() == today.getMonth() && day.getFullYear() == today.getFullYear()
}
</script>

<template>
  <div class="page">
    <main>
      <div class="buttons">
        <button @click="reload" class="reload">
          Reload
        </button>
      </div>
      <div class="days">
        <Day v-for="day in data.days" :day="day" :most="most" class="day"/>
      </div>
    </main>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  min-width: 100vw;
  padding: 10vh 10vw;
}

main {
  max-width: 1000px;
  margin-inline: auto;
}

.buttons {
  display: flex;
  gap: 10px;

  margin-bottom: 50px;

  button {
    background-color: var(--col-button);
    border: 5px solid var(--col-button-hover);
    border-radius: 20px;
    cursor: pointer;
    padding: 15px 20px;
    font-size: 1.1rem;
    font-family: inherit;

    transition: all 100ms ease-out;

    &:hover {
      background-color: var(--col-button-hover);
    }
  }
}

.days {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.day {
  flex-shrink: 0;
  min-width: 150px;
}
</style>