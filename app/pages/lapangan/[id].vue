<script setup>
import { 
  MapPin, Star, Zap, Info, ChevronLeft, ChevronRight, ChevronDown, ChevronUp,
  Calendar, Crown, CheckCircle2, Wifi, Coffee, ParkingCircle, Navigation, X, Undo2, RotateCcw, Lock, Clock
} from 'lucide-vue-next'

// --- STATE UMUM ---
const isDetailModalOpen = ref(false)
const bookingType = ref('reguler') // 'reguler' | 'member'
const selectedSport = ref('Futsal')
const selectedDate = ref(new Date().toISOString().substr(0, 10))
const activeCourtId = ref(1) 
const router = useRouter()

// --- STATE REGULER ---
const selectedSlots = ref([]) 

// --- STATE MEMBER (NEW) ---
const selectedMemberPackage = ref(null) 
const selectedMemberDays = ref([]) 
const tempMemberSlots = ref([]) // Menyimpan pilihan multiple jam sebelum masuk keranjang
const memberCart = ref([]) 
const daysOfWeek = ['Senin', 'Selasa', 'Rabu', 'Kamis', 'Jumat', 'Sabtu', 'Minggu']

// --- LOGIC LOCKS ---
const isMemberDisabled = computed(() => selectedSlots.value.length > 0)
const isRegulerDisabled = computed(() => memberCart.value.length > 0)

// --- DATA DUMMY ---
const venues = {
  name: 'Giga Arena Sport Center',
  location: 'Cihanjuang, Bandung Barat',
  rating: 4.9,
  desc: 'Pusat olahraga modern dengan standar internasional. Kami menyediakan fasilitas terbaik untuk kenyamanan Anda bermain bersama tim atau komunitas.',
  facilities: ['WiFi Gratis', 'Kantin', 'Parkir Luas', 'Musholla', 'Shower Room'],
  canRefund: false,
  canReschedule: false,
  images: [
    'https://images.unsplash.com/photo-1574629810360-7efbbe195018?auto=format&fit=crop&w=800',
    'https://images.unsplash.com/photo-1511886929837-354d827aae26?auto=format&fit=crop&w=400',
    'https://images.unsplash.com/photo-1626224583764-f87db24ac4ea?auto=format&fit=crop&w=400'
  ],
  memberPlans: [
    { id: 'm1', name: 'Member Bulanan', type: 'Bulanan', sessions: 4, desc: 'Main rutin 1x seminggu (Total 4 Sesi)' },
    { id: 'm2', name: 'Member Tahunan', type: 'Tahunan', sessions: 48, desc: 'Main rutin 1x seminggu (Total 48 Sesi)' }
  ],
  courts: [
    { 
      id: 1, 
      name: 'COURT 1', 
      desc: 'Lapangan indoor berkualitas dengan fasilitas pencahayaan optimal.',
      price: 150000, 
      memberPrice: 500000, // Harga per paket/sesi member
      gallery: ['https://images.unsplash.com/photo-1574629810360-7efbbe195018?auto=format&fit=crop&w=500'],
      slots: ['11:00 - 12:00', '12:00 - 13:00', '13:00 - 14:00', '19:00 - 20:00'] 
    },
    { 
      id: 2, 
      name: 'COURT 2', 
      desc: 'Lapangan premium sisi timur dengan sirkulasi udara alami.',
      price: 135000, 
      memberPrice: 450000,
      gallery: ['https://images.unsplash.com/photo-1574629810360-7efbbe195018?auto=format&fit=crop&w=500'],
      slots: ['10:00 - 11:00', '14:00 - 15:00', '16:00 - 17:00'] 
    }
  ]
}

// --- LOGIC FUNCTIONS MEMBER ---
const toggleMemberDay = (day) => {
  const idx = selectedMemberDays.value.indexOf(day)
  if (idx > -1) selectedMemberDays.value.splice(idx, 1)
  else selectedMemberDays.value.push(day)
}

const toggleTempMemberSlot = (time) => {
  const idx = tempMemberSlots.value.indexOf(time)
  if (idx > -1) tempMemberSlots.value.splice(idx, 1)
  else tempMemberSlots.value.push(time)
}

