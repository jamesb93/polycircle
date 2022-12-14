<script>
	export let phases = new Array(4).fill(0);
	import * as d3 from "d3";
	const WIDTH = 600;
	const HEIGHT = 300;
	const TWOPI = Math.PI * 2;

	export const scale = (i, iMin, iMax, oMin, oMax) => {
    	return ((i - iMin) * (oMax - oMin)) / (iMax - iMin) + oMin;
	};

	const phaseCircle = (phase) => {
		const offset = 75;
		return [
			scale(Math.sin(phase * TWOPI), -1, 1, offset, WIDTH-offset),
			scale(Math.cos(phase * TWOPI), -1, 1, offset, HEIGHT-offset),
		]
	}

	$: coordinates = phases.map(p => phaseCircle(p))

	const strokes = [
		d3.interpolateSinebow(0 / 5),
		d3.interpolateSinebow(1 / 5),
		d3.interpolateSinebow(2 / 5),
		d3.interpolateSinebow(3 / 5),
		d3.interpolateSinebow(4 / 5)
	]
</script>

<svg width={WIDTH} height={HEIGHT}>
	{#each coordinates as cd, i}
	<line 
		x1={WIDTH/2} y1={HEIGHT/2} 
		x2={cd[0]} y2={cd[1]} 
		stroke-width={5}
		stroke={strokes[i]}
	/>
	<circle cx={cd[0]} cy={cd[1]} r={5} fill={strokes[i]} />
	{/each}
	<line class='stem' x1={WIDTH/2} y1={HEIGHT} x2={WIDTH/2} y2={HEIGHT/2} />
	<circle r={5} cx={WIDTH/2} cy={HEIGHT/2}/>
</svg>

<style>
	svg {
		width: 600px;
		height: 300px;
		border-radius: 6px;
		background-color: #e2e6ef
	}

	.stem {
		stroke: black;
		stroke-width: 5px;
	}
</style>

