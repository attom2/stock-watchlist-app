<script lang="ts">
import SymbolSearch from '$lib/SymbolSearch.svelte';
import { onMount } from 'svelte';

	let watchlistSynbols = ['AAPL', 'MSFT', 'SPY'];
	const getCurrentQuote = async (symbol: string) => {
		const token = 'pk_90b7dfff38d043d483fedc63be2de505';
		const response = await fetch(
			`https://cloud.iexapis.com/stable/stock/${symbol}/quote?token=${token}`
		);
		const data = await response.json();
		return data;
	};

	const getAllSymbolData = async () => {
		let items = [];
		for (let i = 0; i < watchlistSynbols.length; i++) {
			const symbolData = await getCurrentQuote(watchlistSynbols[i]);
			items.push(symbolData);
		}
		watchlistItems = items;
	};
	let watchlistItems: any[] = [];

	onMount(async () => {
		await getAllSymbolData();
	});

	const deleteSymbol = async (symbolData: any) => {
		const index = watchlistSynbols.indexOf(symbolData.symbol);
		if (index > -1) {
			watchlistSynbols.splice(index, 1);
		}
		await getAllSymbolData();
	};
</script>

<!-- table styling found on https://flowbite.com/docs/components/tables/  -->
<div class="relative overflow-x-auto shadow-md sm:rounded-lg">
	<div class="p-4">
		<label for="table-search" class="sr-only">Search</label>
		<div class="relative mt-1">
			<div class="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none">
				<svg
					class="w-5 h-5 text-gray-500 dark:text-gray-400"
					fill="currentColor"
					viewBox="0 0 20 20"
					xmlns="http://www.w3.org/2000/svg"
					><path
						fill-rule="evenodd"
						d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z"
						clip-rule="evenodd"
					/></svg
				>
			</div>
			<input
				type="text"
				id="table-search"
				class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-80 pl-10 p-2.5  dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
				placeholder="Search for items"
			/>
		</div>
	</div>
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
			{#if watchlistItems}
				{#each watchlistItems as item}
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
							{item.symbol}
						</th>
						<td class="px-6 py-4"> {item.iexBidPrice} </td>
						<td class="px-6 py-4"> {item.iexBidPrice} </td>
						<td class="px-6 py-4"> {item.iexAskPrice} </td>
						<td class="px-6 py-4 text-right"> {item.latestPrice} </td>
						<td class="px-6 py-4 text-center">
							<button on:click={async () => await deleteSymbol(item)}> X </button></td
						>
					</tr>
				{/each}
			{/if}
			<tr
				class="bg-white border-b light:bg-gray-800 light:border-gray-700 hover:bg-gray-50 light:hover:bg-gray-600"
			>
				<td class="p-4" colspan="12">
					<div class="flex items-center">
						<SymbolSearch />
					</div>
				</td>
			</tr>
		</tbody>
	</table>
</div>
