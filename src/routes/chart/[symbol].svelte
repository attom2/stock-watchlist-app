<script lang="ts">
	import Button, { Label } from '@smui/button';
	import { goto } from '$app/navigation';
	import { page } from '$app/stores';
	import Line from 'svelte-chartjs/src/Line.svelte';
	const urlPath = $page.url.toString().split('/');
	const symbol = urlPath[urlPath.length - 1];
	import { onMount } from 'svelte';
	const token = 'pk_3e49b40149a446638c161be217c45003';
	const getChartData = async () => {
		const response = await fetch(
			`https://cloud.iexapis.com/stable/stock/${symbol}/chart/3m?token=${token}`
		);
		const data = await response.json();
		goto(`/chart/${symbol}`);
		return data;
	};

	let symbolData = [];

	onMount(async () => {
		symbolData = await getChartData();
    symbolData = symbolData;
    stockLine.labels = symbolData.map((item) => item.label).slice(30);
    stockLine.datasets[0].data = symbolData.map((item) => item.close).slice(30);
    stockLine = stockLine;
	});

	let stockLine = {
		labels: [],
		datasets: [
			{
				label: symbol,
				fill: true,
				lineTension: 0.3,
				backgroundColor: 'rgba(225, 204,230, .3)',
				borderColor: 'rgb(205, 130, 158)',
				borderCapStyle: 'butt',
				borderDash: [],
				borderDashOffset: 0.0,
				borderJoinStyle: 'miter',
				pointBorderColor: 'rgb(205, 130,1 58)',
				pointBackgroundColor: 'rgb(255, 255, 255)',
				pointBorderWidth: 10,
				pointHoverRadius: 5,
				pointHoverBackgroundColor: 'rgb(0, 0, 0)',
				pointHoverBorderColor: 'rgba(220, 220, 220,1)',
				pointHoverBorderWidth: 2,
				pointRadius: 1,
				pointHitRadius: 10,
				data: [],
			}
		]
	};
</script>

<h1 class="container mx-auto px-4 h-full items-center mt-14">
	Last 30 days - {symbol}
</h1>

<div>
	<Button href="/watchlist" variant="raised" class="back-btn">Go Back to Watchlist</Button>
	{#if symbolData.length}
		<Line data={stockLine} options={{ responsive: true }} />
	{/if}
</div>

<style>
	* :global(.back-btn) {
		float: left;
	}
</style>
