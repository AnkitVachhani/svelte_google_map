<script>
  import loader from "@beyonk/async-script-loader";
  import { onMount, createEventDispatcher } from "svelte";
  import { mapsLoaded, mapsLoading } from "../lib/stores";
  const dispatch = createEventDispatcher();
  export let apiKey;

  $: $mapsLoaded && dispatch("ready");
  onMount(() => {
    // @ts-ignore
    window.byGmapsReady = () => {
      mapsLoaded.set(true);
      // @ts-ignore
      delete window.byGmapsReady;
    };
    if ($mapsLoaded) {
      dispatch("ready");
    }
    if (!$mapsLoading) {
      const url = [
        "//maps.googleapis.com/maps/api/js?",
        apiKey ? `key=${apiKey}&` : "",
        "libraries=places&callback=byGmapsReady",
      ].join("");
      mapsLoading.set(true);
      loader(
        [{ type: "script", url }],
        () => {
          return $mapsLoaded;
        },
        () => {}
      );
    }
  });
</script>
