<script>
    import favicon from "$lib/assets/AeroLogo.svg";

    let { children } = $props();
</script>

<svelte:head>
    <link rel="icon" href={favicon} />
</svelte:head>

{@render children?.()}

<style>
    :root {
        --scarlet: #d10000;
        --white: #ebebeb;
		--off-white: #acacac;
        --light-gray: #2e2e2d;
        --dark-gray: #242423;
        --black: #000000;
		--light-blue: #72bfd4
    }

    :global(body, html, #svelte) {
        margin: 0;
        padding: 0;
        height: 100%;
        background: var(--dark-gray);
        font-family: verdana;
    }
</style>
