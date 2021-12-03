<script>
    import browser from "webextension-polyfill";
import App from "./App.svelte";

    let checkbox_mark = false;
	let checkbox_hist = false;

	browser.storage.local.get({checkbox_mark: false}).then((x) => {
		checkbox_mark = x["checkbox_mark"];
	}, () => { checkbox_mark = false; })
	browser.storage.local.get({checkbox_hist: false}).then((x) => {
		checkbox_hist = x["checkbox_hist"];
	}, () => { checkbox_hist = false; })
</script>

<div id="container">
    <h2>Settings</h2>

    <label> <input type="checkbox" bind:checked={checkbox_mark}> Bookmarks </label>

    <label> <input type="checkbox" bind:checked={checkbox_hist}>  History </label>

    <br/><br/>

    <div>
        <a href=" " on:click={async () => await browser.storage.local.set({
            show_settings: false
        })}> Back </a>
        <a href=" " on:click={async () => await browser.storage.local.set({
            show_settings: false,
            checkbox_mark: checkbox_mark,
            checkbox_hist: checkbox_hist
        })}> Accept </a>
    </div>

</div>

<style>
    #container {
        direction: rtl;
        color: white;
        display: flex;
        flex-direction: column;
    }
</style>