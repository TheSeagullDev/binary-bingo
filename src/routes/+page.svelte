<script>
	import "../app.css";
    import { onMount } from "svelte";

    function getRandom(length) {
		let random = '';
		for (let i = 0; i < length; i++) {
			if (Math.random() > 0.5) {
				random += '0';
			} else {
				random += '1';
			}
		}
		return random;
	}

	function binToDec(bin) {
		let dec = 0;
		for (let i = 0; i < bin.length; i++) {
			let current = bin.charAt(bin.length - 1 - i);
			if (current === '1') {
				dec += Math.pow(2, i);
			}
		}
		return dec;
	}

	function decToBin(dec, length) {
		if (dec > Math.pow(2, length) - 1) {
			throw new Error(`Decimal ${dec} cannot fit in length ${length}`);
		} else {
			let bin = dec.toString(2);
			return bin.padStart(length, '0');
		}
	}

	let length = $state(8);
	let binary = $state('00000000');
	let decimal = $derived(binToDec(binary));

	let generated = $state([]);

    let deleteConfirm = $state(false);

    function resetGame() {
        length = 8;
        binary = "00000000";
        generated = [];
        deleteConfirm = false;
        alert("Game has been reset successfuly");
    }

    onMount(() => {
        if(localStorage.generated !== undefined) {
            generated = JSON.parse(localStorage.generated);
        }
    })
    $effect(() => localStorage.generated = JSON.stringify(generated));
</script>

<div class="flex h-screen items-center justify-center bg-sky-950 text-sky-50">
	<div class="rounded-xl bg-indigo-700 p-8 shadow-2xl">
		<h1>Binary Bingo!</h1>
		<p>{binary}</p>
		<p>{decimal}</p>
		<label for="length">Length:</label>
		<input
			id="length"
			class="rounded-xl bg-indigo-600 p-2 shadow-md"
			type="number"
			placeholder="length"
			bind:value={length}
		/>
		<button
			class="rounded-xl bg-indigo-600 p-2 shadow-md"
			onclick={() => {
				binary = getRandom(length);
                generated.push(binary);
			}}>Get New Binary</button
		>
        <h2>Generated numbers</h2>
        {#each generated as number}
            <div>{number}</div>
        {/each}
        {#if deleteConfirm === false}
            <button onclick={() => deleteConfirm = true} class="rounded-xl bg-indigo-600 p-2 shadow-md">Reset Game</button>
        {:else}
            <button onclick={resetGame} class="rounded-xl bg-red-600 p-2 shadow-md">Are you sure?</button>
        {/if}
	</div>
</div>
