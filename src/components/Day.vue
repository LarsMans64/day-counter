<script setup lang="ts">
import type {Day} from "@/utils/types.ts";
import {computed} from "vue";

const props = defineProps<{
  day: Day
  most: number
}>()

const format: Intl.DateTimeFormatOptions = {
  day: "numeric",
  month: "short"
}

const frac = computed<string>(() => Math.max(0, props.most == 0 ? 0 : props.day.count / props.most) * 100 + "%");
</script>

<template>
  <div class="day">
    <div class="inner tile">
      <div class="count">{{ day.count }}</div>
      <div class="date">{{ new Date(day.date).toLocaleDateString(undefined, format) }}</div>
    </div>
    <div class="buttons">
      <button class="tile" @click="day.count--">-</button>
      <button class="tile" @click="day.count++">+</button>
    </div>
  </div>
</template>

<style scoped>
.day {
  --col-day: color-mix(in oklab, var(--col-min-day) calc(100% - v-bind(frac)), var(--col-max-day) v-bind(frac));
  /*--col-day: oklch(from var(--col-max-day) l calc(c * v-bind(frac)) h);*/

  background: var(--col-day);
  border-radius: 20px;
  overflow: hidden;
  user-select: none;

  padding: 5px 5px 5px 5px;
  margin-bottom: 10px;

  transition: all 200ms ease-out;

  display: flex;
  flex-direction: column;
  gap: 5px;
}

.tile {
  background: oklch(from var(--col-day) calc(l * .8) c h);
  border-top: oklch(from var(--col-day) calc(l * .7) c h) solid 4px;
  border-bottom: transparent solid 4px;
  border-radius: 15px;
  transition: all 200ms ease-out;
}

.inner {
  padding: 15px;

  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 5px;
}

.count {
  font-size: 3rem;
}

.buttons {
  opacity: 0;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 5px;

  transition: opacity 150ms ease-out;
}

.day:hover .buttons {
  opacity: 100%;
}

button {
  border: none;
  background: #24683c;
  border-radius: 15px;
  min-height: 40px;
  font-size: 1.5rem;
  cursor: pointer;

  display: flex;
  justify-content: center;
  align-items: center;
}
</style>