
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

const timeLeft = ref<TimeLeft>({ days: 0, hours: '00', minutes: '00', seconds: '00' });
let timerId: ReturnType<typeof setInterval>;

const padZero = (num: number) => num.toString().padStart(2, '0');

const calculateTimeLeft = () => {
  const now = new Date();
  
  const target = new Date(props.targetDate).getTime(); 
  const difference = target - now.getTime();

  if (difference > 0) {
    timeLeft.value = {
      days: Math.floor(difference / (1000 * 60 * 60 * 24)),
      hours: padZero(Math.floor((difference / (1000 * 60 * 60)) % 24)),
      minutes: padZero(Math.floor((difference / 1000 / 60) % 60)),
      seconds: padZero(Math.floor((difference / 1000) % 60))
    };
  } else {
    clearInterval(timerId); 
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
.countdown-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 2rem 0;
  font-family: system-ui, -apple-system, sans-serif;
  background-color: transparent;
  color: #000000;
}

.timer-box {
  display: flex;
  gap: 18px;
  margin-top: 1rem;
}

.time-block {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #2c2c2c;
  padding: 1rem 1.5rem;
  border-radius: 8px;
  min-width: 75px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.number {
  font-size: 2.5rem;
  font-weight: 700;
  color: #0e8903;
}

.label {  
  font-size: 0.85rem;
  margin-top: 6px;
  color: #f0f0ea;
  text-transform: uppercase;
  letter-spacing: 1px;
}
</style>