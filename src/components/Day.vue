<script setup lang="ts">
import type {Day} from "@/utils/types.ts";
import {computed, ref} from "vue";

const props = defineProps<{
  day: Day
  most: number
}>()

const format: Intl.DateTimeFormatOptions = {
  day: "numeric",
  month: "short"
}

const frac = computed<number>(() => Math.max(0, props.most == 0 ? 0 : props.day.count / props.most));
const percent = computed<string>(() => frac.value * 100 + "%");

const count = computed(() => props.day.count);

const shadowAnim = ref(.5);
function animate() {
  shadowAnim.value = Math.random() * .8 + .2;

  setTimeout(animate, Math.random() * 1000 + 500);
}
animate();
</script>

<template>
  <div class="day">
    <div class="inner tile">
      <div class="count">{{ day.count }}</div>
      <div class="date">{{ new Date(day.date).toLocaleDateString(undefined, format) }}</div>
    </div>
    <div class="buttons">
      <button class="tile" @click.prevent="day.count--">-</button>
      <button class="tile" @click.prevent="day.count++">+</button>
    </div>
  </div>
</template>

<style scoped>
.day {
  --col-day:
      color-mix(in oklab, var(--col-min-day) calc(100% - v-bind(percent)), oklch(from var(--col-max-day) l c calc(h + 15 * v-bind(count))) calc(v-bind(percent) * 3));

  background: var(--col-day);
  box-shadow: 0 5px 0 oklch(0 0 0 / 0.3), 0 0 200px oklch(from var(--col-day) l c h / calc(100% * v-bind(shadowAnim)));
  border-radius: 20px;
  overflow: hidden;
  user-select: none;

  text-shadow: oklch(from var(--col-day) .9 c h / 30%) 0 0 10px;

  padding: 5px 5px 5px 5px;
  margin-bottom: 10px;

  transition: all 1s ease-in-out, background-color 200ms ease-out;

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