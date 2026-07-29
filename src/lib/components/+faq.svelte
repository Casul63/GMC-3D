<script lang="ts">
    let { q = "", a = "" } = $props();
</script>

<div
    class="flex flex-col gap-8 bg-black w-full h-auto rounded-2xl p-6 md:p-8 text-white z-10 shadow-xl"
>
    <div class="flex items-center gap-4">
        <p
            class="text-4xl md:text-5xl uppercase font-bold font-sans w-16 md:w-24 shrink-0 text-center"
        >
            Q&nbsp;&nbsp;:
        </p>
        <p
            class="text-xl md:text-2xl uppercase font-bold font-sans self-center text-justify w-full leading-snug"
        >
            {q}
        </p>
    </div>

    <div class="flex items-center gap-4">
        <p
            class="text-4xl md:text-5xl uppercase font-bold font-sans w-16 md:w-24 shrink-0 text-center"
        >
            A&nbsp;&nbsp;:
        </p>
        <p
            class="text-xl md:text-2xl uppercase font-bold font-sans self-center w-full text-justify leading-relaxed text-zinc-300"
        >
            {a}
        </p>
    </div>
</div>
