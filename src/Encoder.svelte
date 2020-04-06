<script>
	import { createEventDispatcher } from 'svelte';
	import Popup from "./Popup.svelte";

	const dispatch = createEventDispatcher();

    export let clickCount = 24;
	export let radius = 50;
	export let pixelStep = 10;

	let mouseButtonPressed = false;
    let encoder;
    let popup;
    let degrees = 0;
    let startPoint = {x: 0, y: 0};
    let clickDegrees = 360 / clickCount;
    let moved = false;

	function mouseUp(event) {
		if(!moved && mouseButtonPressed === true){
			pressed();
		}
		mouseButtonPressed = false;
	}

	function mouseDown(event) {
		mouseButtonPressed = true;
		moved = false;
		startPoint.x = event.pageX;
		startPoint.y = event.pageY;
	}

	function pressed() {
		dispatch('pressed', {});
		popup.add('pressed');
	}

	function rotateCW() {
		degrees += clickDegrees;
		startPoint.x = event.pageX;
		startPoint.y = event.pageY;
		dispatch('cw', {});
		popup.add('click');
		moved = true;
	}

	function rotateCCW() {
		degrees -= clickDegrees;
		startPoint.x = event.pageX;
		startPoint.y = event.pageY;
		dispatch('ccw', {});
		popup.add('click');
		moved = true;
	}

	function mouseMove(event) {
		if(mouseButtonPressed){
		    if((startPoint.x - event.pageX) > pixelStep) rotateCCW();
			if((startPoint.y - event.pageY) > pixelStep) rotateCCW();
			if((startPoint.y - event.pageY) < -pixelStep) rotateCW();
			if((startPoint.x - event.pageX) < -pixelStep) rotateCW();
        }
	}

	let pointers = [];
	for (let i = 0; i < clickCount; i++){
		pointers = [{
			id: i,
			angle: clickDegrees*i,
		}, ...pointers];
    }
</script>

<svelte:body on:mouseup={mouseUp} on:mousemove={mouseMove}/>
<div class="container">
	<div bind:this={encoder}
         on:mousedown={mouseDown}
         class="dial"
         style="{`
             width: ${radius*2}px;
             height: ${radius*2}px;
             transform: rotate(${degrees}deg);
             background: ${mouseButtonPressed ? '#9b9b9b' : '#575757'}
         `}
">
        {#each pointers as pointer (pointer.id)}
			<div style="{`height: ${radius/4}px; transform: rotate(${pointer.angle}deg); transform-origin: 50% ${radius}px;`}" class="pointer-s"></div>
        {/each}

		<div class="pointer" style="{`height: ${radius/2}px;`}"></div>
	</div>

	<Popup bind:this={popup}/>
</div>


<style>
	.container{
		position: relative;
		display: inline-block;
	}

	.dial {
		width: 0;
		height: 0;
		background: #575757;
        position: relative;
        border-radius: 50%;
		transition: transform 0.05s linear;
	}

	.pointer{
		width: 10px;
		height: 50px;
		background: #000000;
		position: absolute;
		margin-left: -5px;
		left: 50%;
		top: 0;
	}

    .pointer-s{
		width: 4px;
		height: 50px;
		background: #4a4a4a;
		position: absolute;
		margin-left: -2px;
		left: 50%;
		top: 0;
	}
</style>