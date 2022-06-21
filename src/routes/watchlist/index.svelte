<script lang="ts">
	import { goto } from '$app/navigation';
	import Button, { Label } from '@smui/button';
	import WatchlistTable from './watchlistTable.svelte';
	const currentUser = typeof window !== 'undefined' ? localStorage.getItem('currentUser') : '';
	let userWatchlist = [];

	if (typeof window !== 'undefined' && currentUser) {
		userWatchlist = JSON.parse(localStorage.getItem('usersWatchlist'));
		const defaultWatchlist = {};
		const defaultSymbols = [
			{
				name: `${currentUser}'s first list`,
				data: [
					{ symbol: 'AAPL', description: 'Apple Inc. - Common Stock' },
					{ symbol: 'MSFT', description: 'Microsoft Corporation - Common Stock' },
					{ symbol: 'SPY', description: 'SPDR S&P 500' }
				]
			}
		];
		defaultWatchlist[currentUser] = defaultSymbols;
		if (!userWatchlist) {
			localStorage.setItem('usersWatchlist', JSON.stringify(defaultWatchlist));
		}
		userWatchlist = JSON.parse(localStorage.getItem('usersWatchlist'));
		if(!userWatchlist[currentUser]) {
			userWatchlist[currentUser] = defaultSymbols;
			localStorage.setItem('usersWatchlist', JSON.stringify(userWatchlist));
		}
	}
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
		width: 1600px;
	}
</style>
