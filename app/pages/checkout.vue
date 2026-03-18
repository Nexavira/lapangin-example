<script setup>
import { 
  ChevronLeft, CreditCard, Smartphone, CheckCircle2, 
  ShieldCheck, Calendar, Info, X, AlertCircle, RotateCcw, Undo2, Crown, Clock
} from 'lucide-vue-next'

const router = useRouter()
const checkoutData = ref(null)
const selectedPayment = ref('qris')
const isConfirmModalOpen = ref(false)

onMounted(() => {
  const data = localStorage.getItem('lapangin_checkout')
  if (data) checkoutData.value = JSON.parse(data)
})

// Grouping Khusus Reguler (Berdasarkan Tanggal)
const groupedReguler = computed(() => {
  if (!checkoutData.value || checkoutData.value.bookingType !== 'reguler') return {}
  return checkoutData.value.items.reduce((groups, item) => {
    const date = item.date;
    if (!groups[date]) groups[date] = [];
    groups[date].push(item);
    return groups;
  }, {});
});

// Grouping Khusus Member (Berdasarkan Lapangan)
const groupedMember = computed(() => {
  if (!checkoutData.value || checkoutData.value.bookingType !== 'member') return {}
  return checkoutData.value.items.reduce((groups, item) => {
    const court = item.courtName;
    if (!groups[court]) groups[court] = [];
    groups[court].push(item);
    return groups;
  }, {});
});

const serviceFee = 2500
const finalTotal = computed(() => (checkoutData.value?.total || 0) + serviceFee)

const confirmPayment = () => {
  localStorage.setItem('lapangin_payment_method', selectedPayment.value)
  router.push('/payment')
}
</script>

