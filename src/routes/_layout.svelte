<script>
  import { onMount } from "svelte";

  export let segment;

  onMount(() => {
    mapboxgl.accessToken = process.env.MAPBOX_ACCESS_TOKEN;
  });
</script>

<style>

</style>

<slot />
