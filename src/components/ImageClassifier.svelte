<script context="module">
	let classifier = ml5.imageClassifier("MobileNet");

	export function classify(image) {
		return new Promise(function (resolve, reject) {
			classifier.classify(image, (error, results) => {
				if (error) {
					reject(error);
				} else {
					resolve(results);
				}
			});
		});
	}
</script>