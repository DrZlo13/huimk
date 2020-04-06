<script>
	import Display, {pixelFormatEnum} from './Display.svelte';
	import Encoder from "./Encoder.svelte";
	import { onMount } from 'svelte';

	let display;
	let x = 0;
	let y = 0;

	onMount(() => {
		let frame;

		(function loop() {
			frame = requestAnimationFrame(loop);

			mainLoop();
		}());

		return () => {
			cancelAnimationFrame(frame);
		};
	});

	function cwX() {
		x++;
	}

	function ccwX() {
		x--;
	}

	function cwY() {
		y++;
	}

	function ccwY() {
		y--;
	}

	function mainLoop() {
		display.clear(display.colorRGB(0, 0, 0));
		display.drawHLine(y, display.colorRGB(255, 0, 0));
		display.drawVLine(x, display.colorRGB(0, 255, 0));
		display.fillRect(x-3, y-3, x+3, y+3, display.colorRGB(0, 0, 255));
		display.doRender();
	}

</script>

<main>
	<Display bind:this={display} width={160} height="{80}" pixelFormat="{pixelFormatEnum.rgb565}" scale="2"/>
	<div>
		<Encoder on:cw={cwX} on:ccw={ccwX}/>
		<Encoder on:cw={cwY} on:ccw={ccwY}/>
	</div>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}

	* {
		-moz-user-select: none;
		-o-user-select:none;
		-khtml-user-select: none;
		-webkit-user-select: none;
		-ms-user-select: none;
		user-select: none;
	}
</style>