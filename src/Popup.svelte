<script>
	let uid = 1;
	let popups = [];
	let lastPopupText = '';

	let timeoutRunning = false;

	export function add(text, timeout = 1000) {
	    if(!(timeoutRunning && lastPopupText === text)) {
			popups = [{
				id: uid,
				text: text,
				timeout: timeout,
			}, ...popups];

			uid = uid + 1;
			lastPopupText = text;
			timeoutRunning = true;
			setTimeout(function () {
				timeoutRunning = false;
			}, 100);
		}
	}

	function remove(id) {
		popups = popups.filter(t => t.id !== id);
	}
</script>

{#each popups as popup (popup.id)}
    <div style="animation-duration: {popup.timeout}ms; left: {Math.random() * 80 + 20}%;" on:animationend={() => remove(popup.id) } class="popup">{popup.text}</div>
{/each}

<style>
    .popup{
		padding: 5px 15px;
		background-color: #13c30c;
		color: white;
		position: absolute;
		border-radius: 10px;
		animation: animate-in 350ms cubic-bezier(0.4, 0, 1, 1);
        top: -20px;
    }

	@keyframes animate-in {
		0% {
			transform: translate(-50%, 0);
			opacity: 0;
		}
        10% {
			opacity: 1;
		}
		100% {
			transform: translate(-50%, -100px);
            opacity: 0;
		}
	}
</style>