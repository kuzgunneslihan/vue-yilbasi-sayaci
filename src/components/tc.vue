<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';

const props = defineProps<{
  title: string;
  targetDate: string;
}>();

interface TimeLeft {
  days: number;
  hours: string | number;
  minutes: string | number;
  seconds: string | number;
}
defineEmits(['sil']);
const timeLeft = ref<TimeLeft>({ days: 0, hours: '00', minutes: '00', seconds: '00' });
let timerId: ReturnType<typeof setInterval>;

const padZero = (num: number) => num.toString().padStart(2, '0');

const calculateTimeLeft = () => {
  const now = new Date();
  
  const parseableDate = props.targetDate.includes('T') ? props.targetDate : `${props.targetDate}T00:00:00`;
  const target = new Date(parseableDate).getTime(); 
  const difference = target - now.getTime();

  if (difference > 0) {
    timeLeft.value = {
      days: Math.floor(difference / (1000 * 60 * 60 * 24)),
      hours: padZero(Math.floor((difference / (1000 * 60 * 60)) % 24)),
      minutes: padZero(Math.floor((difference / 1000 / 60) % 60)),
      seconds: padZero(Math.floor((difference / 1000) % 60))
    };
  } else {
    if (timerId) clearInterval(timerId); 
    timeLeft.value = { days: 0, hours: '00', minutes: '00', seconds: '00' };
  }
};

onMounted(() => {
  calculateTimeLeft(); 
  timerId = setInterval(calculateTimeLeft, 1000); 
});

onUnmounted(() => {
  if (timerId) clearInterval(timerId);
});
</script>

<template>
  <div class="countdown-wrapper">
    <h1>{{ title }}</h1>
    <button class="sil-btn" type="button" title="Sil" @click="$emit('sil')">✕</button>
    <div class="timer-box">
      <div class="time-block">
        <span class="number">{{ timeLeft.days }}</span>
        <span class="label">Gün</span>
      </div>
      <div class="time-block">
        <span class="number">{{ timeLeft.hours }}</span>
        <span class="label">Saat</span>
      </div>
      <div class="time-block">
        <span class="number">{{ timeLeft.minutes }}</span>
        <span class="label">Dak</span>
      </div>
      <div class="time-block">
        <span class="number">{{ timeLeft.seconds }}</span>
        <span class="label">San</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
-btn {
  position: absolute;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
  background: transparent;
  border: none;
  color: #e53e3e;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
  width: 28px;
  height: 28px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.sil-btn:hover {
  background-color: #fed7d7;
}
.countdown-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 1.25rem 0.5rem;
  font-family: system-ui, -apple-system, sans-serif;
  background-color: transparent;
  color: #000000;
  width: 100%;
  box-sizing: border-box;
}

h1 {
  font-size: clamp(1.1rem, 2vw, 1.5rem);
  margin: 0 0 0.25rem;
  text-align: center;
}

.timer-box {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: clamp(6px, 1.4vw, 14px);
  margin-top: 0.75rem;
  width: 100%;
}

.time-block {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #2c2c2c;
  padding: clamp(0.5rem, 1.4vw, 0.85rem) clamp(0.5rem, 1.8vw, 0.9rem);
  border-radius: 8px;
  min-width: 54px;
  flex: 1 1 54px;
  max-width: 84px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  box-sizing: border-box;
}

.number {
  font-size: clamp(1.25rem, 2.6vw, 2.5rem);
  font-weight: 700;
  color: #0e8903;
  line-height: 1.1;
}

.label {  
  font-size: clamp(0.62rem, 1vw, 0.85rem);
  margin-top: 4px;
  color: #f0f0ea;
  text-transform: uppercase;
  letter-spacing: 1px;
}

@media (max-width: 639px) {
  .time-block {
    max-width: 100px;
  }
}
</style>