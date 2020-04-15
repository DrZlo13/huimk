<script>
	// dp g f e d c b a
	export let ledCount = 1;
	export let width = false;
	export let height = 320;
	export let activeColor = 'red';
	export let backgroundColor = '#262626';

	let segments = [];

	for(let i = 0; i < ledCount; i ++){
		segments[i] = {
			id: i,
			led: 0,
		}
	}

	export function setValue(value){
		value = value.toString().toLowerCase();
		let segmentIndex = ledCount - 1;

		for (let i = 0; i < ledCount; i++) {
			segments[i].led = 0;
		}

		for (let i = value.length - 1; i >= 0; i--) {
			let fontLetter = font[value.charAt(i)];

			if(segmentIndex >= 0) {
				if (typeof fontLetter !== 'undefined') {
					fontLetter |= 0b10000000;
					segments[segmentIndex].led |= 0b01111111;
					segments[segmentIndex].led = segments[segmentIndex].led & fontLetter;
				} else {
					segments[segmentIndex].led = 0;
				}
				segmentIndex--;
			}
		}
	}

	export function setValueAt(value, index){
		if(index >= 0 && index < ledCount){
			value = value.toString().toLowerCase();

			let fontLetter = font[value];

			if (typeof fontLetter !== 'undefined') {
				fontLetter |= 0b10000000;
				segments[index].led |= 0b01111111;
				segments[index].led = segments[index].led & fontLetter;
			} else {
				segments[index].led = 0;
			}
		}
	}

	export function setPointAt(pointValue, index){
		if(index >= 0 && index < ledCount){
			if(pointValue == true){
				segments[index].led |= 0b10000000;
			} else {
				segments[index].led &= 0b01111111;
			}
		}
	}

	export let font = {
		'0': 0b00111111,
		'1': 0b00000110,
		'2': 0b01011011,
		'3': 0b01001111,
		'4': 0b01100110,
		'5': 0b01101101,
		'6': 0b01111101,
		'7': 0b00000111,
		'8': 0b01111111,
		'9': 0b01101111,
		'a': 0b01110111,
		'b': 0b01111100,
		'c': 0b00111001,
		'd': 0b01011110,
		'e': 0b01111001,
		'f': 0b01110001,
		'g': 0b00111101,
		'h': 0b01110110,
		'i': 0b00000110,
		'j': 0b00011110,
		'k': 0b01110101,
		'l': 0b00111000,
		'm': 0b01010101,
		'n': 0b00110111,
		'o': 0b00111111,
		'p': 0b01110011,
		'q': 0b01100111,
		'r': 0b00110001,
		's': 0b01101101,
		't': 0b01111000,
		'u': 0b00111110,
		'v': 0b00101010,
		'w': 0b01101010,
		'x': 0b01001001,
		'y': 0b01101110,
		'z': 0b01011011,
		'-': 0b01000000,
	};
</script>

<div class="segments-wrapper">
{#each segments as segment (segment.id)}
	<svg
		version="1.1"
		xmlns="http://www.w3.org/2000/svg"
		xmlns:xlink="http://www.w3.org/1999/xlink"
		viewBox="0 0 192 320"
		xml:space="preserve"
		class="segments"
		style="{`
        	${width ? 'width: '+width+'px' : ''};
        	height: ${height}px;
    	`}"
	>
		<polygon id="a" fill="{`${(segment.led & 0b00000001) ? activeColor : backgroundColor}`}" points="43.4,32 59.9,16.8 151.1,16.8 165,32 148.5,47.2 57.3,47.2 "/>
		<polygon id="b" fill="{`${(segment.led & 0b00000010) ? activeColor : backgroundColor}`}" points="167.9,35.2 181.8,50.4 173.8,141.6 157.3,156.8 143.4,141.6 151.4,50.4 "/>
		<polygon id="c" fill="{`${(segment.led & 0b00000100) ? activeColor : backgroundColor}`}" points="156.7,163.2 170.6,178.4 162.6,269.6 146.1,284.8 132.2,269.6 140.2,178.4 "/>
		<polygon id="d" fill="{`${(segment.led & 0b00001000) ? activeColor : backgroundColor}`}" points="142.6,288 126.1,303.2 34.9,303.2 21,288 37.5,272.8 128.7,272.8 "/>
		<polygon id="e" fill="{`${(segment.led & 0b00010000) ? activeColor : backgroundColor}`}" points="18.1,284.8 4.2,269.6 12.2,178.4 28.7,163.2 42.6,178.4 34.6,269.6 "/>
		<polygon id="f" fill="{`${(segment.led & 0b00100000) ? activeColor : backgroundColor}`}" points="29.3,156.8 15.4,141.6 23.4,50.4 39.9,35.2 53.8,50.4 45.8,141.6 "/>
		<polygon id="g" fill="{`${(segment.led & 0b01000000) ? activeColor : backgroundColor}`}" points="32.2,160 48.7,144.8 139.9,144.8 153.8,160 137.3,175.2 46.1,175.2 "/>
		<circle id="dp" fill="{`${(segment.led & 0b10000000) ? activeColor : backgroundColor}`}" cx="174.6" cy="288.1" r="13.9"/>
	</svg>
{/each}
</div>

<style>
	.segments{
		background-color: black;
	}

	.segments-wrapper{
		display: inline-block;
		background-color: black;
		padding: 10px;
	}
</style>
