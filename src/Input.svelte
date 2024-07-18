<script>
    export let inputValue = "";
    </script>

<input type="text" placeholder="Search...🔍" autocomplete="off" id="searchInput" bind:value={inputValue} on:input>
<span id="search-icon"></span>