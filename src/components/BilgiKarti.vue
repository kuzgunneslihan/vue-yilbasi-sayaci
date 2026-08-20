<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';

const props = withDefaults(
  defineProps<{
    mesaj?: string;
    mesajlar?: string[];
  }>(),
  {
    mesajlar: () => [
      'Hedeflerine kalan süreleri buradan takip edebilir ve yeni tarihler ekleyebilirsin.',
      'Unutma, her saniye seni hedeflerine bir adım daha yaklaştırıyor.',
    ],
  }
);

const acik = ref(false);
const aktifIndex = ref(0);

// Dışarı tıklama kontrolü için ref'ler
const popupRef = ref<HTMLElement | null>(null);
const butonRef = ref<HTMLElement | null>(null);

function ac() {
  acik.value = !acik.value;
}

function kapat() {
  acik.value = false;
}

function goster(i: number) {
  const liste = props.mesaj ? [props.mesaj] : props.mesajlar;
  const uzunluk = liste.length;
  aktifIndex.value = ((i % uzunluk) + uzunluk) % uzunluk;
}

// Yana / dışarıya tıklanınca kapanma mantığı
function disariTiklandi(e: MouseEvent) {
  const hedef = e.target as Node;
  if (
    acik.value &&
    popupRef.value &&
    !popupRef.value.contains(hedef) &&
    butonRef.value &&
    !butonRef.value.contains(hedef)
  ) {
    kapat();
  }
}

// ESC tuşuna basınca kapanma mantığı
function escIleKapat(e: KeyboardEvent) {
  if (e.key === 'Escape') kapat();
}

onMounted(() => {
  document.addEventListener('click', disariTiklandi);
  document.addEventListener('keydown', escIleKapat);
});

onUnmounted(() => {
  document.removeEventListener('click', disariTiklandi);
  document.removeEventListener('keydown', escIleKapat);
});
</script>

<template>
  <button ref="butonRef" class="bilgi-fab" aria-label="Bilgi" @click="ac">i</button>

  <div class="bilgi-overlay" :class="{ acik: acik }">
   
    <div ref="popupRef" class="bilgi-popup">
      <button class="kapat-btn" aria-label="Kapat" @click="kapat">✕</button>

      <div class="bilgi-karti">
        <p>{{ mesaj ? mesaj : mesajlar[aktifIndex] }}</p>
      </div>

      <div v-if="!mesaj && mesajlar.length > 1" class="bilgi-nav">
        <button class="ok-btn" @click="goster(aktifIndex - 1)">‹</button>
        <div class="noktalar">
          <span
            v-for="(_, i) in mesajlar"
            :key="i"
            class="nokta"
            :class="{ aktif: i === aktifIndex }"
            @click="goster(i)"
          ></span>
        </div>
        <button class="ok-btn" @click="goster(aktifIndex + 1)">›</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.bilgi-karti {
  background-color: #ffffff;
  padding: 1rem;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.05);
  width: 100%;
  text-align: center;
  color: #333;
  box-sizing: border-box;
}

/* Yuvarlak yüzen buton (FAB) */
.bilgi-fab {
  position: fixed;
  right: 24px;
  bottom: 24px;
  width: 48px;
  height: 48px;
  border-radius: 50%;
  background-color: #0e8903;
  color: #ffffff;
  border: none;
  font-size: 20px;
  font-weight: bold;
  font-family: serif;
  cursor: pointer;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  z-index: 99;
  transition: transform 0.15s ease, background-color 0.15s ease;
}

.bilgi-fab:hover {
  background-color: #0b6d02;
  transform: scale(1.08);
}

/* Yana/boşluğa tıklanabilen arkaplan kaplaması */
.bilgi-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 100;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.2s ease;
}

.bilgi-overlay.acik {
  opacity: 1;
  pointer-events: auto;
}


.bilgi-popup {
  position: relative;
  background: #ffffff;
  border-radius: 12px;
  box-shadow: 0 12px 32px rgba(0, 0, 0, 0.2);
  width: min(500px, 90vw);
  padding: 2.2rem 1.5rem 1.2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  transform: scale(0.92);
  transition: transform 0.2s ease;
}

.bilgi-overlay.acik .bilgi-popup {
  transform: scale(1);
}

.kapat-btn {
  position: absolute;
  top: 10px;
  right: 12px;
  background: transparent;
  border: none;
  font-size: 15px;
  color: #888;
  cursor: pointer;
  width: 28px;
  height: 28px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.kapat-btn:hover {
  background: #f0f0f0;
  color: #333;
}

/* Alt navigasyon */
.bilgi-nav {
  display: flex;
  align-items: center;
  gap: 12px;
}

.ok-btn {
  background: #ffffff;
  border: 1px solid #ddd;
  width: 28px;
  height: 28px;
  border-radius: 50%;
  cursor: pointer;
  font-size: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.ok-btn:hover {
  border-color: #0e8903;
  color: #0e8903;
}

.noktalar {
  display: flex;
  gap: 6px;
}

.nokta {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: #ccc;
  cursor: pointer;
}

.nokta.aktif {
  background: #0e8903;
  transform: scale(1.2);
}

@media (max-width: 480px) {
  .bilgi-fab {
    right: 16px;
    bottom: 16px;
  }
}
</style>