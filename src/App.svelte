<script lang="ts">
  import { Styles, Col, Container, Row } from "sveltestrap";
  import { varEnv } from "./lib/env";
  import GooglePlacesAutocomplete from "./components/GooglePlacesAutocomplete.svelte";
  import GoogleMap from "./components/GoogleMap.svelte";
  import RangeSlider from "svelte-range-slider-pips";

  let lat;
  let lng;
  let center;
  let mapComponent;
  let range = [10];

  const placeChanged = (data) => {
    console.log(`Address: ${data?.detail?.place?.formatted_address}`);
    lat = data?.detail?.place?.geometry?.location?.lat();
    lng = data?.detail?.place?.geometry?.location?.lng();
    console.log(`Lat: ${lat}`);
    console.log(`Lng: ${lng}`);
    if (mapComponent) {
      mapComponent.setCentre(data?.detail?.place?.geometry?.location);
    }
  };

  const updateAddressCoordinates = ({ location }) => {
    center = { lat: location.lat(), lng: location.lng() };
  };

  const changeRadius = () => {
    if (mapComponent) {
      mapComponent.setRadius(range[0]);
    }
  };
</script>

<main>
  <Styles />
  <Container class="pt-5 pb-5">
    <Row>
      <Col>
        <GooglePlacesAutocomplete
          apiKey={varEnv.google_map_api_key}
          on:placeChanged={placeChanged}
        />
        <div class="mt-5 pt-2">
          <RangeSlider
            min={5}
            max={65}
            suffix="KM"
            pips
            float={true}
            first="label"
            last="label"
            rest={false}
            bind:values={range}
            on:change={changeRadius}
          />
        </div>
      </Col>
      <Col>
        {#if lat && lng}
          <GoogleMap
            bind:this={mapComponent}
            zoom={8}
            radius={range[0]}
            apiKey={varEnv.google_map_api_key}
            center={{ lat: lat, lng: lng }}
            on:recentre={(e) => updateAddressCoordinates(e.detail)}
          />
        {/if}
      </Col>
    </Row>
  </Container>
</main>

<style>
</style>
