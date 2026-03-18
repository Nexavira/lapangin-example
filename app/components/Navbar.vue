<script setup>
  import { ref, onMounted, onUnmounted } from 'vue'
  import { Menu, X, User } from 'lucide-vue-next'

  const isMenuOpen = ref(false)
  const isScrolled = ref(false)
  const route = useRoute()
  
  // State untuk Modal
  const isAuthOpen = ref(false)
  const authMode = ref('login')

  const toggleMenu = () => {
    isMenuOpen.value = !isMenuOpen.value
  }

  const closeMenu = () => {
    isMenuOpen.value = false
  }

  const handleScroll = () => {
    isScrolled.value = window.scrollY > 20
  }

  const openAuth = (mode) => {
    authMode.value = mode
    isAuthOpen.value = true
  }

  onMounted(() => window.addEventListener('scroll', handleScroll))
  onUnmounted(() => window.removeEventListener('scroll', handleScroll))
</script>

<template>
  <nav 
    class="sticky top-0 z-50 transition-all duration-500"
    :class="[
      isScrolled ? 'bg-white/80 shadow-lg py-2 backdrop-blur-lg' : 'bg-white py-4',
      'border-b border-gray-100'
    ]"
  >
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex justify-between h-14 items-center">
        
        <div class="flex-shrink-0 flex items-center">
          <NuxtLink to="/" @click="closeMenu" class="flex items-center gap-2">
            <div class="bg-green-600 p-1.5 rounded-lg">
               <div class="w-6 h-6 bg-white rounded-sm italic font-black text-green-600 flex items-center justify-center text-xs">L</div>
            </div>
            <span class="font-black text-xl tracking-tighter text-gray-900 uppercase">Lapan<span class="text-green-600 italic">gin</span></span>
          </NuxtLink>
        </div>

        <div class="hidden md:flex space-x-6 lg:space-x-8 items-center">
          <NuxtLink 
            v-for="link in [
              { name: 'Home', path: '/' },
              { name: 'Cari Lapangan', path: '/cari' },
              { name: 'Promo', path: '/promo' }
            ]" 
            :key="link.path"
            :to="link.path"
            class="transition-all duration-300 font-bold text-[11px] uppercase tracking-widest relative group"
            :class="route.path === link.path ? 'text-green-600' : 'text-gray-500 hover:text-green-500'"
          >
            {{ link.name }}
            <span class="absolute -bottom-1 left-0 w-0 h-0.5 bg-green-500 transition-all duration-300 group-hover:w-full" :class="route.path === link.path ? 'w-full' : ''"></span>
          </NuxtLink>
          
          <div class="h-4 w-px bg-gray-200 mx-2"></div>

          <div class="flex items-center gap-3">
            <button @click="openAuth('login')" class="text-xs font-black uppercase tracking-widest text-gray-900 hover:text-green-600 transition">
              Masuk
            </button>
            <button @click="openAuth('register')" class="px-5 py-2.5 bg-green-600 text-white rounded-full text-xs font-black uppercase tracking-widest hover:bg-green-700 transition shadow-lg shadow-green-600/20 transform hover:-translate-y-0.5">
              Daftar
            </button>
          </div>
        </div>

        <div class="md:hidden flex items-center">
          <button @click="toggleMenu" class="text-green-600 p-2 focus:outline-none">
            <Menu v-if="!isMenuOpen" :size="28" stroke-width="2" />
            <X v-else :size="28" stroke-width="2" class="text-red-500" />
          </button>
        </div>
      </div>
    </div>

    <transition
      enter-active-class="transition duration-300 ease-out"
      enter-from-class="opacity-0 -translate-y-10"
      enter-to-class="opacity-100 translate-y-0"
    >
      <div v-show="isMenuOpen" class="md:hidden bg-white/95 backdrop-blur-xl border-b border-gray-100 shadow-2xl">
        <div class="px-6 pt-4 pb-10 space-y-2">
          <NuxtLink v-for="l in ['Home', 'Cari Lapangan', 'Promo']" :key="l" class="block px-4 py-4 text-sm font-black uppercase tracking-widest text-gray-600 hover:text-green-600">{{ l }}</NuxtLink>
          <div class="pt-4 border-t border-gray-100 mt-4 flex flex-col gap-3">
            <button @click="openAuth('login'); closeMenu()" class="w-full py-4 bg-gray-900 text-white rounded-2xl font-black uppercase tracking-widest text-xs">Masuk</button>
            <button @click="openAuth('register'); closeMenu()" class="w-full py-4 bg-green-600 text-white rounded-2xl font-black uppercase tracking-widest text-xs">Daftar</button>
          </div>
        </div>
      </div>
    </transition>
  </nav>

  <AuthModal 
    :isOpen="isAuthOpen" 
    :mode="authMode" 
    @close="isAuthOpen = false" 
    @switchMode="authMode = authMode === 'login' ? 'register' : 'login'" 
  />
</template>