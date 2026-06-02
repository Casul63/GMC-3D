<script lang="ts">
	import "./layout.css";
	import favicon from "$lib/assets/favicon.ico";
	import Navbar from "$lib/components/+navbar.svelte";
	import { page } from "$app/state";
	let { children } = $props();
</script>

<svelte:head><link rel="icon" href={favicon} /></svelte:head>

{#if page.url.pathname !== "/animations"}
	<Navbar></Navbar>
{/if}

{@render children()}
