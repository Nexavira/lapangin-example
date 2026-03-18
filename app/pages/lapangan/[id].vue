<script setup>
import { 
  MapPin, Star, Zap, Info, ChevronLeft, ChevronRight, 
  Calendar, Crown, CheckCircle2, Wifi, Coffee, ParkingCircle, Navigation, X, Undo2, RotateCcw 
} from 'lucide-vue-next'

// --- STATE ---
const isDetailModalOpen = ref(false)
const bookingType = ref('reguler') 
const selectedSport = ref('Futsal')
const currentCourtImg = ref(0)
const selectedDate = ref(new Date().toISOString().substr(0, 10))
const router = useRouter()

// KERANJANG SESI (Multiple & Lintas Tanggal)
const selectedSlots = ref([]) 

// --- DATA DUMMY ---
const venues = {
  name: 'Giga Arena Sport Center',
  location: 'Cihanjuang, Bandung Barat',
  rating: 4.9,
  desc: 'Pusat olahraga modern dengan standar internasional. Kami menyediakan fasilitas terbaik untuk kenyamanan Anda bermain bersama tim atau komunitas di area Bandung Barat yang sejuk dan asri.',
  facilities: ['WiFi Gratis', 'Kantin', 'Parkir Luas', 'Musholla', 'Shower Room', 'Loker'],
  canRefund: false,
  canReschedule: false,
  images: [
    'https://images.unsplash.com/photo-1574629810360-7efbbe195018?auto=format&fit=crop&w=800',
    'https://images.unsplash.com/photo-1511886929837-354d827aae26?auto=format&fit=crop&w=400',
    'https://images.unsplash.com/photo-1626224583764-f87db24ac4ea?auto=format&fit=crop&w=400'
  ],
  membershipPlans: [
    { id: 1, name: 'Pro Monthly', sessions: 4, period: 'Bulan', price: '500.000' },
    { id: 2, name: 'Elite Yearly', sessions: 48, period: 'Tahun', price: '5.000.000' }
  ],
  courts: [
    { 
      id: 1, 
      name: 'Court 1', 
      desc: 'Lapangan Futsal indoor Samator 1 adalah lapangan tertutup dengan fasilitas pencahayaan optimal.',
      price: 150000, 
      gallery: [
        'https://images.unsplash.com/photo-1574629810360-7efbbe195018?auto=format&fit=crop&w=500',
        'https://images.unsplash.com/photo-1511886929837-354d827aae26?auto=format&fit=crop&w=500'
      ],
      slots: ['11:00 - 12:00', '12:00 - 13:00', '13:00 - 14:00', '14:00 - 15:00', '19:00 - 20:00'] 
    }
  ]
}

// --- LOGIC FUNCTIONS ---
const groupedSlots = computed(() => {
  return selectedSlots.value.reduce((groups, slot) => {
    const date = slot.date;
    if (!groups[date]) groups[date] = [];
    groups[date].push(slot);
    return groups;
  }, {});
});

const toggleSlot = (court, time) => {
  const slotId = `${selectedDate.value}-${court.name}-${time}`
  const index = selectedSlots.value.findIndex(s => s.id === slotId)
  if (index > -1) {
    selectedSlots.value.splice(index, 1)
  } else {
    selectedSlots.value.push({ id: slotId, date: selectedDate.value, courtName: court.name, time, price: court.price })
  }
}

const isSlotSelected = (courtName, time) => {
  return selectedSlots.value.some(s => s.id === `${selectedDate.value}-${courtName}-${time}`)
}

const totalPrice = computed(() => selectedSlots.value.reduce((acc, curr) => acc + curr.price, 0))
const removeSlot = (id) => { selectedSlots.value = selectedSlots.value.filter(s => s.id !== id) }

const quickDates = computed(() => {
  return Array.from({ length: 5 }, (_, i) => {
    const d = new Date()
    d.setDate(d.getDate() + i)
    return { full: d.toISOString().substr(0, 10), day: d.toLocaleDateString('id-ID', { weekday: 'short' }), dateNum: d.getDate() }
  })
})

const nextImg = (max) => { currentCourtImg.value = (currentCourtImg.value + 1) % max }
const prevImg = (max) => { currentCourtImg.value = (currentCourtImg.value - 1 + max) % max }

