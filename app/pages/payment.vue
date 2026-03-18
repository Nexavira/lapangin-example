<script setup>
import { 
  Clock, CheckCircle2, Copy, Smartphone, CreditCard, ChevronLeft 
} from 'lucide-vue-next'

const paymentMethod = ref('qris')
const timeLeft = ref(900) // 15 Menit

onMounted(() => {
  paymentMethod.value = localStorage.getItem('lapangin_payment_method') || 'qris'
  const timer = setInterval(() => {
    if (timeLeft.value > 0) timeLeft.value--
  }, 1000)
})

const formatTime = (seconds) => {
  const m = Math.floor(seconds / 60)
  const s = seconds % 60
  return `${m}:${s < 10 ? '0' : ''}${s}`
}
</script>

<template>
  <div class="bg-gray-50 min-h-screen py-20">
    <div class="max-w-md mx-auto px-6 text-center space-y-8">
      
      <div class="space-y-2">
        <h1 class="text-3xl font-black text-gray-900 uppercase italic tracking-tighter">Selesaikan <span class="text-green-600">Pembayaran.</span></h1>
        <div class="inline-flex items-center gap-2 px-4 py-2 bg-white rounded-full border border-gray-100 shadow-sm">
          <Clock :size="14" class="text-orange-500" />
          <span class="text-xs font-black text-gray-900">{{ formatTime(timeLeft) }}</span>
        </div>
      </div>

      <div v-if="paymentMethod === 'qris'" class="bg-white rounded-[40px] p-10 shadow-sm border border-gray-100 space-y-6">
        <div class="flex justify-center"><Smartphone class="text-green-600" :size="32"/></div>
        <p class="text-[10px] font-black text-gray-400 uppercase tracking-widest">Scan QRIS untuk membayar</p>
        <div class="bg-gray-100 aspect-square rounded-3xl flex items-center justify-center border-4 border-gray-50 p-4">
           <img src="https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=LAPANGIN-DUMMY-PAYMENT" class="w-full h-full" />
        </div>
        <p class="text-[10px] font-bold text-gray-400 uppercase italic">Mendukung GoPay, OVO, Dana, LinkAja & Mobile Banking</p>
      </div>

      <div v-else class="bg-white rounded-[40px] p-10 shadow-sm border border-gray-100 space-y-8 text-left">
        <div class="flex justify-center"><CreditCard class="text-green-600" :size="32"/></div>
        <div class="space-y-4">
          <div v-for="bank in ['Bank Mandiri', '88701928374655']" :key="bank" class="space-y-1">
             <p class="text-[9px] font-black text-gray-400 uppercase tracking-widest">{{ bank.includes('Bank') ? 'Nama Bank' : 'Nomor Virtual Account' }}</p>
             <div class="flex justify-between items-center bg-gray-50 p-4 rounded-xl border border-gray-100 font-black text-gray-900 italic">
                {{ bank }} <Copy v-if="!bank.includes('Bank')" :size="14" class="text-green-600 cursor-pointer" />
             </div>
          </div>
        </div>
      </div>

      <button @click="$router.push('/success')" class="w-full py-6 bg-gray-900 text-white rounded-[25px] font-black uppercase text-xs tracking-widest hover:bg-green-600 transition-all shadow-xl shadow-gray-900/10">
        Sudah Bayar
      </button>

      <NuxtLink to="/checkout" class="block text-[10px] font-black text-gray-400 uppercase hover:text-red-500 transition">Batalkan Pesanan</NuxtLink>
    </div>
  </div>
</template>