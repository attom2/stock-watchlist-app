<script lang="ts">
	import SymbolSearch from '$lib/SymbolSearch.svelte';
	import { onMount } from 'svelte';
	import Button, { Label } from '@smui/button';
	import List, { Item, Text, PrimaryText } from '@smui/list';
	import Textfield from '@smui/textfield';
	const getCurrentQuote = async (symbol: string) => {
		// const response = await fetch(
		// 	`https://cloud.iexapis.com/stable/stock/${symbol}/quote?token=${token}`
		// );
		// const data = await response.json();
		const data = {
			avgTotalVolume: 91065668,
			calculationPrice: 'close',
			change: 1.5,
			changePercent: 0.01153,
			close: null,
			closeSource: 'official',
			closeTime: null,
			companyName: 'Apple Inc',
			currency: 'USD',
			delayedPrice: null,
			delayedPriceTime: null,
			extendedChange: null,
			extendedChangePercent: null,
			extendedPrice: null,
			extendedPriceTime: null,
			high: null,
			highSource: 'Close',
			highTime: 1655496001145,
			iexAskPrice: null,
			iexAskSize: null,
			iexBidPrice: null,
			iexBidSize: null,
			iexClose: 131.48,
			iexCloseTime: 1655495999384,
			iexLastUpdated: null,
			iexMarketPercent: null,
			iexOpen: 130.07,
			iexOpenTime: 1655472600114,
			iexRealtimePrice: null,
			iexRealtimeSize: null,
			iexVolume: null,
			lastTradeTime: 1655495999601,
			latestPrice: 131.56,
			latestSource: 'Close',
			latestTime: 'June 17, 2022',
			latestUpdate: 1655496000000,
			latestVolume: null,
			low: null,
			lowSource: 'Close',
			lowTime: 1655496000000,
			marketCap: 2129322412360,
			oddLotDelayedPrice: null,
			oddLotDelayedPriceTime: null,
			open: null,
			openTime: null,
			openSource: 'official',
			peRatio: 21.39,
			previousClose: 130.06,
			previousVolume: 107961508,
			primaryExchange: 'NASDAQ',
			symbol: 'AAPL',
			volume: null,
			week52High: 182.44,
			week52Low: 128.48,
			ytdChange: -0.24554531853545128,
			isUSMarketOpen: false
		};
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
		allWatchlists[currentUser].push({ name: newWatchlistName, data: [] });
		await saveUserWatchlist();
		watchlists = watchlists;
	};
	let currentWatchlistIndex = 0;
	const token = 'pk_3e49b40149a446638c161be217c45003';
	let newWatchlistName = '';
</script>

<div class="flex">
	<div class="flex justify-between" style="min-width: 200px">
		<div>
			<List singleSelection bind:selectedIndex={currentWatchlistIndex} class="list">
				{#each watchlists as watchlist, i}
					<Item
						on:click={() => (currentWatchlistIndex = i)}
						selected={currentWatchlistIndex === i}
						class="flex"
					>
						<Text class="py-2">
							<PrimaryText>{watchlist.name}</PrimaryText>
						</Text>
					</Item>
				{/each}
			</List>
			<div class="flex flex-col mt-3">
				<Button variant="raised" on:click={addNewWatchList} disabled={!newWatchlistName.length}>
					Add new watchlist
				</Button>
				<Textfield
					variant="filled"
					class="full-width"
					label="New watchlist name"
					bind:value={newWatchlistName}
				/>
			</div>
		</div>
	</div>
	<!-- table styling found on https://flowbite.com/docs/components/tables/  -->
	<div class="flex justify-center">
		<table class=" text-sm text-left text-gray-500 dark:text-gray-400">
			<thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
				<tr>
					<th scope="col" class="p-4" />
					<th scope="col" class="px-6 py-3"> Stock Symbol </th>
					<th scope="col" class="px-6 py-3"> Description </th>
					<th scope="col" class="px-6 py-3 text-center"> Bid Price </th>
					<th scope="col" class="px-6 py-3 text-center"> Ask Price </th>
					<th scope="col" class="px-6 py-3 text-center">Last Price </th>
					<th scope="col" class="px-6 py-3 text-center">Delete </th>
				</tr>
			</thead>
			<tbody>
				{#if watchlists[currentWatchlistIndex]?.data}
					{#each watchlists[currentWatchlistIndex]?.data as item}
						<tr
							class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600"
						>
							<td class="w-4 p-4">
								<Button href="/chart/{item?.symbol}">
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
							<td class="px-6 py-4 text-center"> {item?.iexBidPrice ?? '-'} </td>
							<td class="px-6 py-4 text-center"> {item?.iexAskPrice ?? '-'} </td>
							<td class="px-6 py-4 text-center"> {item?.latestPrice ?? '-'} </td>
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
					class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600"
				>
					<td colspan="12">
						<div class="flex justify-center">
							<Button style="width: 100%; min-height: 40px; height: 100%;">
								<Label>Add a Symbol to Watchlist</Label>
							</Button>
						</div>
					</td></tr
				>
			</tbody>
		</table>
	</div>
	<div class="flex">
		<SymbolSearch on:assignToWatchlist={saveWatchlist} index={currentWatchlistIndex} />
	</div>
</div>

<style>
	table {
		width: 1000px;
		height: 200px;
		overflow-y: scroll;
	}

	* :global(.list) {
		max-height: 400px;
		overflow-y: scroll;
		background: white;
		padding: 0;
	}
	td {
		max-width: 100px;
	}
</style>
