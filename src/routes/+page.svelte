<script>
	import '../app.css';
	import { onMount } from 'svelte';

	function getRandom(max) {
		let random = Math.floor((Math.random() * max) + 1)
		if (generated.includes(random)) {
			if (generated.length >= max - 1) {
				alert('Ran out of numbers');
				throw new Error('Ran out of numbers');
			} else {
				random = getRandom(max);
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
	let decimal = $state(0);
	let binary = $derived(decToBin(decimal, length));
	let max = $state(75);

	let presenting = $state(false);

	let check = $state('');

	let generated = $state([]);

	let isCorrect = $derived(generated.includes(parseInt(check)) && check.length > 0);
	let isIncorrect = $derived(!generated.includes(parseInt(check)) && check.length > 0);

	let deleteConfirm = $state(false);

	let date = $state(new Date());

	setInterval(() => date = new Date(), 1000);

	function resetGame() {
		length = 8;
		decimal = 0;
		generated = [];
		deleteConfirm = false;
		alert('Game has been reset successfuly');
	}

	onMount(() => {
		if (localStorage.generated !== undefined) {
			generated = JSON.parse(localStorage.generated);
			if (generated.length > 0) {
				length = localStorage.len;
				decimal = generated.at(-1);
			}
		}
	});
	$effect(() => {
		localStorage.generated = JSON.stringify(generated);
		localStorage.len = length;
	});
</script>

<svelte:window
	onkeydown={(e) => {
		if (e.key === 'p') {
			presenting = !presenting;
		}
	}}
/>

{#if !presenting}
	<div class="flex min-h-screen items-center justify-center bg-slate-900 text-slate-50">
		<div class="rounded-xl p-8 shadow-2xl m-8 bg-slate-800">
			<h1>Binary Bingo!</h1>
			<p>{binary}</p>
			<p>{decimal}</p>
			<div class="my-2">
				<label for="length">Length:</label>
				<input
					id="length"
					class="rounded-xl bg-slate-700 p-2 shadow-md"
					type="number"
					placeholder="length"
					bind:value={length}
					disabled={generated.length > 0}
				/>
			</div>
			<div class="my-2">
				<label for="length">Max:</label>
				<input
					id="max"
					class="rounded-xl bg-slate-700 p-2 shadow-md"
					type="number"
					placeholder="max"
					bind:value={max}
					disabled={generated.length > 0}
				/>
			</div>
			<button
				class="rounded-xl bg-slate-700 p-2 shadow-md"
				onclick={() => {
					decimal = getRandom(max);
					generated.push(decimal);
				}}>Get New Binary</button
			>
			<h2>Generated numbers</h2>
			{#each generated.toReversed() as number}
				<div>{decToBin(number, length)}</div>
			{/each}
			<input
				type="text"
				bind:value={check}
				placeholder="enter to check"
				class={[
					'rounded-xl p-2 shadow-md',
					{ 'bg-slate-700': !isCorrect && !isIncorrect },
					{ 'bg-green-700': isCorrect },
					{ 'bg-red-700': isIncorrect }
				]}
			/>
			{#if deleteConfirm === false}
				<button
					onclick={() => (deleteConfirm = true)}
					class="rounded-xl bg-slate-700 p-2 shadow-md">Reset Game</button
				>
			{:else}
				<button onclick={resetGame} class="rounded-xl bg-red-600 p-2 shadow-md"
					>Are you sure?</button
				>
				<button onclick={deleteConfirm = false} class="rounded-xl bg-slate-700 p-2 shadow-md">Cancel</button>
			{/if}
		</div>
	</div>
{:else}
	<div class="flex h-screen items-center justify-center bg-slate-900 text-slate-50 flex-col">
		<div>Current time: {date.toLocaleTimeString()}</div>
		<button
			class="rounded-2xl bg-slate-700 p-8 text-8xl shadow-2xl"
			onclick={() => {
				decimal = getRandom(max);
				generated.push(decimal);
			}}
		>
			{decToBin(decimal, length)}
		</button>
	</div>
{/if}
