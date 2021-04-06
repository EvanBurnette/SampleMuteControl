<script>
	import { onMount } from 'svelte';
	let pads = [];
	pads.length = 10;
	pads.fill(true)
	let lastPads = [...pads]
	let checkChange = function(){
		console.log("check")
		for (let i = 0; i < pads.length; i++){
			if (pads[i] != lastPads[i]){
				lastPads[i] = pads[i]
				midiOutput.send([0xB0 + i, 58, 127*pads[i]]);
				console.log('midi sent')
			}
		}
	}
	
	let midiOutput = null;
	let handleTouchmove = function(e) {
                // get the touch element
                let touch = e.touches[0];

                // get the DOM element
                let element = document.elementFromPoint(touch.clientX, touch.clientY);

                // make sure an element was found - some areas on the page may have no elements
                if (element) {
                    // interact with the DOM element
                    element.click()
										checkChange()
                }
			}
	navigator.requestMIDIAccess()
		.then(function(midiAccess) {
			midiOutput = Array.from(midiAccess.outputs.values())[0]
})
</script>

<svelte:head>
	<style>
		body{
			background: black;
			margin: 0;
			padding: 0;
		}
	</style>
	<title>Volca Mute Changer</title>
</svelte:head>

<main on:touchmove|preventDefault={handleTouchmove}>
{#each pads as pad, i}
<div class="pad">
	<div class="top" on:mouseover={() => {
		pads[i] = false
		checkChange()
		}} on:touchstart={() => pads[i] = false} on:click={() => pads[i] = false}>
	Off
	</div>
	<div class="bot" on:mouseover={() => {
		pads[i] = true
		checkChange()
		}} on:touchStart={() => pads[i] = true} on:click={() => pads[i] = true}>
	{#if pad}
	<div class="light unmute">
		
	</div>
	{:else}
		<div class="light">
		</div>
	{/if}
	<div>
		On
	</div>
	<div>
		{i + 1}
	</div>
</div>
</div>
{/each}
</main>

<style>
	main{
		display: grid;
		grid-template-columns: repeat(10, 1fr);
		margin: 0;
		padding: 0;
		background: black;
		color: white;
		height: 100vh;
	}
	.pad {
		border: solid white;
		margin: 0px 1px;
		border-radius: 5px;
		display: grid;
		grid-template-rows: 1fr 1fr;
	}
	
	.pad div {
		display: grid;
		justify-content: center;
		align-items: center;
	}
	.bot {
		border-top: solid;
		display: grid;
		grid-template-rows: repeat(3, 1fr);
	}
	.light {
		height: 10px;
		width: 10px;
		border-radius: 50%;
		transform: translate(5px,5px);
		box-shadow: 0 0 0px 2px gray;
	}
	.unmute {
		background: red;
		border: none;
		box-shadow: 0 0 0 1.1px white, 0 0 10px 5px red;
	}
</style>