<script lang="ts">
  import Modal from "$lib/components/+modal.svelte";
</script>

<div class="flex flex-col overflow-x-hidden pt-15">
  <div
    class="relative flex flex-col md:flex-row px-6 md:px-32 py-8 md:py-16 gap-6 md:gap-24 h-auto md:h-140"
  >
    <div class="absolute inset-0 z-0 overflow-hidden pointer-events-none">
      <svg
        width="100%"
        // height="100%"
        viewBox="0 0 1920 4020"
        preserveAspectRatio="none"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
        xmlns:xlink="http://www.w3.org/1999/xlink"
      >
        <rect
          width="1920"
          height="4169"
          fill="url(#pattern0_146_135)"
          fill-opacity="0.3"
        />
        <defs>
          <pattern
            id="pattern0_146_135"
            patternContentUnits="objectBoundingBox"
            width="0.025"
            height="0.0115136"
          >
            <use
              xlink:href="#image0_146_135"
              transform="scale(0.000520833 0.000239866)"
            />
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
    <div class="bg-black w-full md:w-1/3 h-64 md:h-full rounded-2xl p-8 z-10">
      <h1 class="text-rose-700 text-3xl md:text-5xl font-bold">
        GMC <br />FITNESS
      </h1>
      <p class="text-white mt-4 text-xl">
        VISUALISASIKAN FASILITAS GYM DENGAN LEBIH MUDAH DAN AKURAT DENGAN MODEL
        TIGA DIMENSI
      </p>
    </div>
    <div class="w-full md:w-2/3 z-10">
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
    class="relative flex flex-col px-6 md:px-32 py-8 md:py-16 gap-6 md:gap-24 h-auto"
  >
    <div class="absolute inset-0 z-0 overflow-hidden pointer-events-none">
      <svg
        width="100%"
        // height="100%"
        viewBox="0 0 1920 4020"
        preserveAspectRatio="none"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
        xmlns:xlink="http://www.w3.org/1999/xlink"
      >
        <rect
          width="1920"
          height="4169"
          fill="url(#pattern0_146_135)"
          fill-opacity="0.3"
        />
        <defs>
          <pattern
            id="pattern0_146_135"
            patternContentUnits="objectBoundingBox"
            width="0.025"
            height="0.0115136"
          >
            <use
              xlink:href="#image0_146_135"
              transform="scale(0.000520833 0.000239866)"
            />
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
    <div class="flex gap-6 h-100">
      <div class="bg-black w-full md:w-1/3 h-full rounded-2xl p-8 z-10">
        <h1 class="text-rose-700 text-3xl md:text-5xl font-bold">
          GMC <br />FITNESS
        </h1>
        <p class="text-white mt-4 text-xl">
          VISUALISASIKAN FASILITAS GYM DENGAN LEBIH MUDAH DAN AKURAT DENGAN
          MODEL TIGA DIMENSI
        </p>
      </div>
      <div class="w-full md:w-2/3 z-10">
        <img
          src="/home_3.png"
          class="border-4 rounded-2xl w-full h-64 md:h-full object-cover"
          alt="Background"
        />
      </div>
    </div>
    <div
      class="flex z-10 justify-center px-6 md:px-32 pt-8 pb-16 gap-6 md:gap-24 h-auto"
    >
      <div class="bg-red-500 w-32 h-12 my-auto rounded-3xl"></div>
    </div>
  </div>

  <div
    class="flex flex-col md:flex-row justify-between px-6 md:px-32 py-32 bg-black gap-16"
  >
    <div class="w-full md:w-1/3 h-64 md:h-auto min-h-75">
      <iframe
        src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3956.3906723301625!2d109.23093057482619!3d-7.421943873105552!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x2e655e618f9c30c3%3A0xab76f466e70e8f40!2sGMC%20Fitnes!5e0!3m2!1sid!2ssg!4v1780362936096!5m2!1sid!2ssg"
        title="GMC Fitness Maps"
        class="w-full h-full border-0 rounded-2xl"
        allowfullscreen={true}
        loading="lazy"
        referrerpolicy="no-referrer-when-downgrade"
      ></iframe>
    </div>

    <div
      class="flex w-full md:w-2/3 justify-between flex-col md:flex-row gap-8"
    >
      <div class="w-full md:w-1/2 text-white">
        <h1 class="text-4xl font-bold">HUBUNGI KAMI</h1>
        <p class="mt-4 text-2xl font-bold">08XX XXXX XXXX</p>
        <p class="mt-4 text-2xl font-bold">@gmcfitness</p>
      </div>
      <div class="w-full md:w-1/2">
        <img
          src="/logo.png"
          alt=""
          class="h-full object-cover rounded-2xl w-full md:h-full"
        />
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
