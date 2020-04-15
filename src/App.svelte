<script>
	import {onMount} from 'svelte';

	import Display, {pixelFormatEnum} from './Display.svelte';
	import Encoder from "./Encoder.svelte";
	import Button from "./Button.svelte";
	import Led7Segment from "./Led7Segment.svelte"
	import Panel from "./Panel.svelte";
	import Led from "./Led.svelte";

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



	let time = 0;

	let ledDisplay;
	let leds = [];
	let buttons = [];

	for (let i = 0; i < 8; i++) {
		// обьект кнопки с id для отслеживания нажатий
		buttons[i] = {
			id: i
		};

		// обьект светодиода со статусом активности
		leds[i] = {
			active: false,
		};
	}

	let index = 0;
	setInterval(function () {
	    // гасим светодиоды
		for (let i = 0; i < buttons.length; i++) {
			leds[i].active = false;
		}

		// зажигаем текущий активный
		leds[index].active = true;

		// увеличиваем и оборачиваем индекс
		index++;
		if (index >= buttons.length) index = 0;
	}, 250);

	function onButtonReleased(index) {
	}

	function onButtonPressed(index) {
	    switch (index) {
            case 0: ledDisplay.setValue('hello'); break;
            case 1: ledDisplay.setValue('abcdefgh'); break;
            case 2: ledDisplay.setValue('12345678'); break;
            case 3: ledDisplay.setValue('1-2-3-4'); break;
            case 4: ledDisplay.setValue('test'); break;
            case 5: ledDisplay.setValue('jklmnopq'); break;
            case 6: ledDisplay.setValue('ERROR'); break;
            case 7: ledDisplay.setValue(''); break;
		}
	}

    // переменная в которой будет обьект дисплея
    let display;

    // софтварный рендеринг куба
    let vectors = [[60, 60, 60],[-60, 60, 60],[-60, -60, 60],[60, -60, 60],[60, 60, -60],[-60, 60, -60],[-60, -60, -60],[60, -60, -60]];
    let perspective = 180.0;
    let cubeColor;

    // случайный цвет
    function randomCubeColor() {
        cubeColor = display.colorRGB(255 * Math.random(), 255 * Math.random(), 255 * Math.random());
    }

    // поворот куба по X
    function rotateX(angle)
    {
        let rad, cosa, sina, Yn, Zn;

        rad = angle * Math.PI / 180;
        cosa = Math.cos(rad);
        sina = Math.sin(rad);
        for (let i = 0; i < 8; i++)
        {
            Yn = (vectors[i][1] * cosa) - (vectors[i][2] * sina);
            Zn = (vectors[i][1] * sina) + (vectors[i][2] * cosa);
            vectors[i][1] = Yn;
            vectors[i][2] = Zn;
        }
    }

    // поворот куба по Y
    function rotateY(angle)
    {
        let rad, cosa, sina, Xn, Zn;

        rad = angle * Math.PI / 180;
        cosa = Math.cos(rad);
        sina = Math.sin(rad);
        for (let i = 0; i < 8; i++)
        {
            Xn = (vectors[i][0] * cosa) - (vectors[i][2] * sina);
            Zn = (vectors[i][0] * sina) + (vectors[i][2] * cosa);
            vectors[i][0] = Xn;
            vectors[i][2] = Zn;
        }
    }

    // поворот куба по Z
    function rotateZ(angle)
    {
        let rad, cosa, sina, Xn, Yn;

        rad = angle * Math.PI / 180;
        cosa = Math.cos(rad);
        sina = Math.sin(rad);
        for (let i = 0; i < 8; i++)
        {
            Xn = (vectors[i][0] * cosa) - (vectors[i][1] * sina);
            Yn = (vectors[i][0] * sina) + (vectors[i][1] * cosa);
            vectors[i][0] = Xn;
            vectors[i][1] = Yn;
        }
    }

    // вычисление смещения точек на экране
    // перспектива используется неверно, но для демонстрации сойдет
    function translateX(x, z)
    {
        return (x + 120) + (z * (x / perspective));
    }

    function translateY(y, z)
    {
        return (y + 120) + (z * (y / perspective));
    }

    function drawCube()
    {
        display.drawLine(translateX(vectors[0][0], vectors[0][2]), translateY(vectors[0][1], vectors[0][2]), translateX(vectors[1][0], vectors[1][2]), translateY(vectors[1][1], vectors[1][2]), cubeColor);
        display.drawLine(translateX(vectors[1][0], vectors[1][2]), translateY(vectors[1][1], vectors[1][2]), translateX(vectors[2][0], vectors[2][2]), translateY(vectors[2][1], vectors[2][2]), cubeColor);
        display.drawLine(translateX(vectors[2][0], vectors[2][2]), translateY(vectors[2][1], vectors[2][2]), translateX(vectors[3][0], vectors[3][2]), translateY(vectors[3][1], vectors[3][2]), cubeColor);
        display.drawLine(translateX(vectors[3][0], vectors[3][2]), translateY(vectors[3][1], vectors[3][2]), translateX(vectors[0][0], vectors[0][2]), translateY(vectors[0][1], vectors[0][2]), cubeColor);
        display.drawLine(translateX(vectors[4][0], vectors[4][2]), translateY(vectors[4][1], vectors[4][2]), translateX(vectors[5][0], vectors[5][2]), translateY(vectors[5][1], vectors[5][2]), cubeColor);
        display.drawLine(translateX(vectors[5][0], vectors[5][2]), translateY(vectors[5][1], vectors[5][2]), translateX(vectors[6][0], vectors[6][2]), translateY(vectors[6][1], vectors[6][2]), cubeColor);
        display.drawLine(translateX(vectors[6][0], vectors[6][2]), translateY(vectors[6][1], vectors[6][2]), translateX(vectors[7][0], vectors[7][2]), translateY(vectors[7][1], vectors[7][2]), cubeColor);
        display.drawLine(translateX(vectors[7][0], vectors[7][2]), translateY(vectors[7][1], vectors[7][2]), translateX(vectors[4][0], vectors[4][2]), translateY(vectors[4][1], vectors[4][2]), cubeColor);
        display.drawLine(translateX(vectors[0][0], vectors[0][2]), translateY(vectors[0][1], vectors[0][2]), translateX(vectors[4][0], vectors[4][2]), translateY(vectors[4][1], vectors[4][2]), cubeColor);
        display.drawLine(translateX(vectors[1][0], vectors[1][2]), translateY(vectors[1][1], vectors[1][2]), translateX(vectors[5][0], vectors[5][2]), translateY(vectors[5][1], vectors[5][2]), cubeColor);
        display.drawLine(translateX(vectors[2][0], vectors[2][2]), translateY(vectors[2][1], vectors[2][2]), translateX(vectors[6][0], vectors[6][2]), translateY(vectors[6][1], vectors[6][2]), cubeColor);
        display.drawLine(translateX(vectors[3][0], vectors[3][2]), translateY(vectors[3][1], vectors[3][2]), translateX(vectors[7][0], vectors[7][2]), translateY(vectors[7][1], vectors[7][2]), cubeColor);
        display.doRender();
    }

    // служебная функция, вызывается после создании всех компонентов
    function mainInit() {
        cubeColor = display.colorRGB(0, 255, 0);
    }

    // служебная функция, вызывается каждый фрейм анимации
    function mainLoop() {
        display.clear(display.colorRGB(0, 0, 0));
        drawCube();
    }

