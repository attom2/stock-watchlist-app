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

</style>