const addMemberToCart = (court) => {
  if (!selectedMemberPackage.value || selectedMemberDays.value.length === 0 || tempMemberSlots.value.length === 0) {
    alert('Silakan pilih Paket Member, Hari, dan minimal 1 Sesi Jam terlebih dahulu.')
    return
  }
  const pkg = venues.memberPlans.find(p => p.id === selectedMemberPackage.value)
  memberCart.value.push({
    id: `MEM-${Date.now()}`,
    packageName: pkg.name,
    sport: selectedSport.value,
    courtName: court.name,
    days: [...selectedMemberDays.value],
    times: [...tempMemberSlots.value], // Bisa banyak sesi sekaligus
    price: court.memberPrice * tempMemberSlots.value.length // Total harga berdasarkan jumlah sesi
  })
  // Reset Pilihan
  selectedMemberPackage.value = null
  selectedMemberDays.value = []
  tempMemberSlots.value = []
}

const removeMemberItem = (id) => {
  memberCart.value = memberCart.value.filter(m => m.id !== id)
}

// Mengelompokkan Member by Court
const groupedMemberCart = computed(() => {
  return memberCart.value.reduce((groups, item) => {
    if (!groups[item.courtName]) groups[item.courtName] = [];
    groups[item.courtName].push(item);
    return groups;
  }, {});
});

// --- LOGIC FUNCTIONS REGULER ---
// Mengelompokkan Reguler by Date -> Court
const groupedRegulerSlots = computed(() => {
  return selectedSlots.value.reduce((groups, slot) => {
    const date = slot.date;
    if (!groups[date]) groups[date] = {};
    if (!groups[date][slot.courtName]) groups[date][slot.courtName] = [];
    groups[date][slot.courtName].push(slot);
    return groups;
  }, {});
});

const toggleSlot = (court, time) => {
  const slotId = `${selectedDate.value}-${selectedSport.value}-${court.name}-${time}`
  const index = selectedSlots.value.findIndex(s => s.id === slotId)
  if (index > -1) {
    selectedSlots.value.splice(index, 1)
  } else {
    selectedSlots.value.push({ 
      id: slotId, date: selectedDate.value, courtName: court.name, sport: selectedSport.value, time, price: court.price 
    })
  }
}

const isSlotSelected = (courtName, time) => {
  return selectedSlots.value.some(s => s.id === `${selectedDate.value}-${selectedSport.value}-${courtName}-${time}`)
}

const totalPrice = computed(() => {
  if (bookingType.value === 'reguler') return selectedSlots.value.reduce((acc, curr) => acc + curr.price, 0)
  return memberCart.value.reduce((acc, curr) => acc + curr.price, 0)
})

const removeSlot = (id) => { selectedSlots.value = selectedSlots.value.filter(s => s.id !== id) }

const quickDates = computed(() => {
  return Array.from({ length: 5 }, (_, i) => {
    const d = new Date()
    d.setDate(d.getDate() + i)
    return { full: d.toISOString().substr(0, 10), day: d.toLocaleDateString('id-ID', { weekday: 'short' }), dateNum: d.getDate() }
  })
})

const courtIndices = ref({ 1: 0, 2: 0 })
const nextImg = (courtId, max) => { courtIndices.value[courtId] = (courtIndices.value[courtId] + 1) % max }
const prevImg = (courtId, max) => { courtIndices.value[courtId] = (courtIndices.value[courtId] - 1 + max) % max }

