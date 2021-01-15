<script lang="ts">
  import L from "leaflet";
  import ThrowDartButton from "./ThrowDartButton.svelte";
  import { dartIcon } from "./dartIcon";

  let map;
  let marker;
  const initialView = [0, 0];

  function createMap(container) {
    const m = L.map(container, { preferCanvas: true }).setView(initialView, 3);
    L.tileLayer(
      "https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png",
      {
        attribution: `&copy;<a href="https://www.openstreetmap.org/copyright" target="_blank">OpenStreetMap</a>,
	        &copy;<a href="https://carto.com/attributions" target="_blank">CARTO</a>`,
        maxZoom: 14,
      }
    ).addTo(m);

    return m;
  }

  const dart = L.control({ position: "bottomleft" });
  let dartComponent;
  dart.onAdd = (map) => {
    let div = L.DomUtil.create("div");
    dartComponent = new ThrowDartButton({
      target: div,
      props: {},
    });

    dartComponent.$on("throw-dart", ({ detail: { coordinates } }) => {
      map.setView(coordinates, 5, { animate: true });
      if (marker) map.removeLayer(marker);
      marker = L.marker(coordinates, {
        icon: createDartIcon(),
      });
      marker.addTo(map);
    });

    return div;
  };

  dart.onRemove = () => {
    if (dartComponent) {
      dartComponent.$destroy();
      dartComponent = null;
    }
  };

  function mapAction(container) {
    map = createMap(container);
    dart.addTo(map);

    return {
      destroy: () => {
        map.remove();
        dart.remove();
        map = null;
      },
    };
  }

  function resizeMap() {
    if (map) map.invalidateSize();
  }

  function createDartIcon() {
    let html = `<div class="map-marker">${dartIcon}</div>`;
    return L.divIcon({
      html,
      className: "map-marker",
    });
  }
</script>

<svelte:window on:resize={resizeMap} />
<link
  rel="stylesheet"
  href="https://unpkg.com/leaflet@1.6.0/dist/leaflet.css"
  integrity="sha512-xwE/Az9zrjBIphAcBb3F6JVqxf46+CDLwfLMHloNu6KEQCAWi6HcDUbeOfBIptF7tcCzusKFjFw2yuvEpDL9wQ=="
  crossorigin=""
/>
<div class="map" style="height:100%;width:100%" use:mapAction />

<style>
  .map :global(.marker-text) {
    width: 100%;
    text-align: center;
    font-weight: 600;
    background-color: #444;
    color: #eee;
    border-radius: 0.5rem;
  }

  .map :global(.map-marker) {
    width: 30px;
    transform: translateX(-50%) translateY(-25%);
  }
</style>
