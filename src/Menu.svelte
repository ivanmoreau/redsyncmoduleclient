<script>
    import browser from "webextension-polyfill";
    import axios from "axios";

    async function handleCreated(id, bookmarkInfo) {
        let time = Date.now().toString();
        let tt = btoa(time).substring(0, 12);
        let structure = {
            id: tt,
            bmkUri: bookmarkInfo.url,
            title: bookmarkInfo.title
        }
        console.log(structure)
        let res = await browser.storage.local.get("creds");
        axios.post("https://proyecto.ivmoreau.com/upItemsCollection", {
            creds: res,
            collection: "bookmarks",
            payload: [structure]
        } )
        .then(x => {
            console.log(x);
        });
    }

    async function handleCreatedHist(item) {
        let structure = {
            histUri: item.url,
            title: item.title
        }
        console.log(structure)
        let res = await browser.storage.local.get("creds");
        axios.post("https://proyecto.ivmoreau.com/upItemsCollection", {
            creds: res,
            collection: "history",
            payload: [structure]
        } )
        .then(x => {
            console.log(x);
        });
    }

    let syncBook = async () => {
        let res = await browser.storage.local.get("creds");
        let res2 = await axios.post("https://proyecto.ivmoreau.com/getCollection", {
            creds: res,
            collection: "bookmarks"
        });
        await res2.data.forEach(async element => {
            if (element.payload.type == "bookmark") {
                console.log(element.payload)
                let s = {
                    url: element.payload.bmkUri,
                    title: element.payload.title
                }
                let k = await browser.bookmarks.create(s);
            }
        });
    }

    let syncHist = async () => {
        let res = await browser.storage.local.get("creds");
        let res2 = await axios.post("https://proyecto.ivmoreau.com/getCollection", {
            creds: res,
            collection: "history"
        });
        await res2.data.forEach(async element => {
            let s = {
                url: element.payload.histUri
            }
            let k = await browser.history.addUrl(s);
        });
    }

    let handleSyncs = async () => {
        let checkbox_mark = (await browser.storage.local.get({checkbox_mark: false})) ["checkbox_mark"];
        let checkbox_hist = (await browser.storage.local.get({checkbox_hist: false})) ["checkbox_hist"];
        console.log(checkbox_mark)
        console.log(checkbox_hist)
        if(checkbox_mark == true){
            await syncBook();
        }
        if(checkbox_hist == true){
            await syncHist();
        }
    };

    browser.bookmarks.onCreated.addListener(handleCreated);
    browser.history.onVisited.addListener(handleCreatedHist);
</script>

<div id="container">
    <h2>Welcome</h2>

    <!-- svelte-ignore a11y-missing-attribute -->
    <a on:click={async () => await handleSyncs()}> Sync now </a>

    <!-- svelte-ignore a11y-missing-attribute -->
    <a href=" " on:click={async () => await browser.storage.local.set({
        show_settings: true
    })}> Settings </a>

    <br/><br/>

    <!-- svelte-ignore a11y-missing-attribute -->
    <a on:click={async () => await browser.storage.local.clear()}> Logout </a>

</div>

<style>
    #container {
        direction: rtl;
        color: white;
        display: flex;
        flex-direction: column;
    }
</style>