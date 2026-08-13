<script setup lang="ts">
import { ref, watch, onMounted } from 'vue';
import Tc from './components/tc.vue'; 
import FormAlani from './components/FormAlani.vue'; 

const tarihListesi = ref([
  { id: 1, baslik: '-Yılbaşına Kalan Süre-', tarih: '2026-12-31' }
]);

onMounted(() => {
  const kaydedilmisVeri = localStorage.getItem('benimTarihlerim');
  if (kaydedilmisVeri) {
    tarihListesi.value = JSON.parse(kaydedilmisVeri);
  }
});

watch(tarihListesi, (yeniListe) => {
  localStorage.setItem('benimTarihlerim', JSON.stringify(yeniListe));
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

.cizgi {
  width: 100%;
  max-width: 600px;
  border: none;
  border-top: 1px solid #ddd;
  margin-bottom: 1rem;
}

.liste-alani {
  width: 100%;
}
</style>