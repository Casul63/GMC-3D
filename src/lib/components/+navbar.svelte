<script lang="ts">
    import { page } from "$app/state";
</script>

<nav class="fixed z-110 w-full bg-black flex justify-between px-3 md:px-6 h-15">
    <div class="text-amber-50 self-center font-bold tracking-tighter uppercase">
        Logo/Test
    </div>

    <div class="mr-2 my-2 flex gap-3">
        <a
            class="self-center px-6 py-1 rounded-md bg-amber-50 text-black font-medium transition-hover hover:bg-amber-100"
            href="/faq"
        >
            FAQ
        </a>

        <a
            class="self-center px-6 py-1 rounded-md bg-red-800 text-white font-medium hover:bg-red-700 transition-colors"
            href={page.url.pathname === "/" ? "/model" : "/"}
            data-sveltekit-noscroll
        >
            {page.url.pathname === "/" ? "MODEL" : "HOME"}
        </a>
    </div>
</nav>
