<script lang="ts">
</script>

<template>
  <client-only>
    <NavBar />
    <div id="gjMain" class="gj-main"></div>
    <Cookies />
    <Copyright />
  </client-only>
</template>

<script setup lang="ts">
import { onMounted } from 'vue';
import { useSeoMeta, useHead } from '@vueuse/head';
import { useCookie } from '#app'; // Adjust path if necessary

const title = "Guajava | Home";
const description = "A community based Payroll Management System";

useSeoMeta({
  title: () => title,
  description: () => description,
  charset: "utf-8",
  viewport: "width=device-width, initial-scale=1.0"
});

useHead({
  link: [
    { rel: 'icon', type: 'image/png', href: '/logo.png' },
    { rel: 'stylesheet', href: '/reset.css' },
    { rel: 'stylesheet', href: '/custom.css' },
    { rel: 'preconnect', href: 'https://fonts.googleapis.com' },
    { rel: 'preconnect', href: 'https://fonts.gstatic.com', crossorigin: '' },
    {
      rel: 'stylesheet',
      href: 'https://fonts.googleapis.com/css2?family=Bungee+Spice&family=Roboto+Condensed:ital,wght@0,100..900;1,100..900&display=swap'
    }
  ]
});

const globalDelay = 500;
const allowCookies = useCookie<boolean>("allowCookies", {
  sameSite: "none",
  secure: true,
  maxAge: 60 * 60 * 24,
});

allowCookies.value = allowCookies.value || false;

async function loadModal() {
  const cookieModal = document.getElementById("gjCookieModal") as HTMLDivElement | null;
  const cookieBox = document.getElementById("gjCookieBox") as HTMLDivElement | null;
  const cookieX = document.getElementById("gjCookieX") as HTMLDivElement | null;
  const cookieOK = document.getElementById("gjCookieOK") as HTMLDivElement | null;

  if (!cookieModal || !cookieBox || !cookieX || !cookieOK) return;

  if (allowCookies.value === true) {
    allowAllCookies();
    return;
  }

  showCookiePopup();

  cookieX.addEventListener("click", () => {
    showCookiePopup(false);
  });

  cookieOK.addEventListener("click", () => {
    allowAllCookies();
  });
}

async function showCookiePopup(show: boolean = true) {
  const cookieBox = document.getElementById("gjCookieBox") as HTMLDivElement | null;
  const cookieModal = document.getElementById("gjCookieModal") as HTMLDivElement | null;

  if (!cookieBox || !cookieModal) {
    setTimeout(() => showCookiePopup(show), 50);
    return;
  }

  if (!show) {
    cookieBox.classList.add("gj-hidden");
    cookieModal.classList.add("gj-hidden");
    return;
  }

  cookieBox.classList.remove("gj-hidden");
  cookieModal.classList.remove("gj-hidden");
}

let lastCookieClicked = 0;

async function allowAllCookies() {
  if (lastCookieClicked >= Date.now() - globalDelay) return;
  lastCookieClicked = Date.now();
  allowCookies.value = true;
  showCookiePopup(false);
}

onMounted(() => {
  setTimeout(() => {
    loadModal();
  }, 1);
});
</script>

<style scoped></style>
<style></style>