const handleCheckout = () => {
  const hasItems = bookingType.value === 'reguler' ? selectedSlots.value.length > 0 : memberCart.value.length > 0
  if (!hasItems) return
  
  const payload = {
    venueName: venues.name,
    bookingType: bookingType.value,
    items: bookingType.value === 'reguler' ? selectedSlots.value : memberCart.value,
    total: totalPrice.value,
    venueLocation: venues.location
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
            <div class="flex-[2] rounded-[32px] overflow-hidden border border-gray-100 shadow-sm"><img :src="venues.images[0]" class="w-full h-full object-cover" /></div>
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
                <span class="px-2 py-1 bg-green-600 text-white text-[8px] font-black uppercase rounded shadow-sm tracking-widest">Verified Venue</span>
                <div class="flex items-center gap-1 text-xs font-black text-gray-900"><Star class="fill-yellow-400 text-yellow-400" :size="14"/> {{ venues.rating }}</div>
              </div>
              <h2 class="text-2xl font-black text-gray-900 uppercase italic tracking-tighter leading-tight">{{ venues.name }}</h2>
              <p class="text-[11px] text-gray-500 font-medium leading-relaxed line-clamp-2 italic">"{{ venues.desc }}"</p>
              <div class="space-y-4 pt-2">
                <p class="text-[10px] font-black text-gray-400 uppercase tracking-widest flex items-center gap-2"><MapPin :size="12" class="text-green-600" /> {{ venues.location }}</p>
                <div class="flex flex-wrap gap-2 pt-2 border-t border-gray-200">
                  <span v-for="(f, i) in venues.facilities.slice(0, 3)" :key="i" class="px-2.5 py-1 bg-white border border-gray-100 rounded-lg text-[8px] font-black text-gray-400 uppercase">{{ f }}</span>
                  <span v-if="venues.facilities.length > 3" class="px-2.5 py-1 bg-green-100 text-green-700 rounded-lg text-[8px] font-black uppercase">+{{ venues.facilities.length - 3 }} Lainnya</span>
                </div>
                <div class="grid grid-cols-2 gap-2 pt-1">
                   <div class="flex items-center gap-1.5"><Undo2 :size="12" class="text-red-400"/><span class="text-[8px] font-black uppercase text-red-500">No Refund</span></div>
                   <div class="flex items-center gap-1.5"><RotateCcw :size="12" class="text-red-400"/><span class="text-[8px] font-black uppercase text-red-500">No Reschedule</span></div>
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
              <button @click="!isRegulerDisabled && (bookingType = 'reguler')" 
                class="p-6 rounded-[30px] border-2 transition-all text-left flex items-start gap-4 shadow-sm"
                :class="[bookingType === 'reguler' ? 'border-green-600 bg-green-50' : 'border-white bg-white', isRegulerDisabled ? 'opacity-40 grayscale' : '']">
                <div class="p-3 bg-white rounded-2xl shadow-sm text-green-600"><Calendar :size="24"/></div>
                <div><h4 class="font-black uppercase italic text-gray-900 text-xs">Pesan Reguler</h4><p class="text-[9px] font-bold text-gray-400 uppercase mt-1 tracking-tight">Sewa per jam, bebas atur jadwal.</p></div>
              </button>

              <button @click="!isMemberDisabled && (bookingType = 'member')" 
                class="p-6 rounded-[30px] border-2 transition-all text-left flex items-start gap-4 shadow-sm relative group"
                :class="[bookingType === 'member' ? 'border-green-600 bg-green-50' : 'border-white bg-white', isMemberDisabled ? 'opacity-40 grayscale' : '']">
                <div class="p-3 bg-white rounded-2xl shadow-sm text-yellow-500">
                  <Lock v-if="isMemberDisabled" :size="24" class="text-gray-400" />
                  <Crown v-else :size="24"/>
                </div>
                <div><h4 class="font-black uppercase italic text-gray-900 text-xs">Member</h4><p class="text-[9px] font-bold text-gray-400 uppercase mt-1 tracking-tight">Berlangganan rutin & lebih murah.</p></div>
              </button>
            </div>
          </div>

          <div v-if="bookingType === 'member'" class="space-y-12 animate-in fade-in slide-in-from-bottom-4 duration-500">
             <div class="space-y-4">
                <h3 class="text-xs font-black text-gray-400 uppercase tracking-[0.2em]">1. Pilih Paket Member</h3>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                   <button v-for="plan in venues.memberPlans" :key="plan.id" 
                    @click="selectedMemberPackage = plan.id"
                    class="p-6 rounded-[25px] border-2 text-left transition-all"
                    :class="selectedMemberPackage === plan.id ? 'border-green-600 bg-green-50' : 'bg-white border-gray-100 hover:border-gray-300'">
                     <div class="flex justify-between items-center mb-2">
                        <span class="text-[10px] font-black uppercase text-green-600 tracking-widest">{{ plan.type }}</span>
                        <CheckCircle2 v-if="selectedMemberPackage === plan.id" :size="16" class="text-green-600" />
                     </div>
                     <h4 class="font-black uppercase italic text-gray-900 text-sm">{{ plan.name }}</h4>
                     <p class="text-[9px] font-bold text-gray-400 mt-1 uppercase">{{ plan.desc }}</p>
                   </button>
                </div>
             </div>

             <div class="space-y-4">
                <h3 class="text-xs font-black text-gray-400 uppercase tracking-[0.2em]">2. Pilih Hari Rutin</h3>
                <div class="flex flex-wrap gap-2">
                   <button v-for="day in daysOfWeek" :key="day" 
                    @click="toggleMemberDay(day)"
                    class="px-5 py-3 rounded-2xl border-2 font-black uppercase text-[10px] transition-all"
                    :class="selectedMemberDays.includes(day) ? 'bg-gray-900 text-white border-gray-900 shadow-lg shadow-gray-900/20' : 'bg-white text-gray-400 border-gray-100 hover:border-gray-300'">
                    {{ day }}
                   </button>
                </div>
             </div>

             <div class="space-y-4">
                <h3 class="text-xs font-black text-gray-400 uppercase tracking-[0.2em]">3. Pilih Sesi & Court</h3>
                <div v-for="court in venues.courts" :key="court.id" class="bg-white rounded-[40px] overflow-hidden border border-gray-100 shadow-sm transition-all duration-500">
                  <div @click="activeCourtId = activeCourtId === court.id ? null : court.id" class="p-8 flex flex-col md:flex-row gap-8 items-start cursor-pointer hover:bg-gray-50/50 transition">
                    <div class="w-full md:w-1/3 h-48 rounded-3xl overflow-hidden shadow-sm shrink-0 relative"><img :src="court.gallery[0]" class="w-full h-full object-cover" /></div>
                    <div class="flex-1 space-y-4 w-full">
                      <div class="flex justify-between items-start"><h3 class="text-2xl font-black text-gray-900 uppercase italic tracking-tighter">{{ court.name }} &rsaquo;</h3><ChevronDown :size="20" class="text-gray-400" /></div>
                      <p class="text-green-600 font-black text-lg tracking-tighter leading-none">
                        Rp {{ court.memberPrice.toLocaleString() }} 
                        <span class="text-[10px] text-gray-400 uppercase tracking-widest font-bold">/ Sesi Rutin</span>
                      </p>
                      
                      <button @click.stop="addMemberToCart(court)" class="mt-2 px-6 py-3 bg-green-600 text-white rounded-xl text-[10px] font-black uppercase tracking-widest shadow-lg shadow-green-600/30 hover:scale-105 transition-all w-fit flex items-center gap-2">
                        <Plus :size="14" /> Tambahkan Paket Ini
                      </button>
                    </div>
                  </div>
                  <div v-show="activeCourtId === court.id" class="p-8 pt-0 border-t border-gray-50 animate-in slide-in-from-top-4 duration-300">
                    <div class="grid grid-cols-2 sm:grid-cols-4 gap-4 mt-8">
                      <button v-for="slot in court.slots" :key="slot" 
                        @click.stop="toggleTempMemberSlot(slot)" 
                        class="group text-center p-5 rounded-[25px] border-2 transition-all bg-gray-50/50 hover:border-green-600"
                        :class="tempMemberSlots.includes(slot) ? 'border-green-600 bg-green-50 text-green-700' : 'border-gray-100'">
                        <p class="text-[8px] font-black text-gray-400 uppercase mb-1 tracking-widest">Pilih Sesi</p>
                        <p class="text-xs font-black tracking-tighter">{{ slot }}</p>
                      </button>
                    </div>
                  </div>
                </div>
             </div>
          </div>

          <div v-if="bookingType === 'reguler'" class="space-y-6">
            <h2 class="text-xl font-black text-gray-900 uppercase italic tracking-tighter leading-none">Atur <span class="text-green-600">Jadwal.</span></h2>
            <div class="flex items-center gap-2 mb-4">
               <div class="flex gap-2 no-scrollbar">
                  <button v-for="d in quickDates" :key="d.full" @click="selectedDate = d.full" class="w-[65px] flex flex-col items-center py-3 rounded-2xl border-2 transition-all" :class="selectedDate === d.full ? 'border-green-600 bg-green-600 text-white shadow-lg shadow-green-600/20' : 'bg-white border-gray-100 text-gray-400'">
                    <span class="text-[8px] font-black uppercase mb-0.5">{{ d.day }}</span><span class="text-sm font-black">{{ d.dateNum }}</span>
                  </button>
               </div>
               <div class="relative bg-white h-full px-4 py-5 rounded-2xl border border-gray-100 shadow-sm flex items-center justify-center cursor-pointer hover:border-green-600 transition">
                  <Calendar :size="18" class="text-gray-400" /><input type="date" v-model="selectedDate" class="absolute inset-0 opacity-0 cursor-pointer" />
               </div>
            </div>

            <div v-for="court in venues.courts" :key="court.id" class="bg-white rounded-[40px] overflow-hidden border border-gray-100 shadow-sm transition-all duration-500 mb-4">
              <div @click="activeCourtId = activeCourtId === court.id ? null : court.id" class="p-8 flex flex-col md:flex-row gap-8 items-start cursor-pointer hover:bg-gray-50/50 transition">
                <div class="w-full md:w-1/3 h-48 rounded-3xl overflow-hidden shadow-sm shrink-0 relative group">
                  <img :src="court.gallery[courtIndices[court.id]]" class="w-full h-full object-cover transition-opacity duration-300" />
                  <div class="absolute inset-0 flex items-center justify-between px-2 opacity-0 group-hover:opacity-100 transition">
                    <button @click.stop="prevImg(court.id, court.gallery.length)" class="w-8 h-8 bg-white/90 rounded-full flex items-center justify-center shadow-lg"><ChevronLeft :size="16"/></button>
                    <button @click.stop="nextImg(court.id, court.gallery.length)" class="w-8 h-8 bg-white/90 rounded-full flex items-center justify-center shadow-lg"><ChevronRight :size="16"/></button>
                  </div>
                  <div class="absolute bottom-3 left-1/2 -translate-x-1/2 bg-black/60 px-3 py-1 rounded-full text-[9px] font-bold text-white uppercase tracking-widest">{{ courtIndices[court.id] + 1 }} / {{ court.gallery.length }}</div>
                </div>
                <div class="flex-1 space-y-4 w-full">
                  <div class="flex justify-between items-start"><h3 class="text-2xl font-black text-gray-900 uppercase italic tracking-tighter">{{ court.name }} &rsaquo;</h3><ChevronDown :size="20" class="text-gray-400" /></div>
                  <p class="text-green-600 font-black text-lg tracking-tighter leading-none">Rp {{ court.price.toLocaleString() }} <span class="text-[10px] text-gray-400 uppercase tracking-widest font-bold">/ Jam</span></p>
                  <p class="text-xs text-gray-500 font-medium leading-relaxed">{{ court.desc }}</p>
                  <div class="flex gap-4">
                     <span class="flex items-center gap-1.5 text-[10px] font-black uppercase tracking-widest text-gray-400"><Zap :size="14" class="text-green-600"/> {{ selectedSport }}</span>
                     <span class="flex items-center gap-1.5 text-[10px] font-black uppercase tracking-widest text-gray-400"><Info :size="14" class="text-green-600"/> Indoor</span>
                  </div>
                </div>
              </div>
              <div v-show="activeCourtId === court.id" class="p-8 pt-0 border-t border-gray-50 animate-in slide-in-from-top-4 duration-300">
                <div class="grid grid-cols-2 sm:grid-cols-4 gap-4 mt-8">
                  <button v-for="slot in court.slots" :key="slot" @click.stop="toggleSlot(court, slot)" class="group text-center p-5 rounded-[25px] border-2 transition-all" :class="isSlotSelected(court.name, slot) ? 'border-green-600 bg-green-50 text-green-700' : 'bg-white border-gray-100 hover:border-green-600'">
                    <p class="text-[8px] font-black text-gray-400 uppercase mb-1 tracking-widest">60 Menit</p>
                    <p class="text-xs font-black tracking-tighter">{{ slot }}</p>
                    <p class="text-[9px] font-black uppercase mt-1 tracking-widest" :class="isSlotSelected(court.name, slot) ? 'text-green-600' : 'text-gray-400'">{{ isSlotSelected(court.name, slot) ? 'Terpilih' : 'Tersedia' }}</p>
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>

        <aside class="w-full lg:w-[380px] lg:sticky lg:top-24 space-y-6">
          <div class="bg-[#0f172a] rounded-[35px] p-8 text-white shadow-2xl space-y-8">
            <h3 class="text-sm font-black uppercase italic border-b border-white/10 pb-4 tracking-widest">Ringkasan Pesanan</h3>
            
            <div class="space-y-6 max-h-[400px] overflow-y-auto no-scrollbar">
              <div v-if="bookingType === 'reguler'" class="space-y-4">
                <div v-for="(courts, date) in groupedRegulerSlots" :key="date" class="space-y-4 border-l-2 border-green-600/30 pl-4">
                  <p class="text-[9px] font-black text-green-400 uppercase tracking-widest border-b border-white/5 pb-2">{{ date }}</p>
                  <div v-for="(slots, courtName) in courts" :key="courtName" class="space-y-2">
                    <p class="text-[8px] font-black text-white/30 uppercase pl-1 tracking-widest">{{ courtName }}</p>
                    <div v-for="s in slots" :key="s.id" class="flex justify-between items-center bg-white/5 p-3 rounded-xl border border-white/5 group">
                      <div class="space-y-0.5">
                        <div class="flex items-center gap-1.5"><span class="text-[7px] font-black px-1.5 py-0.5 bg-green-600 rounded text-white uppercase">{{ s.sport }}</span></div>
                        <p class="text-[10px] font-medium text-white/40 italic">{{ s.time }}</p>
                      </div>
                      <button @click="removeSlot(s.id)" class="text-red-400"><X :size="14" /></button>
                    </div>
                  </div>
                </div>
              </div>

              <div v-else class="space-y-4">
                 <div v-for="(items, courtName) in groupedMemberCart" :key="courtName" class="space-y-4 border-l-2 border-yellow-500/30 pl-4">
                    <p class="text-[9px] font-black text-yellow-500 uppercase tracking-widest border-b border-white/5 pb-2">{{ courtName }}</p>
                    <div v-for="m in items" :key="m.id" class="p-4 bg-white/5 rounded-2xl border border-white/10 space-y-3">
                       <div class="flex justify-between items-start">
                          <div>
                             <span class="text-[7px] font-black px-1.5 py-0.5 bg-yellow-500 rounded text-black uppercase tracking-tighter">Paket Member</span>
                             <h4 class="text-[10px] font-black uppercase mt-1 tracking-tight">{{ m.packageName }}</h4>
                             <p class="text-[8px] font-bold text-white/30 uppercase tracking-widest">{{ m.sport }}</p>
                          </div>
                          <button @click="removeMemberItem(m.id)" class="text-red-400"><X :size="14" /></button>
                       </div>
                       <div class="flex items-center gap-2 text-[8px] font-black text-green-400 uppercase tracking-widest bg-green-400/5 p-2 rounded-lg">
                          <Clock :size="10" /> {{ m.times.join(', ') }}
                       </div>
                       <div class="text-[8px] font-bold text-white/40 uppercase px-2">Rutin: {{ m.days.join(', ') }}</div>
                    </div>
                 </div>
              </div>

              <p v-if="(bookingType === 'reguler' ? selectedSlots.length : memberCart.length) === 0" class="text-[10px] text-white/20 italic text-center uppercase tracking-widest py-4">Belum ada pilihan</p>
            </div>

            <div class="pt-6 border-t border-white/10 space-y-4">
              <div class="flex justify-between items-center text-[10px] font-bold text-white/40 uppercase tracking-widest">
                <span>Total ({{ bookingType === 'reguler' ? selectedSlots.length : memberCart.length }} Item)</span>
              </div>
              <div class="flex justify-between items-center text-2xl font-black uppercase italic tracking-tighter leading-none">
                <span>Total</span>
                <span class="text-green-600">RP {{ totalPrice.toLocaleString() }}</span>
              </div>
              <button @click="handleCheckout" :disabled="(bookingType === 'reguler' ? selectedSlots.length : memberCart.length) === 0" class="w-full py-5 bg-green-600 rounded-2xl font-black uppercase text-xs tracking-widest shadow-xl shadow-green-600/30 active:scale-95 transition-all disabled:opacity-20 mt-4">Lanjutkan</button>
            </div>
          </div>
        </aside>

      </div>
    </div>
  </div>
</template>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
</style>