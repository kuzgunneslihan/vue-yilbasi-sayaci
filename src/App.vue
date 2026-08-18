<script setup lang="ts">
import { ref, watch, onMounted } from 'vue';
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
</script>

<template>
  <div class="ana-sayfa">
    
    <BilgiKarti mesaj="Hedeflerine kalan süreleri buradan takip edebilir ve yeni tarihler ekleyebilirsin." />
    <BilgiKarti mesaj="Unutma, her saniye seni hedeflerine bir adım daha yaklaştırıyor." />

    <FormAlani @yeni-tarih="yeniVeriyiListeyeEkle" />

    <hr class="cizgi" />

    <div class="liste-alani">
      <Tc 
        v-for="item in tarihListesi" 
        :key="item.id" 
        :title="item.baslik" 
        :targetDate="item.tarih" 
      />
    </div>

  </div>
</template>

<style>
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

/* Yeni koddaki grid genişliğine uyumlu max-width entegre edildi */
.cizgi {
  width: 100%;
  max-width: 1100px;
  border: none;
  border-top: 1px solid #ddd;
  margin-bottom: 1rem;
}

/* Yeni koddaki responsive Grid ve Scroll özellikleri entegre edildi */
.liste-alani {
  width: 100%;
  max-width: 1100px;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
  max-height: 620px;
  overflow-y: auto;
  overflow-x: hidden;
  padding: 4px 6px;
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