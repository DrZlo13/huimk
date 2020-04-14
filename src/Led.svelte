<script>
    export let form = 'round';
    export let active = false;
	export let color = 'red';
	export let bgColor = '#262626';

	export let width = false;
	export let height = 50;
</script>

<div
    class="led"
	style="{`
        	width: ${width ? width : height}px;
        	height: ${height}px;
        	height: ${height}px;
        	${active ? 'box-shadow: 0 0 8px 4px ' + color : ''};
        	${active ? 'background: ' + color : 'background: ' + bgColor };
        	${form === 'round' ? 'border-radius: 50%' : ''};
    	`}"
>

</div>

<style>
    .led{
        border-radius: 50%;
        display: inline-block;
    }
</style>