<script setup lang="ts">
import { ref } from 'vue';

// App.vue'ya veri göndermek için emit tanımlıyoruz
const emit = defineEmits<{
  (e: 'yeni-tarih', veri: { baslik: string; tarih: string }): void
}>();

const girilenBaslik = ref('');
const girilenTarih = ref('');

const listeyeEkle = () => {
  if (girilenTarih.value === '') {
    alert("Lütfen bir tarih seçin!");
    return;
  }

  let baslikYazisi = girilenBaslik.value;
  if (baslikYazisi === '') {
    baslikYazisi = '- Yeni Tarihe Kalan -';
  }

  // Veriyi App.vue'ya fırlatıyoruz
  emit('yeni-tarih', {
    baslik: baslikYazisi,
    tarih: girilenTarih.value
  });

  // İçeriyi temizliyoruz
  girilenBaslik.value = '';
  girilenTarih.value = '';
};
</script>

<template>
  <div class="form-alani">
    <input 
      type="text" 
      v-model="girilenBaslik" 
      placeholder="Başlık Girin"
      class="kutu text-kutu"
    />
    <input 
      type="date" 
      v-model="girilenTarih" 
      class="kutu tarih-kutu"
    />
    <button @click="listeyeEkle" class="ekle-butonu">Yeni Ekle</button>
  </div>
</template>

<style scoped>
/* Sadece bu component'i ilgilendiren stiller */
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
</style>