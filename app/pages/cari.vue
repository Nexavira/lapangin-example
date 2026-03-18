<script setup>
import { Search, MapPin, Star, Zap, Filter, ChevronRight } from 'lucide-vue-next'

const venues = [
  { 
    id: 1, 
    name: 'Siliwangi Sport Center', 
    city: 'Bandung', 
    price: '150.000', 
    rating: 4.8, 
    img: 'https://images.unsplash.com/photo-1574629810360-7efbbe195018?auto=format&fit=crop&w=600', 
    categories: ['Futsal', 'Basket'], // Multi-sport
    instant: true 
  },
  { 
    id: 2, 
    name: 'Giga Arena', 
    city: 'Bandung Barat', 
    price: '50.000', 
    rating: 4.9, 
    img: 'https://images.unsplash.com/photo-1626224583764-f87db24ac4ea?auto=format&fit=crop&w=600', 
    categories: ['Badminton', 'Tenis Meja'], 
    instant: false 
  },
]
</script>

<template>
  <div class="bg-gray-50 min-h-screen pb-20">
    <div class="max-w-7xl mx-auto px-6 mt-10">
      <h2 class="text-3xl font-black tracking-tighter mb-8 uppercase italic">Lapangan <span class="text-green-600">Terdekat.</span></h2>
      <div class="grid grid-cols-1 md:grid-cols-3 gap-10">
        <NuxtLink v-for="v in venues" :key="v.id" :to="`/lapangan/${v.id}`" class="group bg-white rounded-[40px] overflow-hidden border border-gray-100 hover:shadow-2xl transition-all duration-500 transform hover:-translate-y-2">
          <div class="relative h-64 overflow-hidden">
            <img :src="v.img" class="w-full h-full object-cover group-hover:scale-110 transition duration-700" />
            <div class="absolute bottom-4 left-4 flex gap-2">
              <span v-for="cat in v.categories" :key="cat" class="px-3 py-1 bg-gray-900/80 backdrop-blur text-white text-[8px] font-black uppercase rounded-lg tracking-widest">
                {{ cat }}
              </span>
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
              <div class="space-y-1">
                <p class="text-[8px] font-black text-gray-400 uppercase tracking-widest leading-none">Mulai dari</p>
                <p class="text-lg font-black text-gray-900 uppercase tracking-tighter">Rp {{ v.price }}<span class="text-[10px] text-gray-400 font-bold">/jam</span></p>
              </div>
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