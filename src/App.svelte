<script>
  import { onMount } from "svelte";

  import Display, { pixelFormatEnum } from "./Display.svelte";
  import Encoder from "./Encoder.svelte";
  import Button from "./Button.svelte";
  import Panel from "./Panel.svelte";

  onMount(() => {
    let frame;
    mainInit();

    (function loop() {
      frame = requestAnimationFrame(loop);

      mainLoop();
    })();

    return () => {
      cancelAnimationFrame(frame);
    };
  });

  function mainInit() {
    display_left.clear(display_left.colorRGB(0, 0, 0));
    display_right.clear(display_right.colorRGB(0, 0, 0));
  }

  function drawNoise(display) {
    display.clear(display.colorRGB(0, 0, 0));
    for (let i = 0; i < 500; i++) {
      let color = display.colorRGB(
        Math.random() * 255,
        Math.random() * 255,
        Math.random() * 255
      );
      let x = Math.floor(Math.random() * display.width);
      let y = Math.floor(Math.random() * display.height);
      let w = 2;

      for (let j = 0; j < w; j++) {
        for (let i = 0; i < w; i++) {
          display.setPixel(x + j, y + i, color);
        }
      }
    }
    display.doRender();
  }

  function mainLoop() {
    drawNoise(display_left);
    drawNoise(display_right);
  }

  let display_left = null;
  let display_right = null;
</script>

<main>
  <Panel justify="center" offsetTop="50">
    <Panel direction="row" justify="around" offsetLeft="0">
      <Display
        bind:this={display_left}
        width="280"
        height="240"
        scale="1"
        pixelFormat={pixelFormatEnum.rgb565}
      />
    </Panel>
    <Panel direction="row" justify="around" offsetLeft="20">
      <Display
        bind:this={display_right}
        width="280"
        height="240"
        scale="1"
        pixelFormat={pixelFormatEnum.rgb565}
      />
    </Panel>
    <Panel direction="row" justify="around" offsetLeft="0">
      <Panel direction="column" justify="around" offsetLeft="20">
        <Encoder hue="0" sat="50" />
      </Panel>
      <Panel direction="column" justify="around" offsetLeft="20">
        <Encoder hue="120" sat="50" />
      </Panel>
      <Panel direction="column" justify="around" offsetLeft="20">
        <Encoder hue="240" sat="50" />
      </Panel>
      <Panel direction="column" justify="around" offsetLeft="20">
        <Encoder hue="60" sat="50" />
      </Panel>
    </Panel>
  </Panel>
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    width: 100%;
    margin: 0 auto;
  }

  :global(body) {
    background-color: rgb(48, 48, 48);
  }

  * {
    -moz-user-select: none;
    -o-user-select: none;
    -khtml-user-select: none;
    -webkit-user-select: none;
    -ms-user-select: none;
    user-select: none;
  }
</style>
