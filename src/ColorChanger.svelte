<script>
  import { getContext } from "svelte";
  import { key } from './config.js';

  const { getMap } = getContext(key);
  const map = getMap();

  export let fillcolor;

  map.on("idle", function () {
    map.setPaintProperty('indicator','fill-color', fillcolor);
    map.setPaintProperty('indicator','fill-opacity', 0.2);
  });

</script>

