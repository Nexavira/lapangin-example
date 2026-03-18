<script setup>
import { CheckCircle2, Navigation } from 'lucide-vue-next'
</script>

<template>
  <div class="bg-gray-50 min-h-screen flex items-center justify-center p-6">
    <div class="max-w-sm w-full text-center space-y-10 animate-in fade-in zoom-in-95 duration-700">
      <div class="w-32 h-32 bg-green-100 rounded-full flex items-center justify-center mx-auto border-8 border-white shadow-xl shadow-green-600/10">
        <CheckCircle2 :size="64" class="text-green-600" />
      </div>
      
      <div class="space-y-4">
        <h1 class="text-4xl font-black text-gray-900 uppercase italic tracking-tighter">Yay! Berhasil.</h1>
        <p class="text-xs text-gray-400 font-bold uppercase leading-relaxed">Pembayaran terverifikasi. E-Tiket telah dikirimkan ke WhatsApp Anda. Selamat bertanding!</p>
      </div>

      <div class="space-y-3">
        <NuxtLink to="/" class="block w-full py-5 bg-green-600 text-white rounded-2xl font-black uppercase text-xs tracking-widest shadow-xl shadow-green-600/20">Kembali ke Home</NuxtLink>
        <button class="block w-full py-5 bg-white border border-gray-200 text-gray-900 rounded-2xl font-black uppercase text-[10px] tracking-widest flex items-center justify-center gap-2 shadow-sm">
          <Navigation :size="14" /> Lihat Lokasi
        </button>
      </div>
    </div>
  </div>
</template>