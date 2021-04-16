<script>
    export let image;
    export let data;
    let showresult = 'none';

    let draw = (data) => {
        if (!data) {
            showresult = 'none';
            return;
        }

        showresult = 'flex';

        let adata = [];
        data.forEach((e) => {
            adata.push(Object.values(e));
        });
        var gdata = google.visualization.arrayToDataTable([
            ["Element", "Percentage"],
            ...adata,
        ]);

        var options = {
            chartArea: { width: "50%" },
        };

        var options = {
            title: "Auswertung Bilderkennung",
            width: 400,
            height: 400,
            chartArea: { width: "50%" },
            bar: { groupWidth: "95%" },
            legend: { position: "none" },
            hAxis: {
                minValue: 0,
                maxValue: 1,
            },
        };
        var chart = new google.visualization.BarChart(
            document.getElementById("chart")
        );

        chart.draw(gdata, options);
    };

    google.charts.load("current", { packages: ["corechart", "bar"] });
    google.charts.setOnLoadCallback(draw);

    $: {
        draw(data);
    }
</script>

<main showresult={showresult}>
    <h3>Ergebnis</h3>
    {#if image}
        <img class="image" src={image.src} alt={image.name} />
    {/if}

    <div class="chart" id="chart" />

    {#if !data}
        <p>Keine Daten.</p>
    {/if}
</main>

<style>
    main[showresult=none] {
        display: none;
    }

    main[showresult=flex] {
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 1em;
        margin: 0 auto;
    }

    .image {
        height: 400px;
        width: 400px;
    }

    .chart {
        height: 400px;
        width: 400px;
    }
</style>
