<script setup>
import { Search, MapPin, Star, Zap, Filter, ChevronRight } from 'lucide-vue-next'

const venues = [
  { id: 1, name: 'Siliwangi Futsal Center', city: 'Bandung', price: '150.000', rating: 4.8, img: 'https://images.unsplash.com/photo-1574629810360-7efbbe195018?auto=format&fit=crop&w=600', type: 'Futsal', instant: true },
  { id: 2, name: 'Giga Arena Badminton', city: 'Bandung Barat', price: '50.000', rating: 4.9, img: 'https://images.unsplash.com/photo-1626224583764-f87db24ac4ea?auto=format&fit=crop&w=600', type: 'Badminton', instant: false },
  { id: 3, name: 'Elite Basketball Court', city: 'Bandung', price: '200.000', rating: 4.7, img: 'https://images.unsplash.com/photo-1504450758481-7338eba7524a?auto=format&fit=crop&w=600', type: 'Basket', instant: true },
]
</script>

<template>
  <div class="bg-gray-50 min-h-screen pb-20">
    <div class="sticky top-[72px] z-40 bg-white/80 backdrop-blur-md border-b border-gray-100 py-4 px-6">
      <div class="max-w-7xl mx-auto flex items-center justify-between">
        <div class="flex items-center gap-3 overflow-x-auto no-scrollbar">
          <button class="px-5 py-2 bg-green-600 text-white rounded-full text-[10px] font-black uppercase tracking-widest shadow-lg shadow-green-600/20 whitespace-nowrap">Semua</button>
          <button class="px-5 py-2 bg-white border border-gray-200 text-gray-500 rounded-full text-[10px] font-black uppercase tracking-widest hover:border-green-600 whitespace-nowrap">⚡ Instant Booking</button>
          <button class="px-5 py-2 bg-white border border-gray-200 text-gray-500 rounded-full text-[10px] font-black uppercase tracking-widest hover:border-green-600 whitespace-nowrap">Open Sparring</button>
        </div>
        <button class="hidden md:flex items-center gap-2 px-4 py-2 bg-gray-900 text-white rounded-xl text-[10px] font-black uppercase tracking-widest">
          <Filter :size="14" /> Filter
        </button>
      </div>
    </div>

    <div class="max-w-7xl mx-auto px-6 mt-10">
      <h2 class="text-3xl font-black tracking-tighter mb-8 uppercase italic">Lapangan <span class="text-green-600">Terdekat.</span></h2>
      <div class="grid grid-cols-1 md:grid-cols-3 gap-10">
        <NuxtLink v-for="v in venues" :key="v.id" :to="`/lapangan/${v.id}`" class="group bg-white rounded-[40px] overflow-hidden border border-gray-100 hover:shadow-2xl transition-all duration-500 transform hover:-translate-y-2">
          <div class="relative h-64 overflow-hidden">
            <img :src="v.img" class="w-full h-full object-cover group-hover:scale-110 transition duration-700" />
            <div v-if="v.instant" class="absolute top-4 left-4 bg-white/90 backdrop-blur px-3 py-1 rounded-full flex items-center gap-1 shadow-sm text-[9px] font-black uppercase text-orange-500">
              <Zap :size="10" class="fill-orange-500" /> Instant
            </div>
          </div>
          <div class="p-8">
            <div class="flex justify-between items-start mb-2">
              <h3 class="text-xl font-black text-gray-900 group-hover:text-green-600 transition tracking-tight uppercase italic">{{ v.name }}</h3>
              <div class="flex items-center gap-1">
                <Star class="text-yellow-400 fill-yellow-400" :size="14" />
                <span class="text-xs font-black text-gray-900">{{ v.rating }}</span>
              </div>
            </div>
            <p class="text-[10px] font-black text-gray-400 uppercase tracking-widest mb-6 flex items-center gap-1">
              <MapPin :size="12" /> {{ v.city }}
            </p>
            <div class="flex justify-between items-center pt-4 border-t border-gray-50">
              <p class="text-lg font-black text-gray-900 uppercase tracking-tighter">Rp {{ v.price }}<span class="text-[10px] text-gray-400 font-bold tracking-normal">/jam</span></p>
              <div class="p-3 bg-gray-50 rounded-2xl group-hover:bg-green-600 group-hover:text-white transition-colors">
                <ChevronRight :size="20" />
              </div>
            </div>
          </div>
        </NuxtLink>
      </div>
    </div>
  </div>
</template>