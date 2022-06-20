<script lang="ts">
	import SymbolSearch from '$lib/SymbolSearch.svelte';
	import { onMount } from 'svelte';
	import Button, { Group, Label, Icon } from '@smui/button';
	import List, { Item, Graphic, Meta, Text, PrimaryText, SecondaryText } from '@smui/list';
	import mockData from './mockData';

	const getCurrentQuote = async (symbol: string) => {
		const response = await fetch(
			`https://cloud.iexapis.com/stable/stock/${symbol}/quote?token=${token}`
		);
		const data = await response.json();
		return data;
	};

	const getAllSymbolData = async (watchlist) => {
		let items = [];
		for (let i = 0; i < watchlist.data.length; i++) {
			const symbolData = await getCurrentQuote(watchlist.data[i].symbol);
			items.push(symbolData);
			watchlist.data[i] = { ...watchlist.data[i], ...symbolData };
		}
	};

	onMount(async () => {
		await getAllSymbolData(watchlists[0]);
		watchlists = watchlists;
	});

	const deleteSymbol = async (symbolData: any, watchlist) => {
		const index = watchlist.data.findIndex(
			(watchlistitem) => watchlistitem.symbol === symbolData.symbol
		);
		if (index > -1) {
			watchlist.data.splice(index, 1);
		}
		await saveUserWatchlist();
		await getAllSymbolData(watchlist);
		watchlists = watchlists;
	};
	let allWatchlists =
		typeof window !== 'undefined' ? JSON.parse(localStorage.getItem('usersWatchlist')) : {};
	const currentUser = typeof window !== 'undefined' ? localStorage.getItem('currentUser') : '';

	let watchlists = allWatchlists[currentUser] || [];
	const saveUserWatchlist = async () => {
		localStorage.setItem('usersWatchlist', JSON.stringify(allWatchlists));
		for (let i = 0; i < watchlists.length; i++) {
			await getAllSymbolData(watchlists[i]);
		}
	};

	const saveWatchlist = async (event) => {
		allWatchlists[currentUser][event.detail.index].data.push(event.detail.item);
		await saveUserWatchlist();
		watchlists = watchlists;
	};
	const addNewWatchList = async () => {
		allWatchlists[currentUser].push({ name: 'test', data: [] });
		await saveUserWatchlist();
		watchlists = watchlists;
	};
	let currentWatchlistIndex = 0;
	const token = 'pk_3e49b40149a446638c161be217c45003';

	const getChartData = async (item) => {
		// const response = await fetch(
		// 	`https://cloud.iexapis.com/stable/stock/${item.symbol}/chart/3m?token=${token}`,
		// );
		// const data = await response.json();
		const data = mockData;
		console.log(data);
		return data;

	};
</script>

<List twoLine avatarList singleSelection bind:selectedIndex={currentWatchlistIndex}>
	{#each watchlists as watchlist, i}
		<Item on:click={() => (currentWatchlistIndex = i)} selected={currentWatchlistIndex === i}>
			<Text>
				<PrimaryText>{watchlist.name}</PrimaryText>
			</Text>
		</Item>
	{/each}
</List>
<button on:click={addNewWatchList}>Add new watchlist</button>
<!-- table styling found on https://flowbite.com/docs/components/tables/  -->
<div class="relative overflow-x-auto shadow-md sm:rounded-lg">
	<table class="w-full text-sm text-left text-gray-500 dark:text-gray-400">
		<thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
			<tr>
				<th scope="col" class="p-4" />
				<th scope="col" class="px-6 py-3"> Stock Symbol </th>
				<th scope="col" class="px-6 py-3"> Description </th>
				<th scope="col" class="px-6 py-3"> Bid Price </th>
				<th scope="col" class="px-6 py-3"> Ask Price </th>
				<th scope="col" class="px-6 py-3">Last Price </th>
				<th scope="col" class="px-6 py-3">Delete </th>
			</tr>
		</thead>
		<tbody>
			{#if watchlists[currentWatchlistIndex]?.data}
				{#each watchlists[currentWatchlistIndex]?.data as item}
					<tr
						class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600"
					>
						<td class="w-4 p-4">
							<Button on:click={getChartData(item)}>
								<Label>View Chart</Label>
							</Button>
						</td>
						<th
							scope="row"
							class="px-6 py-4 font-medium text-gray-900 dark:text-white whitespace-nowrap"
						>
							{item?.symbol}
						</th>
						<td class="px-6 py-4"> {item?.description ?? '-'} </td>
						<td class="px-6 py-4"> {item?.iexBidPrice ?? '-'} </td>
						<td class="px-6 py-4"> {item?.iexAskPrice ?? '-'} </td>
						<td class="px-6 py-4 text-right"> {item?.latestPrice ?? '-'} </td>
						<td class="px-6 py-4 text-center">
							<button
								on:click={async () => await deleteSymbol(item, watchlists[currentWatchlistIndex])}
							>
								<span class="material-icons">delete</span>
							</button>
						</td>
					</tr>
				{/each}
			{/if}
			<tr
				class="bg-white border-b light:bg-gray-800 light:border-gray-700 hover:bg-gray-50 light:hover:bg-gray-600"
			>
				<td class="p-4" colspan="12">
					<div class="flex items-center">
						<SymbolSearch on:assignToWatchlist={saveWatchlist} index={currentWatchlistIndex} />
					</div>
				</td>
			</tr>
		</tbody>
	</table>
</div>
