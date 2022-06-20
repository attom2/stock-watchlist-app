<script lang="ts">
	import SymbolSearch from '$lib/SymbolSearch.svelte';
	import { onMount } from 'svelte';

	const getCurrentQuote = async (symbol: string) => {
		const token = 'pk_90b7dfff38d043d483fedc63be2de505';
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
	let watchlistItems: any[] = [];

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
</script>

<button on:click={addNewWatchList}>Add new watchlist</button>
{#each watchlists as watchlist, i}
	<!-- table styling found on https://flowbite.com/docs/components/tables/  -->
	<div class="relative overflow-x-auto shadow-md sm:rounded-lg">
		<div class="p-4">{watchlist.name}</div>
		<table class="w-full text-sm text-left text-gray-500 dark:text-gray-400">
			<thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
				<tr>
					<th scope="col" class="p-4">
						<div class="flex items-center">
							<input
								id="checkbox-all-search"
								type="checkbox"
								class="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
							/>
							<label for="checkbox-all-search" class="sr-only">checkbox</label>
						</div>
					</th>
					<th scope="col" class="px-6 py-3"> Stock Symbol </th>
					<th scope="col" class="px-6 py-3"> Description </th>
					<th scope="col" class="px-6 py-3"> Bid Price </th>
					<th scope="col" class="px-6 py-3"> Ask Price </th>
					<th scope="col" class="px-6 py-3">Last Price </th>
					<th scope="col" class="px-6 py-3">Delete </th>
				</tr>
			</thead>
			<tbody>
				{#if watchlist.data}
					{#each watchlist.data as item}
						<tr
							class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600"
						>
							<td class="w-4 p-4">
								<div class="flex items-center">
									<input
										id="checkbox-table-search-1"
										type="checkbox"
										class="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
									/>
									<label for="checkbox-table-search-1" class="sr-only">checkbox</label>
								</div>
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
								<button on:click={async () => await deleteSymbol(item, watchlist)}> X </button></td
							>
						</tr>
					{/each}
				{/if}
				<tr
					class="bg-white border-b light:bg-gray-800 light:border-gray-700 hover:bg-gray-50 light:hover:bg-gray-600"
				>
					<td class="p-4" colspan="12">
						<div class="flex items-center">
							<SymbolSearch on:assignToWatchlist={saveWatchlist} index={i} />
						</div>
					</td>
				</tr>
			</tbody>
		</table>
	</div>
{/each}