<template>
  <div class="bg-gray-50 min-h-screen pb-20 pt-10">
    <div class="max-w-5xl mx-auto px-4 md:px-6" v-if="checkoutData">
      
      <NuxtLink to="/lapangan/1" class="inline-flex items-center gap-2 text-[10px] font-black uppercase tracking-widest text-gray-400 mb-8 hover:text-green-600 transition">
        <ChevronLeft :size="14" /> Kembali
      </NuxtLink>

      <div class="grid grid-cols-1 lg:grid-cols-12 gap-10">
        <div class="lg:col-span-7 space-y-8">
          <h1 class="text-4xl font-black text-gray-900 uppercase italic tracking-tighter">Final <span class="text-green-600">Checkout.</span></h1>

          <div class="bg-white rounded-[40px] p-8 shadow-sm border border-gray-100 space-y-8">
            
            <div class="flex justify-between items-start border-b border-gray-50 pb-6">
              <div>
                <h3 class="text-xl font-black text-gray-900 uppercase italic">{{ checkoutData.venueName }}</h3>
                <p class="text-[10px] font-bold text-gray-400 uppercase tracking-widest mt-1">{{ checkoutData.venueLocation }}</p>
              </div>
              <span class="px-3 py-1 text-white text-[9px] font-black uppercase rounded-lg shadow-sm"
                    :class="checkoutData.bookingType === 'reguler' ? 'bg-green-600 shadow-green-600/20' : 'bg-yellow-500 shadow-yellow-500/20'">
                {{ checkoutData.bookingType }}
              </span>
            </div>

            <div v-if="checkoutData.bookingType === 'reguler'" class="space-y-8">
              <div v-for="(slots, date) in groupedReguler" :key="date" class="space-y-4">
                <p class="text-[10px] font-black text-gray-400 uppercase tracking-[0.2em] flex items-center gap-2">
                  <Calendar :size="12" class="text-green-600" /> {{ date }}
                </p>
                
                <div v-for="s in slots" :key="s.id" class="flex justify-between items-center bg-gray-50 p-5 rounded-[25px] border border-gray-100">
                  <div class="space-y-1">
                    <div class="flex items-center gap-2">
                      <span class="px-2 py-0.5 bg-green-600 text-white text-[8px] font-black uppercase rounded">{{ s.sport }}</span>
                      <p class="text-xs font-black text-gray-900 uppercase italic">{{ s.courtName }}</p>
                    </div>
                    <p class="text-[10px] font-bold text-gray-400 uppercase tracking-tight">{{ s.time }} (60 Menit)</p>
                  </div>
                  <p class="font-black text-gray-900 text-sm tracking-tighter">Rp {{ s.price.toLocaleString() }}</p>
                </div>
              </div>
            </div>

            <div v-else class="space-y-8">
              <div v-for="(items, court) in groupedMember" :key="court" class="space-y-4">
                <p class="text-[10px] font-black text-yellow-500 uppercase tracking-[0.2em] flex items-center gap-2">
                  <Crown :size="12" class="text-yellow-500" /> {{ court }}
                </p>
                
                <div v-for="m in items" :key="m.id" class="flex flex-col sm:flex-row sm:justify-between sm:items-center gap-4 bg-gray-50 p-5 rounded-[25px] border border-gray-100">
                  <div class="space-y-2">
                    <div class="flex items-center gap-2">
                      <span class="px-2 py-0.5 bg-yellow-500 text-black text-[8px] font-black uppercase rounded tracking-tighter">Paket Member</span>
                      <p class="text-xs font-black text-gray-900 uppercase italic">{{ m.packageName }}</p>
                    </div>
                    <div class="flex items-center gap-2 text-[10px] font-bold text-gray-400 uppercase tracking-tight">
                      <span>{{ m.sport }}</span> &bull; 
                      <span class="text-green-600 flex items-center gap-1"><Clock :size="10"/> {{ m.times.join(', ') }}</span>
                    </div>
                    <p class="text-[9px] font-black text-gray-500 uppercase tracking-widest bg-gray-200/50 w-fit px-2 py-1 rounded-md">
                      Rutin: {{ m.days.join(', ') }}
                    </p>
                  </div>
                  <p class="font-black text-gray-900 text-sm tracking-tighter self-end sm:self-auto">Rp {{ m.price.toLocaleString() }}</p>
                </div>
              </div>
            </div>

            <div class="space-y-3 pt-4 border-t border-gray-50">
              <div class="p-4 bg-blue-50 rounded-2xl border border-blue-100 flex gap-4">
                 <ShieldCheck :size="20" class="text-blue-600 shrink-0" />
                 <p class="text-[10px] font-bold text-blue-800 uppercase tracking-tight leading-relaxed">
                   E-Tiket otomatis dikirim ke WhatsApp setelah pembayaran terverifikasi oleh sistem.
                 </p>
              </div>
              <div class="grid grid-cols-2 gap-3">
                <div class="p-3 bg-gray-50 rounded-xl border border-gray-100 flex items-center gap-2">
                  <Undo2 :size="14" class="text-red-400" />
                  <span class="text-[9px] font-black text-gray-400 uppercase">Refund: <span class="text-red-500">Tidak Bisa</span></span>
                </div>
                <div class="p-3 bg-gray-50 rounded-xl border border-gray-100 flex items-center gap-2">
                  <RotateCcw :size="14" class="text-red-400" />
                  <span class="text-[9px] font-black text-gray-400 uppercase">Reschedule: <span class="text-red-500">Tidak Bisa</span></span>
                </div>
              </div>
            </div>
          </div>
        </div>

        <aside class="lg:col-span-5 space-y-6">
          <div class="bg-[#0f172a] rounded-[40px] p-8 text-white shadow-2xl space-y-8 lg:sticky lg:top-24">
            <h3 class="text-sm font-black uppercase italic border-b border-white/10 pb-4">Metode Pembayaran</h3>
            
            <div class="space-y-3">
              <button @click="selectedPayment = 'qris'" :class="selectedPayment === 'qris' ? 'border-green-600 bg-white/5' : 'border-white/5 hover:border-white/10'" class="w-full p-5 rounded-2xl border-2 transition-all flex items-center justify-between group">
                <div class="flex items-center gap-4">
                  <div class="p-2 bg-white rounded-lg text-gray-900 group-hover:scale-110 transition"><Smartphone :size="18"/></div>
                  <span class="text-xs font-black uppercase tracking-widest">QRIS / E-Wallet</span>
                </div>
                <CheckCircle2 v-if="selectedPayment === 'qris'" :size="18" class="text-green-600" />
              </button>

              <button @click="selectedPayment = 'va'" :class="selectedPayment === 'va' ? 'border-green-600 bg-white/5' : 'border-white/5 hover:border-white/10'" class="w-full p-5 rounded-2xl border-2 transition-all flex items-center justify-between group">
                <div class="flex items-center gap-4">
                  <div class="p-2 bg-white rounded-lg text-gray-900 group-hover:scale-110 transition"><CreditCard :size="18"/></div>
                  <span class="text-xs font-black uppercase tracking-widest">Virtual Account</span>
                </div>
                <CheckCircle2 v-if="selectedPayment === 'va'" :size="18" class="text-green-600" />
              </button>
            </div>

            <div class="space-y-4 pt-4 border-t border-white/10">
              <div class="flex justify-between text-[10px] font-bold text-white/40 uppercase tracking-widest">
                <span>Total ({{ checkoutData.items.length }} Item)</span>
                <span>Rp {{ checkoutData.total.toLocaleString() }}</span>
              </div>
              <div class="flex justify-between text-[10px] font-bold text-white/40 uppercase tracking-widest">
                <span>Biaya Layanan</span>
                <span>Rp {{ serviceFee.toLocaleString() }}</span>
              </div>
              <div class="flex justify-between items-center text-3xl font-black uppercase italic pt-4 tracking-tighter">
                <span>Total</span>
                <span class="text-green-400">Rp {{ finalTotal.toLocaleString() }}</span>
              </div>
            </div>

            <button @click="isConfirmModalOpen = true" class="w-full py-6 bg-green-600 hover:bg-green-500 rounded-[25px] font-black uppercase text-xs tracking-[0.2em] transition-all transform active:scale-95 shadow-xl shadow-green-600/20">
              Bayar Sekarang
            </button>
          </div>
        </aside>
      </div>
    </div>
  </div>

  <div v-if="isConfirmModalOpen" class="fixed inset-0 z-[100] flex items-center justify-center p-4 bg-gray-900/80 backdrop-blur-md">
    <div class="bg-white w-full max-w-sm rounded-[40px] p-10 text-center space-y-6 animate-in zoom-in-95">
      <div class="w-20 h-20 bg-green-100 rounded-full flex items-center justify-center mx-auto text-green-600">
        <AlertCircle :size="40" />
      </div>
      <div class="space-y-2">
        <h3 class="text-xl font-black text-gray-900 uppercase italic">Konfirmasi?</h3>
        <p class="text-xs text-gray-400 font-medium">Jadwal yang dipilih sudah benar? Pesanan tidak dapat dibatalkan.</p>
      </div>
      <div class="flex gap-3">
        <button @click="isConfirmModalOpen = false" class="flex-1 py-4 bg-gray-100 rounded-2xl font-black uppercase text-[10px] text-gray-400 hover:bg-gray-200 transition">Batal</button>
        <button @click="confirmPayment" class="flex-1 py-4 bg-green-600 rounded-2xl font-black uppercase text-[10px] text-white shadow-lg shadow-green-600/20 hover:bg-green-500 transition">Ya, Bayar</button>
      </div>
    </div>
  </div>
</template>