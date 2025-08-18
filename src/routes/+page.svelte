<script>
    function getRandom(length) {
        let random = "";
        for(let i = 0; i < length; i++) {
            if(Math.random() > 0.5) {
                random += "0";
            }
            else {
                random += "1";
            }
        }
        return random;
    }

    function binToDec(bin) {
        let dec = 0;
        for(let i = 0; i < bin.length; i++) {
            let current = bin.charAt(bin.length - 1 - i);
            if (current === "1") {
                dec += Math.pow(2, i);
            }
        }
        return dec;
    }

    function decToBin(dec, length) {
        if(dec > Math.pow(2, length) - 1) {
            throw new Error(`Decimal ${dec} cannot fit in length ${length}`);
        }
        else {
            let bin = dec.toString(2);
            return bin.padStart(length, "0");
        }
    }

    let length = $state(8);
    let binary = $state("00000000");
    let decimal = $derived(binToDec(binary));
    
</script>

<div class="bg-sky-950 h-screen text-sky-50 flex justify-center items-center">
    <div class="bg-indigo-700 p-8 rounded-xl shadow-2xl">
        <h1>Binary Bingo!</h1>
        <p>{binary}</p>
        <p>{decimal}</p>
        <label for="length">Length:</label>
        <input id="length" class="bg-indigo-600 p-2 rounded-xl shadow-md" type="number" placeholder="length" bind:value={length}>
        <button class="bg-indigo-600 p-2 rounded-xl shadow-md" onclick={() => binary = getRandom(length)}>Get New Binary</button>
    </div>
</div>