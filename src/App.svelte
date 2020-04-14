<script>
	import Display, {pixelFormatEnum} from './Display.svelte';
	import Encoder from "./Encoder.svelte";
	import Button from "./Button.svelte";
	import Led7Segment from "./Led7Segment.svelte"
	import {onMount} from 'svelte';
	import Panel from "./Panel.svelte";
	import Led from "./Led.svelte";

	let display;
	let x = 0;
	let y = 0;

	onMount(() => {
		let frame;
		mainInit();

		(function loop() {
			frame = requestAnimationFrame(loop);

			mainLoop();
		}());

		return () => {
			cancelAnimationFrame(frame);
		};
	});


	let index = 0;
	let time = 0;

	let ledDisplay;
	let leds = [];
	let buttons = [];

	for (let i = 0; i < 8; i++) {
		buttons[i] = {
			id: i
		};

		leds[i] = {
			id: i,
			active: false,
		};
	}

	function mainInit() {
		ledDisplay.setValue('abcdefgh');
	}

	function mainLoop() {
		let currentTime = performance.now();
		if (currentTime - time > 250) {
			time = currentTime;
			for (let i = 0; i < buttons.length; i++) {
				leds[i].active = false;
			}

			leds[index].active = true;
			index++;
			if (index >= buttons.length) index = 0;
		}
	}

	function onButtonReleased(index) {
		buttons[index].led = !buttons[index].led;
	}
</script>

<main>
	<Panel style="display: flex; justify-content: space-around; padding-bottom: 10px;">
		{#each leds as led (led.id)}
			<Led active={led.active}/>
		{/each}
	</Panel>

    <Panel>
		<Led7Segment bind:this={ledDisplay} ledNum="8" height="120"/>
    </Panel>

    <Panel style="display: flex; justify-content: space-around; padding-top: 10px;">
        {#each buttons as button (button.id)}
			<Button led={button.led} radius="8" on:released={() => onButtonReleased(button.id)}/>
        {/each}
    </Panel>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 596px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
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