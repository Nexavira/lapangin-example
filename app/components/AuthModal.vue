<script setup>
import { X, Mail, Lock, User, Phone } from 'lucide-vue-next'

const props = defineProps(['isOpen', 'mode']) // mode: 'login' | 'register'
const emit = defineEmits(['close', 'switchMode'])

const closeModal = () => emit('close')
</script>

<template>
  <Transition
    enter-active-class="transition duration-300 ease-out"
    enter-from-class="opacity-0"
    enter-to-class="opacity-100"
    leave-active-class="transition duration-200 ease-in"
    leave-from-class="opacity-100"
    leave-to-class="opacity-0"
  >
    <div v-if="isOpen" class="fixed inset-0 z-[60] flex items-center justify-center p-4 bg-gray-900/60 backdrop-blur-sm">
      <div class="relative w-full max-w-md bg-white rounded-[40px] shadow-2xl p-8 md:p-10 overflow-hidden">
        <button @click="closeModal" class="absolute top-6 right-6 text-gray-400 hover:text-gray-900 transition">
          <X :size="24" />
        </button>

        <div class="text-center mb-8">
          <h2 class="text-3xl font-black tracking-tighter text-gray-900 uppercase">
            {{ mode === 'login' ? 'Selamat Datang' : 'Buat Akun' }} <span class="text-green-600 italic">Baru.</span>
          </h2>
        </div>

        <div class="space-y-4">
          <div v-if="mode === 'register'">
            <label class="block text-[10px] font-black uppercase tracking-widest text-gray-400 mb-2 ml-4">Nama Lengkap</label>
            <div class="relative">
              <User class="absolute left-5 top-4 text-gray-400" :size="18" />
              <input type="text" placeholder="Dimas Agung" class="w-full pl-12 pr-6 py-4 bg-gray-50 border-2 border-transparent focus:border-green-500 focus:bg-white rounded-2xl outline-none font-bold transition" />
            </div>
          </div>

          <div>
            <label class="block text-[10px] font-black uppercase tracking-widest text-gray-400 mb-2 ml-4">Email Address</label>
            <div class="relative">
              <Mail class="absolute left-5 top-4 text-gray-400" :size="18" />
              <input type="email" placeholder="nama@email.com" class="w-full pl-12 pr-6 py-4 bg-gray-50 border-2 border-transparent focus:border-green-500 focus:bg-white rounded-2xl outline-none font-bold transition" />
            </div>
          </div>

          <div>
            <label class="block text-[10px] font-black uppercase tracking-widest text-gray-400 mb-2 ml-4">Password</label>
            <div class="relative">
              <Lock class="absolute left-5 top-4 text-gray-400" :size="18" />
              <input type="password" placeholder="••••••••" class="w-full pl-12 pr-6 py-4 bg-gray-50 border-2 border-transparent focus:border-green-500 focus:bg-white rounded-2xl outline-none font-bold transition" />
            </div>
          </div>

          <button class="w-full py-4 mt-4 bg-gray-900 text-white rounded-2xl font-black uppercase tracking-widest hover:bg-green-600 shadow-xl shadow-gray-900/10 transition-all transform hover:-translate-y-1">
            {{ mode === 'login' ? 'Masuk Sekarang' : 'Daftar Sekarang' }}
          </button>
        </div>

        <p class="text-center mt-8 text-sm font-bold text-gray-400">
          {{ mode === 'login' ? 'Belum punya akun?' : 'Sudah punya akun?' }}
          <button @click="emit('switchMode')" class="text-green-600 hover:underline decoration-2 underline-offset-4 ml-1">
            {{ mode === 'login' ? 'Daftar di sini' : 'Masuk di sini' }}
          </button>
        </p>
      </div>
    </div>
  </Transition>
</template>