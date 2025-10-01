<script>
	import favicon from '$lib/assets/AeroLogo.svg';

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

{@render children?.()}

<style>
	:global(body) {
		font-family: Verdana, Geneva, sans-serif;
	}
</style>