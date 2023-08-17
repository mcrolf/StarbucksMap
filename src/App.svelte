<script lang="ts">

  import ChroplethMap from "./components/ChoroplethMap.svelte";

  let datasets = [
    {label: "Dunkin' Donuts", url: "/data/dunkin_donuts_location_counts.csv"},
    {label: "Starbucks", url: "/data/starbucks_locations_counts.csv"}
  ];

  let colors = ["#ea4f19", "#b15222", "#75542a", "#3c5733", "#005739"];

</script>

<main>

  <ChroplethMap {datasets} {colors}/>

</main>

<style>

  main {
    font-family: "Arial";
    display: flex;
    justify-content: center;
  }

</style>
