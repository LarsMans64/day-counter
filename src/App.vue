<script setup lang="ts">
import {useLocalStorage} from "@vueuse/core";
import {computed} from "vue";
import {type CounterData} from "@/utils/types.ts"
import Day from "@/components/Day.vue";

const data = useLocalStorage<CounterData>("data", { days: [] });

const days = data.value.days;

while (!days.some(day => isToday(day.date))) {
  const latest = days[0];
  console.log(latest)
  days.unshift({
    date: latest ? getTomorrow(latest.date).valueOf() : new Date().valueOf(),
    count: 0,
  })
}

const most = computed(() => days.map(value => value.count).reduce((left, right) => Math.max(left, right)));

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
    <div class="days">
      <Day v-for="day in data.days" :day="day" :most="most" class="day"/>
    </div>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  min-width: 100vw;
  padding: 10vh 10vw;
}

.days {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;

  max-width: 1000px;
  margin-inline: auto;
}

.day {
  flex-shrink: 0;
  min-width: 150px;
}
</style>