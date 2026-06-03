<script lang="ts">
  import Modal from "$lib/components/+modal.svelte";
  import FAQ from "$lib/components/+faq.svelte";
</script>

<div
  class="relative flex flex-col overflow-x-hidden mt-15 h-auto p-6 md:p-32 gap-16"
>
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

  <FAQ
    q="Berapa berat beban yang harus Saya pakai?"
    a="Seberat yang anda bisa angkat selagi melakukan gerakan dengan benar. Daripada fokus mengangkat yang berat, lebih baik fokus memperbaiki gerakan."
  />

  <FAQ
    q="Apa saja etika di gym yang perlu dipatuhi?"
    a="Bersihkan alat dengan disinfektan setelah dipakai. Jangan memborong banyak alat sekaligus, jika ramai bergantianlah menggunakan alat dengan orang lain. Selalu letakkan kembali beban ke tempatnya."
  />

  <FAQ
    q="Latihan apa saja yang harus Saya lakukan di gym?"
    a="Untuk pemula, fokus pada gerakan dasar (compound movements) seperti Squat, Bench Press, dan Lat Pulldown. Jadwalkan latihan 3-4 kali seminggu."
  />
</div>

<style>
  :global(html, body) {
    overflow-y: auto !important;
    height: auto !important;
  }
</style>
