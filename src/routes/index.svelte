<script lang="ts">
	import Button, { Label } from '@smui/button';
	import Textfield from '@smui/textfield';
	import { goto } from '$app/navigation';

	let name = '';

	const goToWatchlist = () => {
		if (name) {
			localStorage.setItem('currentUser', name);
			goto('/watchlist');
		}
	};
</script>

<div class="container mx-auto px-4 h-full items-center mt-14">
	<div class="flex content-center items-center justify-center my-auto">
		<div class="w-full lg:w-4/12 px-4">
			<div
				class="relative flex flex-col min-w-0 break-words w-full mb-6 shadow-lg rounded-lg bg-blueGray-200 border-0"
			>
				<div class="flex-auto px-4 lg:px-10 py-10 pt-0">
					<div class="text-blueGray-400 text-center mb-3 font-bold">
						<small>Enter your name</small>
					</div>
					<div>
						<div class="relative w-full mb-3">
							<Textfield class="w-full" label="Name" bind:value={name} />
						</div>

						<div class="text-center mt-6">
							<Button
								color="secondary"
								variant="raised"
								disabled={!name.length}
								on:click={goToWatchlist}
							>
								<Label>Continue</Label>
							</Button>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</div>
