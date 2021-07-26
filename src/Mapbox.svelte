<script>
  import { onMount, setContext } from "svelte";
  import { mapboxgl, key } from "./config.js";

  const lightStyle =
    "mapbox://styles/robsalasco/ckphuacg002mz18pkga2rktq2?optimize=true";

  export let map = null;

  setContext(key, {
    getMap: () => map,
  });

  let container;

  onMount(() => {
    map = new mapboxgl.Map({
      container: container,
      style: lightStyle,
      zoom: 10,
      center: [-70.648956, -33.450349],
      attributionControl: false
    });

    map.addControl(new mapboxgl.AttributionControl({
      compact: true
    }));

    map.on("load", function () {
      map.addSource("h3-data", {
        type: "vector",
        tiles: [
          "https://mvt.mtnlss.co/maps/gpkg_hexagons/{z}/{x}/{y}.vector.pbf",
        ],
        minzoom: 10,
        maxzoom: 14,
      });

      map.addLayer({
        id: "indicator",
        type: "fill",
        source: "h3-data",
        "source-layer": "map_h3",
        paint: {
          "fill-color": "rgba(0, 0, 0, 0)",
          "fill-opacity": 0,
        },
      });
    });
  });
</script>

<div bind:this={container}>
  {#if map}
    <slot />
  {/if}
</div>

<style>
  div {
    height: 700px;
  }
</style>
