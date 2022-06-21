<script lang="ts">
	import '../app.css';
	import { goto } from '$app/navigation';
	import { onMount } from 'svelte';

	onMount(() => {
		const currentUser = typeof window !== 'undefined' ? localStorage.getItem('currentUser') : '';
		if (!currentUser) {
			goto('/');
		}
	})
</script>

<main>
	<slot />
</main>

<footer>
	<!-- <a href="https://iexcloud.io">Data provided by IEX Cloud</a> -->
</footer>

<style>
	main {
		flex: 1;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		padding: 1rem;
		width: 100%;
		max-width: 1024px;
		margin: 0 auto;
		box-sizing: border-box;
	}

	footer {
		position: absolute;
		bottom: 0;
		width: 100%;
		height: 2.5rem;
		text-align: center;
	}

	footer a {
		font-weight: bold;
	}

	@media (min-width: 480px) {
		footer {
			padding: 40px 0;
		}
	}
</style>
