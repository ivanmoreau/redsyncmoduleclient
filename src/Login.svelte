<script lang="ts">

    import browser from "webextension-polyfill";
    import { get_key_fetch_token, get_creds } from "../../redsyncmodulelib/pkg/redsyncmodulelib";
    import init from "../../redsyncmodulelib/pkg/redsyncmodulelib";
    import axios from "axios";

    export let change: () => void;

    let load = async (x) => {
        await init()
    };
    load("./redsyncmodulelib_bg.wasm").then(() => {});
    
    let show_inputs = false;
    let wait = false;
    browser.storage.local.get({wait: false}).then((x) => {
		wait = x["wait"];
	}, () => { wait = false; })
    let email = "";
    let password = "";

    let change_show = () => {
        show_inputs = !show_inputs
    };

    let login = () => {
        get_key_fetch_token(email, password).then((x) => {
            if(JSON.parse(x).message) {
                alert(JSON.parse(x).message);
            } else {
                browser.storage.local.set({
			        token: JSON.parse(x),
                    wait: true
		        });
                wait = true
            }
        }, (err) => {alert(err)});
    }

    let complete_login = () => {
        browser.storage.local.get({token: null}).then((x) => {
		    if(x) {
                console.log(x["token"]);
                axios.post("https://proyecto.ivmoreau.com/login2", JSON.parse(JSON.stringify(x["token"])))
                .then(y => {
                    browser.storage.local.set({
			            creds: y.data
		            });
                browser.storage.local.remove("token");
                wait = false;
                browser.storage.local.set({
			        logged: true
		        });
                change();
                });
            }
	    }, (err) => {alert(err)});
    }
</script>


<div id="container">
    
    {#if show_inputs == false && wait == false}
    <button class="loginb" on:click={change_show}>Login</button>
    {:else if show_inputs == true && wait == false}

    <div>
        Email
        <input type="email" bind:value={email}>
    </div>

    <div>
        password
        <input type="password" bind:value={password}>
    </div>

    <br>
    <br>

    <button class="loginb" on:click={login}>Login</button>
    <button class="loginb" on:click={change_show}>Back</button>
    {:else}

    <h2>Please allow access in your email, and then click done.</h2>
    <button class="loginb" on:click={complete_login}>Done</button>
    {/if}

</div>

<style>
    #container {
        direction: rtl;
        color: white;
        display: flex;
        flex-direction: column;
        height: 100%;
    }
    button.loginb {
		background-color: #bfbfbf;
    	color: #616161;
    	font-weight: 800;
    	outline: none;
    	height: 30px;
    	width: 110px;
    	border-radius: 10px;
		align-self: center;
        margin: auto;
	}
</style>