<script>
    export let style = null;
    export let justify = false;
	export let offsetTop = 0;
	export let offsetBottom = 0;
	export let offsetLeft = 0;
	export let offsetRight = 0;
</script>
<div style="{`
        	padding-top: ${offsetTop}px;
        	padding-bottom: ${offsetBottom}px;
        	padding-left: ${offsetLeft}px;
        	padding-right: ${offsetRight}px;
        	${justify === 'around' ? 'display: flex; justify-content: space-around' : ''};
    	`}">
	<slot/>
</div>