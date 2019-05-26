<script>
  import { onMount } from "svelte";
  import { stores } from "@sapper/app";

  const { preloading, page, session } = stores();

  let container;
  let map;
  let style;
  let center;
  let zoom;

  $: hash =
    center && zoom
      ? `${center.lat.toFixed(5)}/${center.lng.toFixed(5)}/${zoom.toFixed(2)}`
      : "";

  const { params } = $page;

  onMount(() => {
    if (window.location.hash !== "") {
      const [lat, lng, z] = location.hash.slice(1).split("/");
      center = { lat: +lat, lng: +lng };
      zoom = +z;
    }

    const options = {
      container,
      style: `mapbox://styles/${params.username}/${params.style}`
    };

    if (center && zoom) {
      options.center = center;
      options.zoom = zoom;
    }

    map = new mapboxgl.Map(options);

    map.addControl(
      new MapboxGeocoder({
        accessToken: mapboxgl.accessToken,
        mapboxgl
      })
    );

    map.on("styledata", e => {
      style = map.getStyle();
    });

    map.on("moveend", e => {
      center = map.getCenter();
      zoom = map.getZoom();
      window.location.replace(
        `${window.location.origin}${window.location.pathname}#${hash}`
      );
    });

    window.map = map;

    return () => map.remove();
  });
</script>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
  }
  #map {
    position: absolute;
    top: 0;
    bottom: 0;
    width: 100%;
  }
</style>

<svelte:head>
  <title>{style ? style.name : 'Map Viewer'}</title>
</svelte:head>

<div id="map" bind:this={container} />
