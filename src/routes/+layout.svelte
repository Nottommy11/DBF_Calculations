<script>
	import favicon from '$lib/assets/favicon.svg';
	export const prerender = true;

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