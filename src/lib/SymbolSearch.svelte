<script lang="ts">
	import LinearProgress from '@smui/linear-progress';
	import Textfield from '@smui/textfield';
	import HelperText from '@smui/textfield/helper-text';
	import List, { Item, Meta, Text, PrimaryText, SecondaryText } from '@smui/list';

	let previewSearch: Symbol[] = [];
	let search: string = '';
	type Symbol = {
		symbol: string;
		options: Boolean;
		description: string;
	};

	const searchForSymbols = async () => {
		if (!search.length) {
			previewSearch = [];
			return;
		}
		try {
			const response = await fetch(`https://vast.tastyworks.com/symbols/search/${search}`);
			const formattedResp = await response.json();
			previewSearch = formattedResp?.data?.items;
		} catch {
			previewSearch = [];
		}
	};

	const symbolClick = (item: Symbol) => {
		selection = item.symbol;
		previewSearch = [];
	};

	let selection = '';
</script>

<div class="counter" />
<div class="search">
	<Textfield class="full-width" label="Symbol" bind:value={search} on:input={searchForSymbols}>
		<HelperText persistent slot="helper">EX: AAPL, GOOG</HelperText>
	</Textfield>
	<List class="list mt-1" twoLine singleSelection dense>
		{#each previewSearch as item}
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
	</List>
</div>

<style>
	.counter {
		display: flex;
		border-top: 1px solid rgba(0, 0, 0, 0.1);
		border-bottom: 1px solid rgba(0, 0, 0, 0.1);
		margin: 1rem 0;
	}

	.search {
		width: 600px;
	}

	* :global(.list) {
		max-height: 400px;
		overflow-y: scroll;
	}

	* :global(.full-width) {
		width: 100%;
	}
</style>
