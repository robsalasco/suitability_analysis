<script>
  import Mapbox from "./Mapbox.svelte";
  import Layout from "./Layout.svelte";
  import Slider from "@bulatdashiev/svelte-slider";
  import Papa from "papaparse";
  import ColorChanger from "./ColorChanger.svelte";

  let mapComponent;

  let range1 = [100, 100];
  let range2 = [100, 100];
  let range3 = [100, 100];
  let range4 = [100, 100];

  let mounted = true;

  var data;

  let fillcolor;
  
  function run_model(ran1 = 100, ran2 = 100, ran3 = 100, ran4 = 100) {

    let model;
    let dataplot;
    let colorg;

    model = var1.map(
      (x, i) =>
        var1[i] * (ran1 / 100) +
        var2[i] * (ran2 / 100) +
        var3[i] * (ran3 / 100) +
        var4[i] * (ran4 / 100)
      );

      dataplot = h3.map(function(e, i) {
        return {h3:e, value: (model[i]-Math.min(...model))/(Math.max(...model)-Math.min(...model))};
      });
 
      mounted = false
      colorg = ['match',['get', 'h3']];

      for (const row of dataplot) {
        var green = Math.floor(row['value'] * 255);
        var color = 'rgb(0, ' + green + ', 0)';
        colorg.push(row['h3'], color);
      }

      colorg.push('rgba(0, 0, 0, 0)');

      return colorg
  }


//  fillcolor = ["case",["==",["get","h3"],"88b2c5542bfffff"], "red" , "grey" ];
// fillcolor = ['match',['get', 'h3'],'88b2c5542bfffff','blue', 'red'];

  var h3 = [],
    var1 = [],
    var2 = [],
    var3 = [],
    var4 = [];

  Papa.parse(`./data/hexagons.csv`, {
    header: false,
    download: true,
    complete: function (results) {
      data = results.data;
      data.map(function (d) {
        h3.push(d[0]);
        var1.push(parseFloat(d[1]));
        var2.push(parseFloat(d[2]));
        var3.push(parseFloat(d[3]));
        var4.push(parseFloat(d[4]));
      });
    },
  });

  $: {
    if (range1[0]!=100 || range2[0]!=100 || range3[0]!=100 || range4[0]!=100) {
      reCalc(range1 && range2 && range3 && range4);
    } else {
      fillcolor =  "rgba(0, 0, 0, 0)"
    }
  }

  async function reCalc() {

      fillcolor = run_model(range1[0], range2[0], range3[0], range4[0]);

      mounted = false;
      setTimeout(() => mounted = true, 0);
  }

  //fillcolor.push('rgba(0, 0, 0, 0)');

</script>

<Layout>
  <span slot="left">
    <div class="slidecontainer">
      <Slider bind:value={range1}>
        <span style="font-size: 30px;">&#128647;</span>
      </Slider>
    </div>
    <div class="slidecontainer">
      <Slider bind:value={range2}>
        <span style="font-size: 30px;">&#127795;</span>
      </Slider>
    </div>
    <div class="slidecontainer">
      <Slider bind:value={range3}>
        <span style="font-size: 30px;">&#127867;</span>
      </Slider>
    </div>
    <div class="slidecontainer">
      <Slider bind:value={range4}>
        <span style="font-size: 30px;">&#128717;&#65039;</span>
      </Slider>
    </div>
  </span>
  <span slot="right">
    <Mapbox bind:this={mapComponent}>
      {#if mounted}
      <ColorChanger fillcolor={fillcolor} />
      {/if}
    </Mapbox>
</span>
</Layout>

<style>
  .slidecontainer {
    margin-bottom: 40px;
  }
</style>
