<script lang="ts">
	import { goto } from '$app/navigation';
	import Button, { Label } from '@smui/button';
	import WatchlistTable from './watchlistTable.svelte';
	const getCurrentQuote = async (symbol: string) => {
		const token = 'pk_90b7dfff38d043d483fedc63be2de505';
		const response = await fetch(
			`https://cloud.iexapis.com/stable/stock/${symbol}/quote?token=${token}`
		);
		const data = await response.json();
	};
	const currentUser = typeof window !== 'undefined' ? localStorage.getItem('currentUser') : '';
	const logout = () => {
		localStorage.removeItem('currentUser');
		goto('/');
	};
</script>

<svelte:head>
  <title>Watchlists</title>
</svelte:head>

<div class="content">
	<Button on:click={logout}>Log out</Button>
	<h1>Watchlists - {currentUser}</h1>
	<WatchlistTable />
</div>

<style>
	.content {
		width: 100%;
		max-width: var(--column-width);
		margin: var(--column-margin-top) auto 0 auto;
	}
</style>