const handleCheckout = () => {
  if (selectedSlots.value.length === 0) return
  
  const payload = {
    venueName: venues.name,
    bookingType: bookingType.value,
    sportCategory: selectedSport.value, // Tambahkan ini
    slots: selectedSlots.value,
    total: totalPrice.value,
    venueLocation: venues.location,
    policies: {
      refund: venues.canRefund,
      reschedule: venues.canReschedule
    }
  }
  
  localStorage.setItem('lapangin_checkout', JSON.stringify(payload))
  router.push('/checkout')
}

</script>

<template>
  <div class="bg-gray-50 min-h-screen pb-20">
    
    <section class="bg-white px-4 md:px-6 py-6 border-b border-gray-100">
      <div class="max-w-7xl mx-auto space-y-6">
        <NuxtLink to="/cari" class="inline-flex items-center gap-2 text-[10px] font-black uppercase tracking-widest text-gray-400 hover:text-green-600 transition">
          <ChevronLeft :size="14" /> Kembali ke Katalog
        </NuxtLink>

        <div class="flex flex-col lg:flex-row gap-6">
          <div class="flex-[2] flex flex-col md:flex-row gap-4 h-[350px] md:h-[400px]">
            <div class="flex-[2] rounded-[32px] overflow-hidden border border-gray-100 shadow-sm">
              <img :src="venues.images[0]" class="w-full h-full object-cover" />
            </div>
            <div class="flex-1 hidden md:flex flex-col gap-4">
              <div class="flex-1 rounded-[32px] overflow-hidden border border-gray-100 shadow-sm"><img :src="venues.images[1]" class="w-full h-full object-cover" /></div>
              <div class="flex-1 rounded-[32px] overflow-hidden relative group border border-gray-100">
                <img :src="venues.images[2]" class="w-full h-full object-cover opacity-60" />
                <div class="absolute inset-0 flex items-center justify-center font-black text-gray-900 uppercase tracking-widest text-[10px] bg-white/20 backdrop-blur-sm">+12 Foto</div>
              </div>
            </div>
          </div>

          <div class="flex-1 bg-gray-50 rounded-[32px] p-8 border border-gray-100 flex flex-col justify-between">
            <div class="space-y-4">
              <div class="flex justify-between items-start">
                <span class="px-2 py-1 bg-green-600 text-white text-[8px] font-black uppercase rounded shadow-sm">Verified Venue</span>
                <div class="flex items-center gap-1 text-xs font-black text-gray-900"><Star class="fill-yellow-400 text-yellow-400" :size="14"/> {{ venues.rating }}</div>
              </div>
              <h2 class="text-2xl font-black text-gray-900 uppercase italic tracking-tighter leading-tight">{{ venues.name }}</h2>
              <p class="text-[11px] text-gray-500 font-medium leading-relaxed line-clamp-2 italic">"{{ venues.desc }}"</p>
              
              <div class="space-y-3 pt-2">
                <p class="text-[10px] font-black text-gray-400 uppercase tracking-widest flex items-center gap-2"><MapPin :size="12" class="text-green-600" /> {{ venues.location }}</p>
                <div class="flex flex-wrap gap-2 pt-2 border-t border-gray-200">
                  <span v-for="(f, i) in venues.facilities.slice(0, 3)" :key="i" class="px-2 py-1 bg-white border border-gray-100 rounded-lg text-[8px] font-black text-gray-400 uppercase">{{ f }}</span>
                  <span v-if="venues.facilities.length > 3" class="px-2 py-1 bg-green-50 text-green-600 rounded-lg text-[8px] font-black uppercase">+{{ venues.facilities.length - 3 }} Lainnya</span>
                </div>
                <div class="flex gap-4 pt-1">
                   <div class="flex items-center gap-1.5"><Undo2 :size="12" class="text-red-400"/><span class="text-[8px] font-black text-red-500 uppercase">No Refund</span></div>
                   <div class="flex items-center gap-1.5"><RotateCcw :size="12" class="text-red-400"/><span class="text-[8px] font-black text-red-500 uppercase">No Reschedule</span></div>
                </div>
              </div>
            </div>
            <div class="space-y-2 mt-6">
               <button @click="isDetailModalOpen = true" class="w-full py-3 bg-white border border-gray-200 text-gray-900 rounded-xl text-[9px] font-black uppercase tracking-widest hover:border-green-600 transition shadow-sm">Detail Venue</button>
               <button class="w-full py-3 bg-[#0f172a] text-white rounded-xl text-[9px] font-black uppercase tracking-widest flex items-center justify-center gap-2 hover:bg-green-600 transition shadow-sm"><Navigation :size="12" /> Petunjuk Lokasi</button>
            </div>
          </div>
        </div>
      </div>
    </section>

    <div class="max-w-7xl mx-auto px-4 md:px-6 mt-10">
      <div class="flex flex-col lg:flex-row gap-10 items-start">
        
        <div class="flex-1 space-y-12 w-full">
          <div class="flex gap-8 border-b border-gray-200">
            <button v-for="s in ['Futsal', 'Badminton', 'Basket']" :key="s" @click="selectedSport = s"
              class="pb-3 text-[11px] font-black uppercase tracking-widest relative transition-all"
              :class="selectedSport === s ? 'text-green-600 border-b-2 border-green-600' : 'text-gray-400'">{{ s }}</button>
          </div>

          <div class="space-y-6">
            <h2 class="text-xl font-black text-gray-900 uppercase italic tracking-tighter leading-none">Pilih Tipe <span class="text-green-600">Pemesanan.</span></h2>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <button @click="bookingType = 'reguler'" 
                class="p-6 rounded-[30px] border-2 transition-all text-left flex items-start gap-4 shadow-sm"
                :class="bookingType === 'reguler' ? 'border-green-600 bg-green-50' : 'border-white bg-white hover:border-gray-200'">
                <div class="p-3 bg-white rounded-2xl shadow-sm text-green-600"><Calendar :size="24"/></div>
                <div><h4 class="font-black uppercase italic text-gray-900 text-xs">Pesan Reguler</h4><p class="text-[9px] font-bold text-gray-400 uppercase mt-1 tracking-tight">Sewa per jam, bebas atur jadwal.</p></div>
              </button>
              <button @click="bookingType = 'membership'" 
                class="p-6 rounded-[30px] border-2 transition-all text-left flex items-start gap-4 shadow-sm"
                :class="bookingType === 'membership' ? 'border-green-600 bg-green-50' : 'border-white bg-white hover:border-gray-200'">
                <div class="p-3 bg-white rounded-2xl shadow-sm text-yellow-500"><Crown :size="24"/></div>
                <div><h4 class="font-black uppercase italic text-gray-900 text-xs">Membership</h4><p class="text-[9px] font-bold text-gray-400 uppercase mt-1 tracking-tight">Langganan bulanan & lebih murah.</p></div>
              </button>
            </div>
            <div v-if="bookingType === 'membership'" class="grid grid-cols-2 gap-3 animate-in fade-in zoom-in-95">
               <button v-for="plan in venues.membershipPlans" :key="plan.id" @click="selectedMembership = plan.id" class="p-4 rounded-2xl border-2 text-left transition-all" :class="selectedMembership === plan.id ? 'border-green-600 bg-green-50' : 'bg-white border-gray-100'">
                 <h5 class="text-[10px] font-black uppercase italic text-gray-900">{{ plan.name }}</h5>
                 <p class="text-[9px] font-bold text-gray-400 uppercase tracking-widest">{{ plan.sessions }} Sesi • {{ plan.period }}</p>
               </button>
            </div>
          </div>

          <div v-if="bookingType" class="space-y-6">
            <h2 class="text-xl font-black text-gray-900 uppercase italic tracking-tighter leading-none">Atur <span class="text-green-600">Jadwal.</span></h2>
            <div class="flex items-center gap-2">
               <div class="flex gap-2 no-scrollbar">
                  <button v-for="d in quickDates" :key="d.full" @click="selectedDate = d.full" class="w-[65px] flex flex-col items-center py-3 rounded-2xl border-2 transition-all" :class="selectedDate === d.full ? 'border-green-600 bg-green-600 text-white shadow-lg shadow-green-600/20' : 'bg-white border-gray-100 text-gray-400'">
                    <span class="text-[8px] font-black uppercase mb-0.5">{{ d.day }}</span><span class="text-sm font-black">{{ d.dateNum }}</span>
                  </button>
               </div>
               <div class="relative bg-white h-full px-4 py-5 rounded-2xl border border-gray-100 shadow-sm flex items-center justify-center cursor-pointer hover:border-green-600 transition">
                  <Calendar :size="18" class="text-gray-400" /><input type="date" v-model="selectedDate" class="absolute inset-0 opacity-0 cursor-pointer" />
               </div>
            </div>

            <div v-for="court in venues.courts" :key="court.id" class="bg-white rounded-[40px] p-6 shadow-sm border border-gray-100 mt-2">
              <div class="flex flex-col md:flex-row gap-6 items-start">
                <div class="w-full md:w-1/3 h-40 rounded-3xl overflow-hidden shadow-sm shrink-0 relative group">
                  <img :src="court.gallery[currentCourtImg]" class="w-full h-full object-cover transition duration-500" />
                  <div class="absolute inset-0 flex items-center justify-between px-2 opacity-0 group-hover:opacity-100 transition">
                    <button @click="prevImg(court.gallery.length)" class="bg-white/90 p-1 rounded-full shadow-lg"><ChevronLeft :size="16" /></button>
                    <button @click="nextImg(court.gallery.length)" class="bg-white/90 p-1 rounded-full shadow-lg"><ChevronRight :size="16" /></button>
                  </div>
                  <div class="absolute bottom-2 left-1/2 -translate-x-1/2 bg-black/60 px-2 py-0.5 rounded text-[8px] font-bold text-white uppercase tracking-widest">{{ currentCourtImg + 1 }} / {{ court.gallery.length }}</div>
                </div>
                <div class="flex-1 space-y-2">
                  <h3 class="text-xl font-black text-gray-900 uppercase italic tracking-tighter leading-tight">{{ court.name }} &rsaquo;</h3>
                  <p class="text-[11px] text-gray-500 font-medium leading-relaxed">{{ court.desc }}</p>
                  <div class="flex gap-4 pt-1 text-gray-400"><span class="flex items-center gap-1.5 text-[9px] font-black uppercase tracking-widest"><Zap :size="12" class="text-green-600"/> {{ selectedSport }}</span><span class="flex items-center gap-1.5 text-[9px] font-black uppercase tracking-widest"><Info :size="12" class="text-green-600"/> Indoor</span></div>
                </div>
              </div>
              <div class="grid grid-cols-2 sm:grid-cols-4 gap-3 pt-6 border-t border-gray-50 mt-6">
                <button v-for="slot in court.slots" :key="slot" @click="toggleSlot(court, slot)" class="group text-center p-4 rounded-[20px] border-2 transition-all" :class="isSlotSelected(court.name, slot) ? 'border-green-600 bg-green-50 text-green-700' : 'bg-white border-gray-100 hover:border-green-600'">
                  <p class="text-[8px] font-black text-gray-400 uppercase mb-1 tracking-widest">60 Menit</p>
                  <p class="text-xs font-black">{{ slot }}</p>
                  <p class="text-[9px] font-black uppercase mt-1 tracking-widest" :class="isSlotSelected(court.name, slot) ? 'text-green-600' : 'text-gray-400'">{{ isSlotSelected(court.name, slot) ? 'Terpilih' : 'Tersedia' }}</p>
                </button>
              </div>
            </div>
          </div>
        </div>

        <aside class="w-full lg:w-[380px] lg:sticky lg:top-24 space-y-6">
          <div class="bg-[#0f172a] rounded-[35px] p-8 text-white shadow-2xl space-y-8">
            <h3 class="text-sm font-black uppercase italic tracking-tight italic border-b border-white/10 pb-4">Ringkasan Pesanan</h3>
            <div class="space-y-6 max-h-[400px] overflow-y-auto no-scrollbar">
              <div v-for="(slots, date) in groupedSlots" :key="date" class="space-y-2 border-l-2 border-green-600/30 pl-4">
                <p class="text-[9px] font-black text-green-400 uppercase tracking-[0.2em] mb-3">{{ date }}</p>
                <div v-for="s in slots" :key="s.id" class="flex justify-between items-center bg-white/5 p-3 rounded-xl border border-white/5 group transition">
                  <div class="space-y-0.5"><p class="text-[10px] font-bold uppercase tracking-tight">{{ s.courtName }}</p><p class="text-[9px] font-medium text-white/40 italic">{{ s.time }}</p></div>
                  <button @click="removeSlot(s.id)" class="p-1.5 hover:bg-red-500/20 rounded-lg transition text-red-400"><X :size="14" /></button>
                </div>
              </div>
              <p v-if="selectedSlots.length === 0" class="text-[10px] text-white/20 italic text-center uppercase tracking-widest py-4">Belum ada sesi dipilih</p>
            </div>
            <div class="pt-6 border-t border-white/10 space-y-4">
              <div class="flex justify-between items-center text-[10px] font-bold text-white/40 uppercase tracking-widest"><span>Total ({{ selectedSlots.length }} Sesi)</span><span>Rp {{ totalPrice.toLocaleString() }}</span></div>
              <div class="flex justify-between items-center text-2xl font-black uppercase italic tracking-tighter"><span>Total</span><span class="text-green-600">RP {{ totalPrice.toLocaleString() }}</span></div>
              <button 
                @click="handleCheckout"
                :disabled="selectedSlots.length === 0" 
                class="w-full py-5 bg-green-600 rounded-2xl font-black uppercase text-xs tracking-widest shadow-xl shadow-green-600/30 active:scale-95 transition-all disabled:opacity-20 mt-4"
                >
                Lanjutkan Ke Pembayaran
              </button>
            </div>
          </div>
        </aside>
      </div> </div> <div v-if="isDetailModalOpen" class="fixed inset-0 z-[100] flex items-center justify-center p-4 bg-gray-900/60 backdrop-blur-sm">
       <div class="bg-white w-full max-w-xl rounded-[40px] shadow-2xl overflow-hidden animate-in zoom-in-95 duration-300">
          <div class="p-8 border-b border-gray-50 flex justify-between items-center"><h3 class="text-xl font-black text-gray-900 uppercase italic tracking-tighter leading-none">Info <span class="text-green-600">Venue.</span></h3><button @click="isDetailModalOpen = false" class="p-2 hover:bg-gray-100 rounded-full transition"><X :size="20"/></button></div>
          <div class="p-8 space-y-8 max-h-[75vh] overflow-y-auto no-scrollbar">
             <div class="space-y-3"><h4 class="text-[10px] font-black text-gray-400 uppercase tracking-widest">Tentang</h4><p class="text-sm text-gray-600 leading-relaxed font-medium">{{ venues.desc }}</p></div>
             <div class="grid grid-cols-2 gap-4">
                <div class="p-4 bg-red-50 rounded-2xl border border-red-100"><h5 class="text-[9px] font-black text-red-600 uppercase mb-1">Kebijakan Refund</h5><p class="text-[11px] font-bold text-red-900 uppercase italic">Tidak bisa Refund</p></div>
                <div class="p-4 bg-orange-50 rounded-2xl border border-orange-100"><h5 class="text-[9px] font-black text-orange-600 uppercase mb-1">Reschedule</h5><p class="text-[11px] font-bold text-orange-900 uppercase italic">Tidak bisa Reschedule</p></div>
             </div>
             <div class="grid grid-cols-2 gap-8 pt-4">
                <div><h4 class="text-[10px] font-black text-gray-400 uppercase tracking-widest mb-4">Fasilitas</h4><ul class="space-y-2"><li v-for="f in venues.facilities" :key="f" class="flex items-center gap-2 text-[11px] font-black text-gray-900 uppercase tracking-tight"><CheckCircle2 :size="14" class="text-green-600" /> {{ f }}</li></ul></div>
                <div><h4 class="text-[10px] font-black text-gray-400 uppercase tracking-widest mb-4">Aturan</h4><ul class="space-y-2"><li v-for="r in venues.rules" :key="r" class="flex items-center gap-2 text-[11px] font-black text-gray-900 uppercase tracking-tight"><Info :size="14" class="text-green-600" /> {{ r }}</li></ul></div>
             </div>
          </div>
       </div>
    </div>
  </div>
</template>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
</style>