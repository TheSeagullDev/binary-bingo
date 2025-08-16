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

    let binary = $state("0000000");
    let length = $state(8);
</script>

<h1>Binary Bingo!</h1>
<p>{binary}</p>
<input type="number" placeholder="length" bind:value={length}>
<input type="button" value="Get New Binary" onclick={() => binary = getRandom(length)}>