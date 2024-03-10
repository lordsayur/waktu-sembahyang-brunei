<script setup>
import { greet } from "@wsb/core";

const { $pwa } = useNuxtApp();

const text = ref("");
const needRefresh = computed(() => $pwa?.needRefresh);

const notificationPermission = ref();

onMounted(() => {
  if ($pwa.offlineReady) console.log("App ready to work offline");
});

async function subscribe() {
  if ("Notification" in window && navigator.serviceWorker) {
    try {
      const permission = await Notification.requestPermission();
      notificationPermission.value = permission;
      if (permission === "granted") {
        notify("Notification Enabled", "Notification is now enabled");
      }
    } catch (error) {
      console.error(error);
    }
  } else {
    alert("Web notifications are not supported in your browser.");
  }
}

function notify(title, body) {
  const registration = $pwa.getSWRegistration();

  const payload = {
    body,
  };

  if ("showNotification" in registration) {
    registration.showNotification(title, payload);
  } else {
    new Notification(title, payload);
  }
}

async function fetch() {
  const { data } = await useFetch(
    "https://corporatebs-generator.sameerkumar.website/"
  );

  text.value = data.value.phrase;
}

function reloadPage() {
  window.location.reload();
}
</script>

<template>
  <NuxtPwaManifest />
  <div>
    <h1>{{ greet("Waktu Sembahyang Brunei") }}</h1>
    <pre> {{ $pwa }}</pre>
    <button @click="reloadPage()">Refresh Page</button>
    <button @click="fetch">Refresh Text</button>
    <pre>{{ text }}</pre>

    <button
      v-if="notificationPermission !== 'granted'"
      type="button"
      @click="subscribe"
    >
      Subscribe
    </button>
    <button
      v-else
      type="button"
      @click="notify('Azan', 'Sudah masuk waktu Zuhur')"
    >
      Notify
    </button>
  </div>
  <div v-show="needRefresh">
    <span> New content available, click on reload button to update. </span>

    <button @click="$pwa.updateServiceWorker()">Reload</button>
  </div>
</template>
