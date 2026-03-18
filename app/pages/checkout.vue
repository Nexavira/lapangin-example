<script setup>
import { 
  ChevronLeft, CreditCard, Smartphone, CheckCircle2, 
  ShieldCheck, Calendar, Info, X, AlertCircle, RotateCcw, Undo2 
} from 'lucide-vue-next'

const router = useRouter()
const checkoutData = ref(null)
const selectedPayment = ref('qris')
const isConfirmModalOpen = ref(false)

onMounted(() => {
  const data = localStorage.getItem('lapangin_checkout')
  if (data) checkoutData.value = JSON.parse(data)
})

const groupedCheckout = computed(() => {
  if (!checkoutData.value) return {}
  return checkoutData.value.slots.reduce((groups, slot) => {
    const date = slot.date;
    if (!groups[date]) groups[date] = [];
    groups[date].push(slot);
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
      
      <NuxtLink :to="`/lapangan/1`" class="inline-flex items-center gap-2 text-[10px] font-black uppercase tracking-widest text-gray-400 mb-8 hover:text-green-600 transition">
        <ChevronLeft :size="14" /> Kembali
      </NuxtLink>

      <div class="grid grid-cols-1 lg:grid-cols-12 gap-10">
        <div class="lg:col-span-7 space-y-8">
          <h1 class="text-4xl font-black text-gray-900 uppercase italic tracking-tighter">Final <span class="text-green-600">Checkout.</span></h1>

          <div class="bg-white rounded-[40px] p-8 shadow-sm border border-gray-100 space-y-8">
            <div class="flex justify-between items-start border-b border-gray-50 pb-6">
              <div>
                <h3 class="text-xl font-black text-gray-900 uppercase italic">{{ checkoutData.venueName }}</h3>
                <p class="text-[10px] font-bold text-gray-400 uppercase tracking-widest">{{ checkoutData.sportCategory }} • {{ checkoutData.venueLocation }}</p>
              </div>
              <span class="px-3 py-1 bg-green-600 text-white text-[9px] font-black uppercase rounded-lg">{{ checkoutData.bookingType }}</span>
            </div>

            <div class="space-y-6">
              <div v-for="(slots, date) in groupedCheckout" :key="date" class="space-y-3">
                <p class="text-[10px] font-black text-gray-400 uppercase tracking-[0.2em] flex items-center gap-2">
                  <Calendar :size="12" class="text-green-600" /> {{ date }}
                </p>
                <div v-for="s in slots" :key="s.id" class="flex justify-between items-center bg-gray-50 p-4 rounded-2xl border border-gray-100">
                  <p class="text-xs font-black text-gray-900 uppercase italic">{{ s.courtName }} • {{ s.time }}</p>
                  <p class="font-black text-gray-900 text-sm tracking-tighter">Rp {{ s.price.toLocaleString() }}</p>
                </div>
              </div>
            </div>

            <div class="space-y-3">
              <div class="p-4 bg-blue-50 rounded-2xl border border-blue-100 flex gap-4">
                 <ShieldCheck :size="20" class="text-blue-600 shrink-0" />
                 <p class="text-[10px] font-bold text-blue-800 uppercase tracking-tight">E-Tiket otomatis dikirim ke WhatsApp setelah pembayaran terverifikasi.</p>
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
              <button @click="selectedPayment = 'qris'" :class="selectedPayment === 'qris' ? 'border-green-600 bg-white/5' : 'border-white/5'" class="w-full p-5 rounded-2xl border-2 transition-all flex items-center justify-between">
                <div class="flex items-center gap-4"><Smartphone :size="18"/><span class="text-xs font-black uppercase tracking-widest">QRIS / E-Wallet</span></div>
                <CheckCircle2 v-if="selectedPayment === 'qris'" :size="18" class="text-green-600" />
              </button>
              <button @click="selectedPayment = 'va'" :class="selectedPayment === 'va' ? 'border-green-600 bg-white/5' : 'border-white/5'" class="w-full p-5 rounded-2xl border-2 transition-all flex items-center justify-between">
                <div class="flex items-center gap-4"><CreditCard :size="18"/><span class="text-xs font-black uppercase tracking-widest">Virtual Account</span></div>
                <CheckCircle2 v-if="selectedPayment === 'va'" :size="18" class="text-green-600" />
              </button>
            </div>

            <div class="space-y-4 pt-4 border-t border-white/10">
              <div class="flex justify-between text-[10px] font-bold text-white/40 uppercase"><span>Total ({{ checkoutData.slots.length }} Sesi)</span><span>Rp {{ checkoutData.total.toLocaleString() }}</span></div>
              <div class="flex justify-between text-[10px] font-bold text-white/40 uppercase"><span>Biaya Layanan</span><span>Rp {{ serviceFee.toLocaleString() }}</span></div>
              <div class="flex justify-between items-center text-3xl font-black uppercase italic pt-4"><span>Total</span><span class="text-green-400">Rp {{ finalTotal.toLocaleString() }}</span></div>
            </div>

            <button @click="isConfirmModalOpen = true" class="w-full py-6 bg-green-600 hover:bg-green-500 rounded-[25px] font-black uppercase text-xs tracking-[0.2em] transition-all transform active:scale-95 shadow-xl shadow-green-600/20">Bayar Sekarang</button>
          </div>
        </aside>
      </div>
    </div>

    <div v-if="isConfirmModalOpen" class="fixed inset-0 z-[100] flex items-center justify-center p-4 bg-gray-900/80 backdrop-blur-md">
      <div class="bg-white w-full max-w-sm rounded-[40px] p-10 text-center space-y-6 animate-in zoom-in-95">
        <div class="w-20 h-20 bg-green-100 rounded-full flex items-center justify-center mx-auto text-green-600">
          <AlertCircle :size="40" />
        </div>
        <div class="space-y-2">
          <h3 class="text-xl font-black text-gray-900 uppercase italic">Konfirmasi Bayar?</h3>
          <p class="text-xs text-gray-400 font-medium">Pastikan jadwal yang Anda pilih sudah benar. Pesanan tidak dapat diubah setelah dibayar.</p>
        </div>
        <div class="flex gap-3">
          <button @click="isConfirmModalOpen = false" class="flex-1 py-4 bg-gray-100 rounded-2xl font-black uppercase text-[10px] text-gray-400">Batal</button>
          <button @click="confirmPayment" class="flex-1 py-4 bg-green-600 rounded-2xl font-black uppercase text-[10px] text-white shadow-lg shadow-green-600/20">Ya, Bayar</button>
        </div>
      </div>
    </div>
  </div>
</template>