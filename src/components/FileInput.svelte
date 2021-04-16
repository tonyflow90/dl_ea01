<script>
    import { createEventDispatcher } from "svelte";

    // Props
    export let file;
    export let error = null;
    export let text;

    const dispatch = createEventDispatcher();

    let readFile = (file) => {
        return new Promise(function (resolve, reject) {
            let reader = new FileReader();

            reader.onload = (evt) => {
                if (evt.target.readyState == FileReader.DONE) {
                    resolve(evt.target.result);
                }
            };
            reader.readAsDataURL(file);
        });
    };

    let handleFileInput = (evt) => {
        if (evt.target.files.length != 1) return;

        let input = evt.target.files[0];

        if (input.type != "image/jpeg" && input.type != "image/png") {
            error = new Error(
                "Fehler! Die ausgewählt Datei ist kein Bild oder entspricht nicht dem Format JPEG oder PNG!"
            );
            dispatch("error", error);
            evt.target.value = "";
            return;
        }

        readFile(input)
            .then((s) => {
                let nImage = new Image();
                nImage.alt = input.name;
                nImage.src = s;
                file = nImage;

                dispatch(ChannelMergerNode, file);
            })
            .catch((e) => {
                console.log(e);
            });
    };
</script>

<div class="container">
    <input type="file" id="fileInput" on:change={handleFileInput} />
    {#if file}
        <img class="image selected" src={file.src} alt={file.alt} />
    {:else}
        <span class="material-icons md-48 icon">upload_file</span>
        <p>{text}</p>
    {/if}
</div>

<style>
    .material-icons.md-48 {
        font-size: 48px;
    }

    input[type="file"] {
        position: absolute;
        opacity: 0;
        max-width: 400px;
        max-height: 400px;
        min-width: 180px;
        min-height: 180px;
        width: 100vw;
        height: 100vw;
    }

    img {
        max-width: 400px;
        max-height: 400px;
        min-width: 180px;
        min-height: 180px;
        width: 100vw;
        height: 100vw;
    }

    .container {
        max-width: 400px;
        max-height: 400px;
        min-width: 180px;
        min-height: 180px;
        width: 100vw;
        height: 100vw;
        display: flex;
        flex-direction: column;
        padding: 20px;
        align-items: center;
        justify-content: center;
        text-align: center;
        background-color: var(--color-secondary-500);
    }
</style>
