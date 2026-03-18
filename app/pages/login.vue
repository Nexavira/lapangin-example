<template>
  <div class="min-h-[80vh] flex items-center justify-center px-4 py-12">
    <div class="max-w-md w-full bg-white rounded-[40px] shadow-2xl border border-gray-100 p-10">
      <div class="text-center mb-10">
        <h2 class="text-3xl font-black tracking-tighter text-gray-900">SELAMAT DATANG <span class="text-green-600 italic">KEMBALI.</span></h2>
        <p class="text-gray-400 text-sm font-medium mt-2">Masuk untuk mulai booking lapangan favoritmu</p>
      </div>

      <form @submit.prevent class="space-y-4">
        <div>
          <label class="block text-[10px] font-black uppercase tracking-widest text-gray-400 mb-2 ml-4">Email Address</label>
          <input type="email" placeholder="nama@email.com" class="w-full px-6 py-4 bg-gray-50 border-transparent border-2 focus:border-green-500 focus:bg-white rounded-2xl transition outline-none font-bold" />
        </div>
        <div>
          <label class="block text-[10px] font-black uppercase tracking-widest text-gray-400 mb-2 ml-4">Password</label>
          <input type="password" placeholder="••••••••" class="w-full px-6 py-4 bg-gray-50 border-transparent border-2 focus:border-green-500 focus:bg-white rounded-2xl transition outline-none font-bold" />
        </div>
        
        <div class="flex justify-end pt-2">
          <a href="#" class="text-[10px] font-black uppercase tracking-widest text-gray-400 hover:text-green-600 transition">Lupa Password?</a>
        </div>

        <button class="w-full py-4 bg-gray-900 text-white rounded-2xl font-black uppercase tracking-widest hover:bg-green-600 shadow-xl shadow-gray-900/10 transition-all transform hover:-translate-y-1 mt-4">
          Masuk Sekarang
        </button>
      </form>

      <p class="text-center mt-8 text-sm font-bold text-gray-400">
        Belum punya akun? <NuxtLink to="/register" class="text-green-600 hover:underline">Daftar di sini</NuxtLink>
      </p>
    </div>
  </div>
</template>