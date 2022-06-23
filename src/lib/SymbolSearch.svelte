<script lang="ts">
	import Textfield from '@smui/textfield';
	import HelperText from '@smui/textfield/helper-text';
	import List, { Item, Meta, Text, PrimaryText, SecondaryText } from '@smui/list';
	import { createEventDispatcher } from 'svelte';
	export let index;
	const dispatch = createEventDispatcher();

	function assignToWatchlist(item) {
		dispatch('assignToWatchlist', {
			item,
			index
		});
	}
	let searchPreviews: SymbolInfo[] = [];
	let search: string = '';
	type SymbolInfo = {
		symbol: string;
		options: Boolean;
		description: string;
	};

	const searchForSymbols = async () => {
		if (!search.length) {
			searchPreviews = [];
			return;
		}
		try {
			const response = await fetch(`https://vast.tastyworks.com/symbols/search/${search}`);
			const formattedResp = await response.json();
			searchPreviews = formattedResp?.data?.items;
		} catch {
			searchPreviews = [];
		}
	};

	const symbolClick = async (item: SymbolInfo) => {
		selection = item.symbol;
		searchPreviews = [];
		search = '';
		assignToWatchlist(item);
	};

	let selection = '';

	function debounce(func, timeout = 10) {
		let timer;
		return (...args) => {
			clearTimeout(timer);
			timer = setTimeout(() => {
				func.apply(this, args);
			}, timeout);
		};
	}
	const processChange = debounce(() => searchForSymbols());
</script>

<div class="search">
	<h1>Search for symbol</h1>
	<Textfield
		variant="filled"
		class="full-width"
		label="Symbol"
		bind:value={search}
		on:input={processChange}
	>
		<HelperText persistent slot="helper">EX: AAPL, GOOG</HelperText>
	</Textfield>
	<List class="list mt-1" twoLine singleSelection dense>
		{#each searchPreviews as item}
			<Item on:click={() => symbolClick(item)} selected={selection === item.symbol}>
				<Text>
					<PrimaryText>{item.symbol}</PrimaryText>
					<SecondaryText>{item.description}</SecondaryText>
				</Text>
				{#if item.options}
					<Meta>Options</Meta>
				{/if}
			</Item>
		{/each}
		{#if search.length > 0 && !searchPreviews.length}
			<Item>
				<Text>
					<PrimaryText>No symbols found</PrimaryText>
				</Text>
			</Item>
		{/if}
	</List>
</div>

<style>
	.search {
		width: 500px;
		padding: 20px;
	}

	* :global(.list) {
		max-height: 400px;
		width: 500px;
		overflow-y: scroll;
		background: white;
		position: absolute;
	}

	* :global(.full-width) {
		width: 100%;
	}

</style>
