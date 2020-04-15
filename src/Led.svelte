<script>
    export let form = 'round';
    export let active = false;
	export let backgroundColor = '#262626';
	export let activeColor = 'red';

	export let width = false;
	export let height = 50;
</script>

<div
    class="led"
	style="{`
        	width: ${width ? width : height}px;
        	height: ${height}px;
        	${active ? 'box-shadow: 0 0 8px 4px ' + activeColor : ''};
        	${active ? 'background: ' + activeColor : 'background: ' + backgroundColor };
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