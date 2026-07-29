<script lang="ts">
    import type { Snippet } from "svelte";

    interface Props {
        isOpen: boolean;
        title?: string;
        onClose: () => void;
        children?: Snippet;
    }

    let { isOpen, title = "Menu", onClose, children }: Props = $props();

    function handleKeydown(e: KeyboardEvent) {
        if (e.key === "Escape") onClose();
    }
</script>

<svelte:window onkeydown={handleKeydown} />

{#if isOpen}
    <div
        class="fixed inset-0 z-100 flex items-center justify-center bg-black/60 backdrop-blur-sm p-4"
        onclick={onClose}
    >
        <div
            class="relative mt-12 w-full max-w-4xl bg-zinc-50 border border-black/20 rounded-xl p-4 shadow-2xl flex flex-col"
            onclick={(e) => e.stopPropagation()}
        >
            <div
                class="modal-body overflow-y-auto custom-scroll"
                style="max-height: 60vh;"
            >
                {@render children?.()}
            </div>
        </div>
    </div>
{/if}

<style>
    /* Styling Scrollbar khusus untuk Chrome/Safari/Edge */
    .custom-scroll::-webkit-scrollbar {
        width: 6px;
    }

    .custom-scroll::-webkit-scrollbar-track {
        background: rgba(255, 255, 255, 0.05);
        border-radius: 10px;
    }

    .custom-scroll::-webkit-scrollbar-thumb {
        background: rgba(255, 255, 255, 0.2);
        border-radius: 10px;
        transition: background 0.2s;
    }

    .custom-scroll::-webkit-scrollbar-thumb:hover {
        background: rgba(255, 255, 255, 0.05);
    }

    /* Firefox */
    .custom-scroll {
        scrollbar-width: thin;
        scrollbar-color: rgba(255, 255, 255, 0.2) rgba(0, 0, 0, 0);
    }
</style>
