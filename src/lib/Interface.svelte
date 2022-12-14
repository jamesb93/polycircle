 <script>
	import { onMount } from 'svelte';
	import { CanvasSpace, SVGSpace, Pt } from 'pts';
	import * as d3 from 'd3';

	let canvas;

	const TWOPI = Math.PI * 2;
	const WIDTH = 600;
	const HEIGHT = 300;

	export let phases = [0.0, 0.0, 0.0, 0.0];

	export const scale = (i, iMin, iMax, oMin, oMax) => {
    	return ((i - iMin) * (oMax - oMin)) / (iMax - iMin) + oMin;
	};

	const phaseCircle = (phase) => {
		const offset = 75;
		return new Pt([
			scale(Math.sin(phase * TWOPI), -1, 1, offset, WIDTH-offset),
			scale(Math.cos(phase * TWOPI), -1, 1, offset, HEIGHT-offset),
		])
	}
	
	onMount(() => {
		let centre;
		const mid = new Pt(WIDTH/2, HEIGHT/2)
		let space = new SVGSpace('#sketch')
		.setup({ bgcolor: '#e2e6ef' });

		let form = space.getForm();
		space.add({
			start: () => {
				centre = [ mid, new Pt(WIDTH/2, HEIGHT) ];
				form.fill('#000').point(mid, 10, "circle")
			},
			animate: (time, ftime) => {
				
				// spine
				phases.forEach((x, i) => {
					const colour = d3.interpolateRainbow(i / phases.length)
					const end = phaseCircle(x);
					form.strokeOnly(colour, 5).line([
						mid,
						end
					])
					form.fill(colour)
					.point( end, 3, "circle" );
				})
				form.strokeOnly("#123", 7).line(centre);
				form.fill('#000').point(mid, 3, "circle")

			},
		});
		space.bindMouse().bindTouch().play();
		ready = true;
	})
</script>

<!-- <canvas id='sketch' bind:this={canvas} /> -->
<svg id='sketch' bind:this={canvas} />


<style>
	#sketch {
		border: 1px solid black;
		width: 600px;
		height: 300px;
		border-radius: 6px;
	}
</style>

