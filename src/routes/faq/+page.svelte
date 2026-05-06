<script lang="ts">
  import Modal from "$lib/components/+modal.svelte";
  import Navbar from "$lib/components/+navbar.svelte";
</script>

<Navbar></Navbar>
<div class="flex flex-col overflow-x-hidden mt-15 h-auto p-32">
  <div
    class="flex flex-col gap-4 bg-black w-full h-64 md:h-full rounded-2xl p-8 text-white"
  >
    <div class="flex gap-4">
      <p class="text-5xl font-bold w-24">Q :</p>
      <p class="text-xl self-center w-full">
        Berapa berat beban yang harus Saya pakai?
      </p>
    </div>
    <div class="flex gap-4">
      <p class="text-5xl font-bold w-24">A :</p>
      <p class="text-xl items-center w-full">
        Seberat yang anda bisa angkat selagi melakukan gerakan dengan benar.
        Daripada fokus mengangkat yang berat, lebih baik fokus memperbaiki
        gerakan.
      </p>
    </div>
  </div>
</div>

<style>
  :global(html, body) {
    overflow-y: auto !important;
    height: auto !important;
  }
</style>
