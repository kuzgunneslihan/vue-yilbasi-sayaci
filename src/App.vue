<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';

interface TimeLeft {
  days: number;
  hours: string | number;
  minutes: string | number;
  seconds: string | number;
}

// Başlangıç değerleri
const timeLeft = ref<TimeLeft>({ days: 0, hours: '00', minutes: '00', seconds: '00' });
let timerId: ReturnType<typeof setInterval>;

// Tek haneli sayılara başa sıfır ekleme 
const padZero = (num: number) => num.toString().padStart(2, '0');

const calculateTimeLeft = () => {
  const now = new Date();
  
  // Bulunduğumuz yılın sonunu dinamik olarak alıyor.
  const targetDate = new Date(now.getFullYear(), 11, 31, 23, 59, 59).getTime(); 
  const difference = targetDate - now.getTime();

  if (difference > 0) {
    timeLeft.value = {
      days: Math.floor(difference / (1000 * 60 * 60 * 24)),
      hours: padZero(Math.floor((difference / (1000 * 60 * 60)) % 24)),
      minutes: padZero(Math.floor((difference / 1000 / 60) % 60)),
      seconds: padZero(Math.floor((difference / 1000) % 60))
    };
  } else {
    clearInterval(timerId); // Süre bittiyse boşa çalışmasın
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
    <h1>-Yılbaşına Kalan Süre-</h1>
    
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
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  font-family: system-ui, -apple-system, sans-serif;
  background-color: #fdf8f8;
  color: #000000;
}

.timer-box {
  display: flex;
  gap: 18px;
  margin-top: 2rem;
}

.time-block {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #2c2c2c;
  padding: 1rem 1.5rem;

}

.number {
  font-size: 2.5rem;
  font-weight: 700;
  color: #0e8903; 
}



</style>

