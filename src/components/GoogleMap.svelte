<script>
  import GoogleSdk from "./GoogleSdk.svelte";
  import { createEventDispatcher, setContext } from "svelte";
  import { key } from "../lib/contexts";
  setContext(key, {
    getMap: () => map,
    getGoogleMap: () => mapElement,
  });
  const dispatch = createEventDispatcher();
  export let apiKey;
  let mapElement;
  let map;
  let marker;
  let circle;

  export let center = null;
  export let zoom = 8;
  export let radius = 8;
  export let options = {};

  export function getDomBounds() {
    return mapElement.getBoundingClientRect();
  }
  export function getDefaultView() {
    return mapElement.ownerDocument.defaultView;
  }
  export function setHeight(height) {
    mapElement.style.height = height;
  }
  export function setMaxHeight(height) {
    mapElement.style.maxHeight = height;
  }
  export function setCentre(location) {
    map.setCenter(location);
    if (marker) {
      // @ts-ignore
      marker.setPosition(new window.google.maps.LatLng(location.lat(), location.lng()));
    }
    if (circle) {
      circle.setCenter(location);
    }
  }
  export function setRadius(radius) {
    if (circle) {
      circle.setRadius(radius * 1000);
    }
  }

  function initialise() {
    setTimeout(() => {
      // @ts-ignore
      const google = window.google;
      map = new google.maps.Map(
        mapElement,
        Object.assign(
          {
            center,
            zoom,
          },
          options
        )
      );
      marker = new google.maps.Marker({
        position: new google.maps.LatLng(center.lat, center.lng),
        map: map,
        animation: google.maps.Animation.DROP,
      });

      // Add circle overlay and bind to marker
      circle = new google.maps.Circle({
        strokeColor: "#0000ff",
        strokeOpacity: 0.8,
        strokeWeight: 2,
        fillColor: "#0000ff",
        fillOpacity: 0.35,
        map,
        center: center,
        radius: radius * 1000,
      });
      dispatch("ready");
    }, 1);
  }
</script>

<GoogleSdk {apiKey} on:ready={initialise} />
<div class="map" bind:this={mapElement}>
  {#if map}
    <slot />
  {/if}
</div>

<style>
  .map {
    height: 500px;
    width: 100%;
  }
</style>
