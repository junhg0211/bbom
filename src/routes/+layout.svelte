<script lang="ts">
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head>
	<style>
		@font-face {
			font-family: 'JZukinie';
			src: url('/JZukinie.otf') format('truetype');
		}

		@font-face {
			font-family: 'LaundryGothic';
			src: url('/LaundryGothic.otf') format('truetype');
		}

		body {
			margin: 0;
			padding: 0;
			font-family: 'Noto Sans KR', sans-serif;
			background-color: #f5f5f5;
		}
	</style>
	<link rel="icon" href={favicon} />
</svelte:head>

{@render children()}
