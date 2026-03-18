<script setup>
import { Search, MapPin, Trophy, CalendarDays, ChevronDown } from 'lucide-vue-next'

const router = useRouter()

// Form state
const filter = ref({
  location: 'Bandung Barat',
  sport: 'Futsal',
  date: new Date().toISOString().substr(0, 10)
})

const goToCatalog = () => {
  router.push({
    path: '/cari',
    query: { ...filter.value }
  })
}
</script>

<template>
  <div class="bg-white min-h-screen">
    <section class="relative pt-24 pb-44 overflow-hidden">
      
      <div class="absolute right-0 top-0 w-full h-full lg:w-1/2 pointer-events-none z-0 overflow-hidden hidden lg:block">
        <img 
          src="https://cdni.iconscout.com/illustration/premium/thumb/football-player-kicking-ball-illustration-download-in-svg-png-gif-file-formats--soccer-sports-man-pack-illustrations-5211516.png" 
          class="w-full h-full object-contain opacity-[0.1] transform scale-150 translate-x-20 translate-y-10 rotate-[-5deg]" 
          alt="Illustration"
        />
      </div>

      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 relative z-10">
        <div class="text-center mb-16">
          <h1 class="text-6xl md:text-8xl font-black text-[#0f172a] tracking-tighter mb-6 leading-[0.85] uppercase">
            MAINKAN <span class="text-green-600 italic">GAMES-MU</span> <br>
            DI LAPANGAN TERBAIK.
          </h1>
          <p class="text-gray-500 text-lg md:text-xl max-w-2xl mx-auto font-medium leading-relaxed">
            Booking lapangan olahraga favoritmu hanya dalam hitungan detik. 
            Tanpa telepon, tanpa ribet, langsung tanding!
          </p>
        </div>

        <div class="max-w-5xl mx-auto bg-white rounded-[40px] shadow-[0_32px_64px_-12px_rgba(0,0,0,0.12)] border border-gray-100 p-3">
          <div class="grid grid-cols-1 md:grid-cols-4 gap-2">
            
            <div class="relative group p-5 hover:bg-gray-50 rounded-[30px] transition cursor-pointer">
              <label class="flex items-center gap-2 text-[10px] font-black uppercase tracking-[0.2em] text-gray-400 group-hover:text-green-600 mb-1">
                <MapPin :size="14" /> Lokasi
              </label>
              <div class="relative flex items-center">
                <select v-model="filter.location" class="w-full bg-transparent font-bold text-gray-900 focus:outline-none appearance-none cursor-pointer pr-8 z-10">
                  <option value="Bandung Barat">Bandung Barat</option>
                  <option value="Kota Bandung">Kota Bandung</option>
                </select>
                <ChevronDown :size="16" class="absolute right-0 text-gray-300 pointer-events-none" />
              </div>
            </div>

            <div class="relative group p-5 hover:bg-gray-50 rounded-[30px] transition cursor-pointer border-t md:border-t-0 md:border-l border-gray-100">
              <label class="flex items-center gap-2 text-[10px] font-black uppercase tracking-[0.2em] text-gray-400 group-hover:text-green-600 mb-1">
                <Trophy :size="14" /> Olahraga
              </label>
              <div class="relative flex items-center">
                <select v-model="filter.sport" class="w-full bg-transparent font-bold text-gray-900 focus:outline-none appearance-none cursor-pointer pr-8 z-10">
                  <option value="Futsal">Futsal</option>
                  <option value="Badminton">Badminton</option>
                  <option value="Basket">Basket</option>
                </select>
                <ChevronDown :size="16" class="absolute right-0 text-gray-300 pointer-events-none" />
              </div>
            </div>

            <div class="relative group p-5 hover:bg-gray-50 rounded-[30px] transition cursor-pointer border-t md:border-t-0 md:border-l border-gray-100">
              <label class="flex items-center gap-2 text-[10px] font-black uppercase tracking-[0.2em] text-gray-400 group-hover:text-green-600 mb-1">
                <CalendarDays :size="14" /> Tanggal
              </label>
              <input type="date" v-model="filter.date" class="w-full bg-transparent font-bold text-gray-900 focus:outline-none cursor-pointer" />
            </div>

            <div class="flex items-center p-1">
              <button @click="goToCatalog" class="w-full h-[70px] bg-[#0f172a] text-white rounded-[25px] font-black uppercase tracking-widest hover:bg-green-600 transition-all flex items-center justify-center gap-3 group shadow-xl shadow-gray-900/10">
                <Search :size="20" class="group-hover:scale-110 transition" />
                Cari Lapangan
              </button>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>