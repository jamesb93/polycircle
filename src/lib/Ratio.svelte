<script>
	export let title = 'ratio'
	export let denom = 1;
	export let divis = 1;
	export let color = '#000000';
</script>

<div class="container">
	<input type=number min=0.01 bind:value={denom}>
	<div>/</div>
	<input type=number min=0.01 bind:value={divis}>
	<div style:color={color}>{title}</div>
</div>

<style>
	.container {
		display: flex;
		flex-direction: row;
		gap: 1em;
		align-items: center;
	}

	input {
		outline: 0;
		border: 0;
		max-width: 2ch;
	}


	/* Chrome, Safari, Edge, Opera */
	input::-webkit-outer-spin-button,
	input::-webkit-inner-spin-button {
	-webkit-appearance: none;
	margin: 0;
	}

	/* Firefox */
	input[type=number] {
	-moz-appearance: textfield;
	}
</style>