</script>

    <main>
        <Panel justify="around" offsetBottom="10">
            {#each leds as led}
                <Led active={led.active}/>
            {/each}
        </Panel>

        <Panel>
            <Led7Segment bind:this={ledDisplay} ledCount="8" height="120"/>
        </Panel>

        <Panel justify="around" offsetTop="10">
            {#each buttons as button}
                <Button radius="8" on:pressed={() => onButtonPressed(button.id)}/>
            {/each}
        </Panel>

		<Panel justify="center" offsetTop=50>
            <Display bind:this={display} width=240 height=240 scale=2 pixelFormat={pixelFormatEnum.rgb565}/>
			<Panel direction="column" justify="around" offsetLeft=20>
                <Encoder on:click={randomCubeColor} on:ccw={() => rotateY(2)} on:cw={() => rotateY(-2)}/>
                <Encoder on:click={randomCubeColor} on:ccw={() => rotateZ(2)} on:cw={() => rotateZ(-2)}/>
			</Panel>
		</Panel>

    </main>

<style>
	main {
		text-align: center;
		padding: 1em;
		width: 596px;
		margin: 0 auto;
	}

	*{
		-moz-user-select: none;
		-o-user-select:none;
		-khtml-user-select: none;
		-webkit-user-select: none;
		-ms-user-select: none;
		user-select: none;
	}
</style>