<script lang="ts">
	import SymbolSearch from '$lib/SymbolSearch.svelte';
	import { onDestroy, onMount } from 'svelte';
	import Button, { Label } from '@smui/button';
	import List, { Item, Text, PrimaryText, Meta } from '@smui/list';
	import Textfield from '@smui/textfield';
	import Modal from './Modal.svelte';
	let symbolModal;
	let watchlistModal;
	const token = 'pk_3e49b40149a446638c161be217c45003';
	const myInterval = setInterval(myTimer, 5000);
	let allWatchlists =
		typeof window !== 'undefined' ? JSON.parse(localStorage.getItem('usersWatchlist')) : {};
	const currentUser = typeof window !== 'undefined' ? localStorage.getItem('currentUser') : '';

	let watchlists = allWatchlists[currentUser] || [];
	let currentWatchlistIndex = 0;
	let newWatchlistName = '';
	let currentWatchlist = null;

	const getCurrentQuote = async (symbol: string) => {
		const response = await fetch(
			`https://cloud.iexapis.com/stable/stock/${symbol}/quote?token=${token}`
		);
		const data = await response.json();
		return data;
	};

	const getAllSymbolData = async (watchlist) => {
		let items = [];
		for (let i = 0; i < watchlist?.data.length; i++) {
			const symbolData = await getCurrentQuote(watchlist.data[i].symbol);
			items.push(symbolData);
			watchlist.data[i] = { ...watchlist.data[i], ...symbolData };
		}
	};

	async function myTimer() {
		await getAllSymbolData(watchlists[currentWatchlistIndex]);
	}

	function myStopFunction() {
		clearInterval(myInterval);
	}

	onMount(async () => {
		currentWatchlist = allWatchlists[currentUser][0];
	});

	onDestroy(() => {
		myStopFunction();
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
		currentWatchlist = currentWatchlist;
	};

	const saveUserWatchlist = async () => {
		localStorage.setItem('usersWatchlist', JSON.stringify(allWatchlists));
		for (let i = 0; i < watchlists.length; i++) {
			await getAllSymbolData(currentWatchlist);
		}
	};

	const saveWatchlist = async (event) => {
		currentWatchlist?.data.push(event.detail.item);
		await saveUserWatchlist();
		currentWatchlist = currentWatchlist;
		symbolModal.hide();
	};
	const addNewWatchList = async () => {
		watchlistModal.hide();
		allWatchlists[currentUser].push({
			name: newWatchlistName,
			data: [],
			id: Date.now()
		});
		await saveUserWatchlist();
		watchlists = watchlists;
		newWatchlistName = '';
	};

	const resetWatchlist = () => {
		if (allWatchlists && allWatchlists[currentUser]) {
			currentWatchlist = allWatchlists[currentUser][0];
		}
		currentWatchlist = currentWatchlist;
	};

	const deleteWatchlist = async () => {
		if (allWatchlists[currentUser].length === 1) {
			alert('Cannot delete last watchlist');
			return;
		}
		const index = allWatchlists[currentUser].findIndex((list) => list.id === currentWatchlist.id);
		allWatchlists[currentUser].splice(index, 1);
		allWatchlists = allWatchlists;
		resetWatchlist();
		await saveUserWatchlist();
	};
</script>

{#if currentWatchlist}
	<div>
		<div>
			<h1>
				{currentWatchlist.name}
				<Button color="secondary" on:click={deleteWatchlist}>
					<span class="material-icons">delete</span>
				</Button>
			</h1>
		</div>
		<div class="flex">
			<!-- table styling found on https://flowbite.com/docs/components/tables/  -->
			<div class="flex flex-col" style="min-width: 200px">
				<List singleSelection bind:selectedIndex={currentWatchlistIndex} class="list">
					<h4 class="text-center bg-cyan-700 text-white">
						{currentUser}'s Watchlists
					</h4>
					{#each watchlists as watchlist, i}
						<Item
							on:click={() =>
								(currentWatchlist = allWatchlists[currentUser].find(
									(list) => list.id == watchlist.id
								))}
							selected={currentWatchlist?.id === watchlist?.id}
						>
							<Text class="py-2">
								<PrimaryText>{watchlist.name}</PrimaryText>
							</Text>
						</Item>
					{/each}
				</List>
				<Button variant="raised" on:click={() => watchlistModal.show()}>Add new watchlist</Button>
			</div>
			<table class=" text-sm text-left text-gray-500 dark:text-gray-400">
				<thead
					class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400"
				>
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
					{#each currentWatchlist?.data as item}
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
								<button on:click={async () => await deleteSymbol(item, currentWatchlist)}>
									<span class="material-icons">delete</span>
								</button>
							</td>
						</tr>
					{/each}
					<tr
						class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600"
					>
						<td colspan="12">
							<div class="flex justify-center">
								<Button
									on:click={() => symbolModal.show()}
									variant="raised"
									color="primary"
									style="min-height: 40px; height: 100%;"
								>
									<Label>Add a Symbol to Watchlist</Label>
								</Button>
							</div>
						</td>
					</tr>
				</tbody>
			</table>
		</div>
	</div>
	<Modal bind:this={symbolModal}>
		<SymbolSearch on:assignToWatchlist={saveWatchlist} index={currentWatchlistIndex} />
	</Modal>
	<Modal bind:this={watchlistModal}>
		<div class="flex flex-col">
			<h1>Add new watchlist</h1>

			<Textfield
				variant="filled"
				class="full-width"
				label="New watchlist name"
				bind:value={newWatchlistName}
			/>
			<Button variant="raised" on:click={addNewWatchList} disabled={!newWatchlistName.length}>
				Add
			</Button>
		</div>
	</Modal>
{/if}

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
		width: 100%;
	}
	td {
		max-width: 100px;
	}
</style>
