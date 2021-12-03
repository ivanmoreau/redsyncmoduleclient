<script lang="ts">
	import browser, { userScripts } from "webextension-polyfill";
	import Login from './Login.svelte'
	import Menu from './Menu.svelte'
	import Settings from './Settings.svelte'

	let logged = false;
	let show_settings = false;
	browser.storage.local.get({logged: false}).then((x) => {
		logged = x["logged"];
	}, () => { logged = false; })
	browser.storage.local.get({show_settings: false}).then((x) => {
		show_settings = x["show_settings"];
	}, () => { show_settings = false; })
	let image =
		"https://images.unsplash.com/photo-1586074299757-dc655f18518c?fit=crop&w=1268&q=80";

	function change_logged_status() {
		browser.storage.local.set({
			logged: true
		});
		logged = true;
	}
	function change_settings_status() {
		browser.storage.local.set({
			show_settings: true
		});
		show_settings = true;
	}
</script>

<main>
	<h1 id="title">RedPanda <br>
		Sync</h1>
	
	{#if logged == false}
		<Login change={change_logged_status}/>
	{:else if logged == true && show_settings == true}
		<Settings/>
	{:else}
		<Menu/>
	{/if}
</main>

<style>
	#title {
		direction: rtl;
		color: #e46c6c;
		font-family: system-ui;
	}
	main {
		padding: 15px;
		background-color: #616161;
		height: 100%;
		display: flex;
    	flex-direction: column;
	}
	:global(body) {
		margin: 0;
    	padding: 0;
		width: 200px;
    	height: 300px;
	}
	:global(h2) {
        align-self: center;
    }
    :global(a) {
        color: white;
        font-weight: 600;
        padding: 10px;
    }
	:global(input) {
		direction: ltr;
	}
</style>
