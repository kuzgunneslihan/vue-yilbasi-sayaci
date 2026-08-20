<script setup lang="ts">
import { ref, watch, onMounted ,computed } from 'vue';
import Tc from './components/tc.vue'; 
import FormAlani from './components/FormAlani.vue'; 
import BilgiKarti from './components/BilgiKarti.vue';

const tarihListesi = ref([
  { id: 1, baslik: '-Yılbaşına Kalan Süre-', tarih: '2026-12-31' },
  { id: 2, baslik: '-Benim Doğum Günüme Kalan-', tarih: '2027-07-19' },
  { id: 3, baslik: "-Taha'nın Doğum Günü-", tarih: '2026-12-07' },
  { id: 4, baslik: '-Seneye Bugüne Kalan-', tarih: '2027-08-13' },
  { id: 5, baslik: '-Okulların Açılması-', tarih: '2026-09-22' },
  { id: 6, baslik: '-Zafer Bayramı-', tarih: '2026-08-30' },
  { id: 7, baslik: '-Hackathon Başlangıcı-', tarih: '2026-09-15' },
  { id: 8, baslik: '-Cumhuriyet Bayramı-', tarih: '2026-10-29' },
  { id: 9, baslik: '-Cadılar Bayramı-', tarih: '2026-10-31' },
  { id: 10, baslik: '-Sene Sonu Proje Teslimi-', tarih: '2027-01-15' },
  { id: 11, baslik: '-Sevgililer Günü-', tarih: '2027-02-14' },
  { id: 12, baslik: '-23 Nisan Ulusal Egemenlik-', tarih: '2027-04-23' },
  { id: 13, baslik: '-1 Mayıs İşçi Bayramı-', tarih: '2027-05-01' },
  { id: 14, baslik: '-19 Mayıs Gençlik ve Spor Bayramı-', tarih: '2027-05-19' },
  { id: 15, baslik: '-Yaz Tatilinin Başlaması-', tarih: '2027-06-15' }
]);

onMounted(() => {
  const kaydedilmisVeri = localStorage.getItem('benimTarihlerim2');
  if (kaydedilmisVeri) {
    tarihListesi.value = JSON.parse(kaydedilmisVeri);
  }
});

watch(tarihListesi, (yeniListe) => {
  localStorage.setItem('benimTarihlerim2', JSON.stringify(yeniListe));
}, { deep: true });

const yeniVeriyiListeyeEkle = (gelenVeri: { baslik: string; tarih: string }) => {
  tarihListesi.value.push({
    id: Date.now(),
    baslik: gelenVeri.baslik,
    tarih: gelenVeri.tarih 
  });
};
const aramaMetni = ref('');
const siralamaTuru = ref<'yakin' | 'uzak' | 'a-z'>('yakin');

function kartSil(id: number | string) {
  tarihListesi.value = tarihListesi.value.filter(item => item.id !== id);
}

const filtrelenmisListesi = computed(() => {
  let sonuc = tarihListesi.value.filter(item =>
    item.baslik.toLowerCase().includes(aramaMetni.value.toLowerCase())
  );

  return sonuc.sort((a, b) => {
    if (siralamaTuru.value === 'yakin') return new Date(a.tarih).getTime() - new Date(b.tarih).getTime();
    if (siralamaTuru.value === 'uzak') return new Date(b.tarih).getTime() - new Date(a.tarih).getTime();
    if (siralamaTuru.value === 'a-z') return a.baslik.localeCompare(b.baslik);
    return 0;
  });
});
</script>

<template>
  <div class="filtre-bar">
      <input 
        v-model="aramaMetni" 
        type="text" 
        placeholder="Sayaçlarda ara..." 
        class="arama-input"
      />
      <select v-model="siralamaTuru" class="siralama-select">
        <option value="yakin">En Yakın Tarih</option>
        <option value="uzak">En Uzak Tarih</option>
        <option value="a-z">İsme Göre (A-Z)</option>
      </select>
    </div>
  <div class="ana-sayfa">
    
    <!-- İki ayrı bileşen yerine tek bileşene dizi geçiyoruz -->
    <BilgiKarti :mesajlar="[
      'Hedeflerine kalan süreleri buradan takip edebilir ve yeni tarihler ekleyebilirsin.',
      'Unutma, her saniye seni hedeflerine bir adım daha yaklaştırıyor.'
    ]" />

    <FormAlani @yeni-tarih="yeniVeriyiListeyeEkle" />

    <hr class="cizgi" />

    <div class="liste-alani">
      <Tc 
        v-for="item in filtrelenmisListesi" 
        :key="item.id" 
        :id="item.id"
        :title="item.baslik" 
        :targetDate="item.tarih" 
        @sil="kartSil(item.id)"
      />
    </div>

  </div>
</template>

<style>
.filtre-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 16px;
  width: 100%;
  max-width: 800;
  padding: 12px 20px;
  background: #ffffff;
  border-radius: 12px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
  box-sizing: border-box;
}

.arama-input {
  flex: 1;
  max-width: 320px;
  padding: 10px 14px;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  font-size: 14px;
  outline: none;
  transition: border-color 0.2s ease;
}

.siralama-select {
  padding: 10px 14px;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  font-size: 14px;
  background-color: #ffffff;
  color: #333;
  cursor: pointer;
  outline: none;
}


body {
  margin: 0;
  padding: 0;
  background-color: #fdf8f8;
  font-family: system-ui, -apple-system, sans-serif;
}

.ana-sayfa {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2rem 1rem;
}

.cizgi {
  width: 100%;
  max-width: 1100px;
  border: none;
  border-top: 1px solid #ddd;
  margin-bottom: 1rem;
}

.liste-alani {
  width: 100%;
  max-width: 1400px;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 40px;
  max-height: 670px;
  overflow-y: auto;
  overflow-x: hidden;
  padding: 5px 15px;
  box-sizing: border-box;
}

.liste-alani::-webkit-scrollbar {
  width: 8px;
}
.liste-alani::-webkit-scrollbar-thumb {
  background: #ddd;
  border-radius: 8px;
}

@media (max-width: 1023px) {
  .liste-alani {
    grid-template-columns: repeat(2, 1fr);
    max-height: 560px;
  }
}

@media (max-width: 639px) {
  .liste-alani {
    grid-template-columns: 1fr;
    max-height: 70vh;
  }
}
</style>