<script>
  import { createEventDispatcher } from "svelte";
  import Popup from "./Popup.svelte";
  const dispatch = createEventDispatcher();

  export let width = 50;
  export let height = 50;

  export let led = false;

  export let bgColor = "black";
  export let hoverColor = "gray";
  export let radius = 0;
  export let ledColor = "red";

  export let pressed = false;

  let color = bgColor;
  let popup;

  function mouseDown() {
    if (!pressed) {
      pressed = true;
      popup.add("pressed");
      dispatch("pressed", {});
    }
  }

  function mouseUp() {
    if (pressed) {
      pressed = false;
      popup.add("released");
      dispatch("released", {});
    }
  }

  function mouseOver() {
    color = hoverColor;
  }

  function mouseOut() {
    if (!pressed) {
      color = bgColor;
    }
  }
</script>

<style>
  button {
    position: relative;
  }
</style>

<button
  style={`
        width: ${width}px;
        height: ${height}px;
        border: 0;
        background-color: ${led ? ledColor : color};
        border-radius: ${radius}px;
        ${led ? 'box-shadow: 0 0 8px 4px ' + ledColor : ''};
    `}
  on:mouseup={mouseUp}
  on:mousedown={mouseDown}
  on:mouseover={mouseOver}
  on:mouseout={mouseOut}>
  <Popup bind:this={popup} />
</button>
