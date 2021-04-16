<script>
    export let image;
    export let data;
    let showresult = "none";

    let draw = (data) => {
        if (!data) {
            showresult = "none";
            return;
        }
        showresult = "flex";

        let confidence = 0;
        for (const [i, el] of data.entries()) {
            confidence += el.confidence;
        }

        let adata = [];
        data.forEach((e) => {
            adata.push(Object.values(e));
        });
        adata.push(["andere", 1 - confidence]);

        var gdata = new google.visualization.DataTable();
        gdata.addColumn("string", "Name");
        gdata.addColumn("number", "Percentage");
        gdata.addRows(adata);

        var options = {
            title: "Auswertung Bilderkennung",
            width: 320,
            height: 200,
            colors: ["#00c853", "#2962ff", "#d50000", "#9e9e9e"],
            is3D: true,
        };

        var chart_pie = new google.visualization.PieChart(
            document.getElementById("chart_pie")
        );
        chart_pie.draw(gdata, options);
    };

    google.charts.load("current", { packages: ["corechart", "bar"] });
    google.charts.setOnLoadCallback(draw);

    $: {
        draw(data);
    }
</script>

<main {showresult}>
    <h5>Ergebnis</h5>
    {#if image}
        <img class="image" src={image.src} alt={image.name} />
    {/if}

    <div class="chart" id="chart_pie" />
    <!-- <div class="chart" id="chart_bar" /> -->

    {#if !data}
        <p>Keine Daten.</p>
    {/if}
</main>

<style>
    main[showresult="none"] {
        display: none;
    }

    main[showresult="flex"] {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .image {
        border-radius: 25%;
        height: 200px;
        width: 200px;
    }

    .chart {
        height: 200px;
        width: 320px;
    }
</style>
