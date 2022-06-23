<script>
	let shown = false;
	export function show() {
		shown = true;
	}
	export function hide() {
		shown = false;
	}
</script>

{#if shown}
	<div class="modal-wrapper">
		<div style="max-width: 500px;">
			<span class="modal-close" on:click={() => hide()}>X</span>
			<slot />
		</div>
	</div>
{/if}

<style>
	.modal-wrapper {
		background-color: rgba(184, 184, 184, 0.7);
		height: 100%;
		position: fixed;
		top: 0;
		right: 0;
		left: auto;
	}

	.modal-close {
		float: left;
		cursor: pointer;
		font-size: 14px;
	}
	.modal-close:hover {
		font-weight: bold;
	}
</style>
