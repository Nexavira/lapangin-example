<template>
  <div class="min-h-[85vh] flex items-center justify-center px-4 py-12">
    <div class="max-w-md w-full bg-white rounded-[40px] shadow-2xl border border-gray-100 p-10">
      <div class="text-center mb-10">
        <h2 class="text-3xl font-black tracking-tighter text-gray-900 uppercase">Buat Akun <span class="text-green-600 italic">Baru.</span></h2>
        <p class="text-gray-400 text-sm font-medium mt-2">Gabung dengan komunitas Lapangin sekarang!</p>
      </div>

      <form @submit.prevent class="space-y-4">
        <div>
          <label class="block text-[10px] font-black uppercase tracking-widest text-gray-400 mb-2 ml-4">Nama Lengkap</label>
          <input type="text" class="w-full px-6 py-4 bg-gray-50 border-transparent border-2 focus:border-green-500 focus:bg-white rounded-2xl transition outline-none font-bold text-sm" />
        </div>
        <div>
          <label class="block text-[10px] font-black uppercase tracking-widest text-gray-400 mb-2 ml-4">No. WhatsApp</label>
          <input type="tel" placeholder="0812..." class="w-full px-6 py-4 bg-gray-50 border-transparent border-2 focus:border-green-500 focus:bg-white rounded-2xl transition outline-none font-bold text-sm" />
        </div>
        <div>
          <label class="block text-[10px] font-black uppercase tracking-widest text-gray-400 mb-2 ml-4">Email</label>
          <input type="email" class="w-full px-6 py-4 bg-gray-50 border-transparent border-2 focus:border-green-500 focus:bg-white rounded-2xl transition outline-none font-bold text-sm" />
        </div>
        <div>
          <label class="block text-[10px] font-black uppercase tracking-widest text-gray-400 mb-2 ml-4">Password</label>
          <input type="password" class="w-full px-6 py-4 bg-gray-50 border-transparent border-2 focus:border-green-500 focus:bg-white rounded-2xl transition outline-none font-bold text-sm" />
        </div>

        <button class="w-full py-4 bg-green-600 text-white rounded-2xl font-black uppercase tracking-widest hover:bg-green-700 shadow-xl shadow-green-600/20 transition-all transform hover:-translate-y-1 mt-6">
          Daftar Sekarang
        </button>
      </form>
    </div>
  </div>
</template>