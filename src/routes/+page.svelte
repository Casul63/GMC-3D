<script lang="ts">
  import Footer from "$lib/components/+footer.svelte";
  import { page } from "$app/state";
</script>

<div class="relative flex flex-col overflow-x-hidden pt-15">
  <div class="absolute inset-0 z-0 overflow-hidden pointer-events-none">
    <svg
      width="100%"
      height="100%"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
      xmlns:xlink="http://www.w3.org/1999/xlink"
    >
      <rect
        width="100%"
        height="100%"
        fill="url(#pattern0_146_135)"
        fill-opacity="0.3"
      />
      <defs>
        <pattern
          id="pattern0_146_135"
          patternUnits="userSpaceOnUse"
          width="48"
          height="48"
        >
          <use xlink:href="#image0_146_135" transform="scale(1)" />
        </pattern>
        <image
          id="image0_146_135"
          width="48"
          height="48"
          preserveAspectRatio="none"
          xlink:href="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADAAAAAwCAYAAABXAvmHAAAAAXNSR0IArs4c6QAAARxJREFUaAXtmFkKwzAMRH3BXqpX7n+ph9Q/DkIaxyuMwDGWtTxNKKVNKaXXf+UtbC054eJsIGA+eWGPGBsfqfk4JgoVjXsM1FLAg/PuW3p2z7EgLX93gB4Fa9j63KPH8BoF+p07MR/w4WBMA8B/88J+nB39Bgo8dlh9vrybPi1Yy7/VGB6kd790mChcNG7qMCwUGz98GABhMdaSw9RXrBSQAlJACkgBKdBLgZZv7ZacXry3OoBhfpay8beGIxxRqGjcCEa3pgfn3bsNZgRYkJZ/BhPdo4atz3TBFQkFWv87rVAfPY9+AwUeO6w+X95Nnxas5d9qDA/Su186TBQuGjd1GBaKjR8+DICwGGvJYeorVgpIASkgBaTAHgr8AOhqUuVzyRYOAAAAAElFTkSuQmCC"
        />
      </defs>
    </svg>
  </div>

  <div
    class="flex flex-col md:flex-row px-6 md:px-32 py-8 md:py-16 gap-6 md:gap-12 h-auto md:h-140"
  >
    <div class="w-full md:w-2/3 z-10">
      <img
        src="/home_1.png"
        class="border-4 rounded-2xl w-full h-64 md:h-full object-cover"
        alt="Background"
      />
    </div>
    <div
      class="bg-black w-full md:w-1/3 h-50 md:h-full rounded-2xl p-5 md:p-8 z-10"
    >
      <h1 class="text-rose-700 text-4xl md:text-5xl font-bold font-sans">
        GMC <br class="hidden md:inline" /> FITNESS
      </h1>
      <p class="text-white mt-4 text-2xl md:text-4xl font-sans">
        GYM MAJAPAHIT PURWOKERTO #NGEGYM NGASI KIAMAT
      </p>
    </div>
  </div>

  <div
    class="h-100 md:h-140 w-full px-6 md:px-32 py-16 flex relative items-center"
  >
    <img
      src="/home_2.png"
      class="absolute inset-0 w-full h-full object-cover z-0"
      alt=""
    />

    <div
      class="z-10 w-1/2 md:w-1/3 mr-auto rounded-2xl p-5 md:p-8 border-4 border-white bg-black h-full"
    >
      <h1 class="text-rose-700 text-2xl md:text-5xl font-bold font-sans">
        MODEL TIGA DIMENSI
      </h1>
      <p class="text-white mt-4 text-md md:text-4xl font-sans">
        VISUALISASIKAN FASILITAS GYM DENGAN LEBIH MUDAH DAN AKURAT
      </p>
    </div>
  </div>

  <div class="flex flex-col px-6 md:px-32 py-8 md:py-16 gap-6 md:gap-24 h-auto">
    <div class="flex flex-col md:flex-row gap-6 md:gap-12 h-auto md:h-100">
      <div class="w-full md:w-2/3 z-10">
        <img
          src="/home_3.png"
          class="border-4 rounded-2xl w-full h-64 md:h-full object-cover"
          alt="Background"
        />
      </div>
      <div
        class="bg-black w-full md:w-1/3 h-auto md:h-full rounded-2xl p-6 md:p-8 z-10"
      >
        <h1 class="text-rose-700 text-4xl md:text-5xl font-bold font-sans">
          ANIMASI <br class="hidden md:inline" /> GERAKAN
        </h1>
        <p class="text-white mt-4 text-2xl md:text-4xl font-sans">
          PELAJARI CARA MENGGUNAKAN ALAT-ALAT GYM TANPA RASA BINGUNG
        </p>
      </div>
    </div>

    <div class="flex z-10 justify-center px-6 md:px-32 pt-4 pb-16 h-auto">
      <div
        class="flex items-center justify-center bg-red-500 w-32 h-12 rounded-3xl cursor-pointer hover:scale-130 transition-all hover:bg-red-600 shadow-lg"
      >
        <a
          href="/model"
          class="text-white font-bold font-sans text-center select-none flex"
        >
          TRY NOW
        </a>
      </div>
    </div>
  </div>
  <Footer></Footer>
</div>
