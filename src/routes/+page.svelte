<script lang="ts">
  import Modal from "$lib/components/+modal.svelte";
  import Navbar from "$lib/components/+navbar.svelte";
</script>

<Navbar></Navbar>
<div class="flex flex-col overflow-x-hidden">
  <div
    class="mt-15 flex flex-col md:flex-row px-6 md:px-32 py-8 md:py-16 gap-6 md:gap-24 h-auto md:h-140 bg-amber-400"
  >
    <div class="bg-black w-full md:w-1/3 h-64 md:h-full rounded-2xl p-8">
      <h1 class="text-rose-700 text-3xl md:text-5xl font-bold">
        GMC <br />FITNESS
      </h1>
      <p class="text-white mt-4 text-xl">
        VISUALISASIKAN FASILITAS GYM DENGAN LEBIH MUDAH DAN AKURAT DENGAN MODEL
        TIGA DIMENSI
      </p>
    </div>
    <div class="w-full md:w-2/3">
      <img
        src="/home_1.png"
        class="border-4 rounded-2xl w-full h-64 md:h-full object-cover"
        alt="Background"
      />
    </div>
  </div>

  <div class="h-100 md:h-140 w-full px-32 py-16 flex relative items-center">
    <img
      src="/home_2.png"
      class="absolute inset-0 w-full h-full object-cover z-0"
      alt=""
    />

    <div
      class="z-10 w-3/4 md:w-1/3 md:ml-auto rounded-2xl p-8 border-4 border-white bg-black h-full"
    >
      <h1 class="text-rose-700 text-3xl md:text-5xl font-bold">
        MODEL TIGA DIMENSI
      </h1>
      <p class="text-white mt-4 text-xl">
        VISUALISASIKAN FASILITAS GYM DENGAN LEBIH MUDAH DAN AKURAT DENGAN MODEL
        TIGA DIMENSI
      </p>
    </div>
  </div>

  <div
    class="flex flex-col md:flex-row px-6 md:px-32 py-8 md:py-16 gap-6 md:gap-24 h-auto md:h-140 bg-amber-400"
  >
    <div class="bg-black w-full md:w-1/3 h-64 md:h-full rounded-2xl p-8">
      <h1 class="text-rose-700 text-3xl md:text-5xl font-bold">
        GMC <br />FITNESS
      </h1>
      <p class="text-white mt-4 text-xl">
        VISUALISASIKAN FASILITAS GYM DENGAN LEBIH MUDAH DAN AKURAT DENGAN MODEL
        TIGA DIMENSI
      </p>
    </div>
    <div class="w-full md:w-2/3">
      <img
        src="/home_3.png"
        class="border-4 rounded-2xl w-full h-64 md:h-full object-cover"
        alt="Background"
      />
    </div>
  </div>

  <div
    class="flex justify-center px-6 md:px-32 pt-8 pb-16 gap-6 md:gap-24 h-auto bg-amber-400"
  >
    <div class="bg-red-500 w-32 h-12 my-auto rounded-3xl"></div>
  </div>

  <div class="flex justify-between px-48 py-32 bg-black gap-16">
    <div class="w-1/3">
      <img src="/home_4.png" class="h-full object-cover rounded-2xl" alt="" />
    </div>
    <div class="flex md:w-2/3 justify-between sm:flex-col md:flex-row">
      <div class="w-1/2 text-white">
        <h1 class=" text-4xl font-bold">HUBUNGI KAMI</h1>
        <p class="mt-4 text-2xl font-bold">08XX XXXX XXXX</p>
        <p class="mt-4 text-2xl font-bold">@gmcfitness</p>
      </div>
      <div class="w-1/2">
        <img src="/home_1.png" alt="" class="h-full object-cover rounded-2xl" />
      </div>
    </div>
  </div>
</div>

<style>
  :global(html, body) {
    overflow-y: auto !important;
    height: auto !important;
  }
</style>
