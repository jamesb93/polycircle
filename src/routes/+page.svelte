<script>
	import { createDeviceInstance, sendDeviceMessage } from '@jamesb93/rnbo-svelte'
	import Interface from '$lib/Interface.svelte';
	import SVGInterface from '$lib/SVGInterface.svelte';
	import Ratio from '$lib/Ratio.svelte';
	import { presets } from '$lib/presets.js';
	import * as d3 from 'd3';

	let patch, context

	let phasors = new Array(4).fill(0.0);
	let fan = 1.0;
	let rate = 0.35;
	let [denom1, divis1] = [3, 2];
	let [denom2, divis2] = [5, 4];
	let [denom3, divis3] = [15, 16];
	let [denom4, divis4] = [7, 8];

	const start = async() => {
        context = new (window.AudioContext || window.webkitAudioContext)();
        let gain = context.createGain().connect(context.destination);
        createDeviceInstance('/patch.export.json', context, gain)
		.then(device => {
			patch = device
			patch.messageEvent.subscribe(ev => {
				if (ev.tag === 'phasors') {
					phasors = ev.payload;
				}
			})
		})
	}

	const applyPreset = (x) => {
		denom1 = x[0]
		divis1 = x[1]
		denom2 = x[2]
		divis2 = x[3]
		denom3 = x[4]
		divis3 = x[5]
		denom4 = x[6]
		divis4 = x[7]
	}

	$: if (patch) sendDeviceMessage(patch, 'rate', [rate]);
	$: if (patch) sendDeviceMessage(patch, 'r1', [denom1 / divis1]);
	$: if (patch) sendDeviceMessage(patch, 'r2', [denom2 / divis2]);
	$: if (patch) sendDeviceMessage(patch, 'r3', [denom3 / divis3]);
	$: if (patch) sendDeviceMessage(patch, 'r4', [denom4 / divis4]);
</script>

<div class="container">
	<button on:click={start} disabled={patch}>start</button>
	<div class="rate control">
		<div class="label">Rate</div>
		<div class="value">{rate}</div>
		<input type=range min=0.1 max=5 step=0.01 bind:value={rate}/>
	</div>

	<div class="rhythms">
		<div class="subcontainer">
			<div class="label">Rhythmic Ratio</div>
			<div class="ratios">
				<Ratio bind:denom={denom1} bind:divis={divis1} title={'ratio 1'} color={d3.interpolateSinebow(0.2)} />
				<Ratio bind:denom={denom2} bind:divis={divis2} title={'ratio 2'} color={d3.interpolateSinebow(0.4)} />
				<Ratio bind:denom={denom3} bind:divis={divis3} title={'ratio 3'} color={d3.interpolateSinebow(0.6)} />
				<Ratio bind:denom={denom4} bind:divis={divis4} title={'ratio 4'} color={d3.interpolateSinebow(0.8)} />
			</div>
		</div>
		
		<div class="subcontainer">
			<div class="label">Presets</div>
			<div class="presets">
				{#each presets as p, i}
					<button on:click={() => applyPreset(p)}>{i}</button>
				{/each}
			</div>
		</div>
	</div>

	<!-- <Interface bind:phases={phasors}/> -->
	<SVGInterface bind:phases={phasors}/>
</div>

<style>
	.container {
		display: flex;
		flex-direction: column;
		max-width: 600px;
		max-height: 1200px;
		gap: 0.5em;
	}

	.subcontainer {
		display: flex;
		flex-direction: column;;
		gap: 1em;
		border: 1px solid #e2e6ef;
		border-radius: 6px;
		padding: 1em;
	}
	.rhythms {
		display: grid;
		grid-template-columns: 1fr 1fr;
		gap: 2em;
	}

	.ratios {
		display: flex;
		flex-direction: column;;
	}

	.control {
		display: flex;
		flex-direction: row;
		gap: 0.25em;
		justify-content: left;
		align-items: center;
	}

	.value {
		min-width: 4ch;
	}
</style>
