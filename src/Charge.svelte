<script>
  import {shields} from "./dataModel.js";
  import {loadedCharges} from "./stores";
  import {drag, transform} from "./drag"
  export let coa, charge, i, shield, colors, t;

  $: shieldPositions = shields[shield] || shields.spanish;
  $: positions = [...new Set(charge.p)].filter(p => shieldPositions[p]);

  const defs = document.getElementById("charges");
  function getCharge(charge) {
    if ($loadedCharges[charge] || defs.querySelector("#"+charge)) return charge;
    $loadedCharges[charge] = 1;

    fetch("charges/"+charge+".svg").then(response => response.text()).then(text => {
      const el = document.createElement("html");
      el.innerHTML = text;
      defs.insertAdjacentHTML("beforeend", el.querySelector("g").outerHTML);
    });
    return charge;
  }

  function getElTransform(shieldPositions, c, p) {
    const [x, y] = shieldPositions[p];
    const s = c.size || 1;
    const scale = c.sinister || c.reversed ? `${c.sinister ? "-" : ""}${s}, ${c.reversed ? "-" : ""}${s}` : s;
    return `translate(${x} ${y}) scale(${scale})`;
  }
</script>

<g class="charge" {i} charge={getCharge(charge.charge)} transform={transform(charge)} transform-origin="center" stroke="#000" on:mousedown={function(e) {drag(e, charge, coa)}}>
  {#each positions as p}
    <use href="#{charge.charge}" transform={getElTransform(shieldPositions, charge, p)} transform-origin="center" fill="{colors[t]}"></use>
  {/each}
</g>