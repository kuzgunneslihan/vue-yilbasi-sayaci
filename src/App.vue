<script setup lang="ts">
import { ref, watch, onMounted } from 'vue';
import Tc from './components/tc.vue'; 

const tarihListesi = ref([
  { id: 1, baslik: '-Yılbaşına Kalan Süre-', tarih: '2026-12-31' }
]);

const girilenBaslik = ref('');
const girilenTarih = ref('');

onMounted(() => {
  const kaydedilmisVeri = localStorage.getItem('benimTarihlerim');
  if (kaydedilmisVeri) {
    tarihListesi.value = JSON.parse(kaydedilmisVeri);
  }
});

watch(tarihListesi, (yeniListe) => {
  localStorage.setItem('benimTarihlerim', JSON.stringify(yeniListe));
}, { deep: true });

const listeyeEkle = () => {
  if (girilenTarih.value === '') {
    alert("Lütfen bir tarih seçin!");
    return;
  }

  let baslikYazisi = girilenBaslik.value;
  if (baslikYazisi === '') {
    baslikYazisi = '- Yeni Tarihe Kalan -';
  }

  tarihListesi.value.push({
    id: Date.now(),
    baslik: baslikYazisi,
    tarih: girilenTarih.value 
  });

  girilenBaslik.value = '';
  girilenTarih.value = '';
};
</script>
<template>
  <div class="ana-sayfa">
    
    <div class="form-alani">
      <input 
        type="text" 
        v-model="girilenBaslik" 
        class="kutu text-kutu"
      />
      <input 
        type="date" 
        v-model="girilenTarih" 
        class="kutu tarih-kutu"
      />
      <button @click="listeyeEkle" class="ekle-butonu">Yeni Ekle</button>
    </div>

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

.form-alani {
  display: flex;
  gap: 10px;
  background: white;
  padding: 1rem;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  margin-bottom: 1rem;
}

.kutu {
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1rem;
}

.ekle-butonu {
  padding: 0.5rem 1rem;
  background-color: #0e8903;
  color: white;
  border: none;
  border-radius: 4px;
  font-weight: bold;
  cursor: pointer;
}

.ekle-butonu:hover {
  background-color: #0b6d02;
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