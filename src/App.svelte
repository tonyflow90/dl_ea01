<script>
	// ui
	import "smelte/src/tailwind.css";
	import { Tooltip, Snackbar, Button } from "smelte";

	import FileInput from "./components/FileInput.svelte";
	import ImageClassifier, {
		classify,
	} from "./components/ImageClassifier.svelte";
	import BarChart from "./components/BarChart.svelte";

	// Props
	let selectedImage = undefined;
	let showSnackbar = false;
	let snackbarTimeout = 2000;
	let result;

	// Data
	let images = [
		{ src: "./images/penguin.jpg", name: "penguin" },
		{ src: "./images/maltese.jpg", name: "maltese" },
		{ src: "./images/espresso.jpg", name: "espresso" },
		{ src: "./images/gecko.jpg", name: "gecko" },
		{ src: "./images/stopwatch.jpg", name: "stopwatch" },
	];

	// Texts & Labels
	let imageContainerText =
		"Beispielbild auswählen oder Bild hierher ziehen & ablegen";
	let taskTitle = "Bilderkennung mit ml5";
	let taskNumber = 1;
	let snackbarColor = "success";
	let message = "";

	let onSelectPreviewImage = (evt) => {
		let image = evt.target;
		selectedImage = image;
	};

	let onClassify = () => {
		if (selectedImage) {
			classify(selectedImage)
				.then((res) => {
					result.image = selectedImage;
					result.items = res;
					showSnackbar = true;
					message = "Klassifizierung erfoglreich.";
					snackbarColor = "success";
					_clearInput();
				})
				.catch((error) => {
					showSnackbar = true;
					message = "Fehler! " + error;
					snackbarColor = "error";
				});
		} else {
			showSnackbar = true;
			message = "Fehler! Es wurde kein Bild ausgewählt!";
			snackbarColor = "error";
		}
	};

	let onError = (e) => {
		showSnackbar = true;
		message = e.detail.message;
		snackbarColor = "error";
	};

	let onReset = () => {
		_clearInput();
	};

	let _clearInput = () => {
		selectedImage = undefined;
	};

	let _clearResult = () => {
		result = _initResult();
	};

	let _initResult = () => {
		return { image: undefined, items: undefined };
	};

	result = _initResult();
</script>

<app>
	<ImageClassifier />

	<header>
		<h5>Einsendeaufgabe {taskNumber}</h5>
		<h3>{taskTitle}</h3>
	</header>

	<main>
		<FileInput
			bind:file={selectedImage}
			text={imageContainerText}
			on:error={onError}
		/>

		{#if !selectedImage}
			<div class="images">
				{#each images as image, i}
					<img
						class="image preview"
						src={image.src}
						alt={image.name}
						on:click={onSelectPreviewImage}
					/>
				{/each}
			</div>
		{:else}
			<div class="buttons">
				<Tooltip>
					<div slot="activator">
						<Button on:click={onClassify}>klassifizieren</Button>
					</div>
					Ausgewähltes Bild klassifizieren.
				</Tooltip>

				<Tooltip>
					<div slot="activator">
						<Button
							color="secondary"
							light
							block
							outlined
							on:click={onReset}>zurücksetzen</Button
						>
					</div>
					Eingaben zurücksetzen.
				</Tooltip>
			</div>
		{/if}

		<div class="results">
			<BarChart image={result.image} data={result.items} />
		</div>

		<Snackbar
			bind:value={showSnackbar}
			noAction
			color={snackbarColor}
			timeout={snackbarTimeout}
		>
			<div>{message}</div>
			<div slot="action">
				<Button on:click={() => (showSnackbar = false)}>Close</Button>
			</div>
		</Snackbar>
	</main>

	<footer>
		<div>
			<h4>Sources</h4>
			<a href="https://github.com/tonyflow90/dl_ea01">
				<p>Github Repository</p>
			</a>
			<a href="https://ml5js.org/">
				<p>ml5js</p>
			</a>
			<a href="https://developers.google.com/chart/interactive/docs">
				<p>Google Charts</p>
			</a>

			<a
				href="https://github.com/ml5js/ml5-library/blob/main/src/utils/IMAGENET_CLASSES.js"
			>
				<p>ml5js IMAGENET_CLASSES</p>
			</a>
		</div>

		<div>
			<h4>Images</h4>
			<a
				href="https://www.pexels.com/de-de/foto/schwarzweiss-pinguin-der-auf-sand-geht-2078474/"
			>
				<p>penguin</p>
			</a>
			<a
				href="https://www.pexels.com/de-de/foto/meer-natur-ferien-sand-6528245/"
			>
				<p>maltese</p>
			</a>
			<a
				href="https://www.pexels.com/de-de/foto/koffein-kaffee-espresso-schwarzer-kaffee-1233528/"
			>
				<p>espresso</p>
			</a>
			<a
				href="https://www.pexels.com/de-de/foto/grunes-chamaleon-auf-schwarzer-oberflache-3779926/"
			>
				<p>gecko</p>
			</a>
			<a
				href="https://www.pexels.com/de-de/foto/vintage-zeit-uhr-timer-5563414/"
			>
				<p>stopwatch</p>
			</a>
		</div>
	</footer>
</app>

<style>
	.image {
		width: 50px;
		height: 50px;
		margin: 4px;
		border-radius: 0.7rem;
	}

	.images {
		max-width: 400px;
		min-width: 180px;
		width: 100vw;
		display: grid;
		justify-items: center;
		grid-template-columns: repeat(auto-fit, minmax(50px, 1fr));
		padding: 20px;
	}

	.buttons {
		max-width: 400px;
		min-width: 180px;
		width: 100vw;
		display: grid;
		justify-items: center;
		grid-template-columns: repeat(auto-fit, minmax(50px, 1fr));
		padding: 20px;
	}

	.buttons * {
		padding: 0 5px;
	}

	.results {
		display: flex;
		padding-top: 20px;
	}

	header {
		min-height: 20vh;
		display: flex;
		flex-direction: column;
		align-items: center;
		text-align: center;
		padding: 10px 0;
		background-color: var(--color-primary-500);
	}

	main {
		min-height: 55vh;
		display: flex;
		flex-direction: column;
		align-items: center;
		padding: 20px 0;
		background-color: var(--color-white-500);
	}

	footer {
		min-height: 25vh;
		background-color: var(--color-secondary-700);
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
		text-align: center;
		padding: 1rem;
		bottom: 0;
	}

	p,
	h3,
	h4,
	h5 {
		color: var(--color-white-500);
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
