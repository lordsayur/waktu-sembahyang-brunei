<script setup>
import { greet } from "@wsb/core";

const { $pwa } = useNuxtApp();

const text = ref("");
const isPWAInstalled = computed(() => $pwa?.isPWAInstalled);
const needRefresh = computed(() => $pwa?.needRefresh);

onMounted(() => {
  if ($pwa.offlineReady) alert("App ready to work offline");
});

async function fetch() {
  const { data } = await useFetch(
    "https://corporatebs-generator.sameerkumar.website/"
  );

  text.value = data.value.phrase;
}
</script>

<template>
  <NuxtPwaManifest />
  <div>
    <h1>{{ greet("Waktu Sembahyang Brunei") }}</h1>
    <p>PWA Installed: {{ isPWAInstalled }}</p>
    <button @click="window.reload()">Refresh Page</button>
    <button @click="fetch">Refresh Text</button>
    <pre>{{ text }}</pre>
  </div>
  <div v-show="needRefresh">
    <span> New content available, click on reload button to update. </span>

    <button @click="$pwa.updateServiceWorker()">Reload</button>
  </div>
</template>
