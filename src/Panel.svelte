<script>
  export let style = null;
  export let justify = false;
  export let offsetTop = 0;
  export let offsetBottom = 0;
  export let offsetLeft = 0;
  export let offsetRight = 0;
  export let direction = "row";
</script>

<div
  style={`
            display: flex;
        	padding-top: ${offsetTop}px;
        	padding-bottom: ${offsetBottom}px;
        	padding-left: ${offsetLeft}px;
        	padding-right: ${offsetRight}px;
        	${justify === 'around' ? 'justify-content: space-around' : ''};
        	${justify === 'center' ? 'justify-content: center' : ''};
        	${direction === 'column' ? 'flex-direction: column' : ''};
    	`}>
  <slot />
</div>
