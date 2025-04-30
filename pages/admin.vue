<script lang="ts">
</script>

<template>
  <client-only>
    <NavBar :navAdminMode="navAdminMode" />
    <div id="gjMain" class="gj-main">
      <span id="gjSimpleHello" class="gj-simple-hello gj-hidden">
        Hello, {{ fullName }}!
      </span>
    </div>
    <Cookies />
    <Copyright />
  </client-only>
</template>

<script setup lang="ts">
import { onMounted, nextTick, ref } from "vue";
import { useSeoMeta, useHead } from "@vueuse/head";
import { useRouter } from "vue-router";

// Components (make sure they exist or create them)
import NavBar from "@/components/NavBar.vue";
import Cookies from "@/components/Cookies.vue";
import Copyright from "@/components/Copyright.vue";

// Router
const router = useRouter();

// Reactive variables (replace with Pinia/store if needed)
const accessToken = ref("");
const userLevel = ref(0);
const username = ref("");
const fullName = ref("User");
const allowCookies = ref(false);
const retries = ref(0);
const globalDelay = 2000; // e.g., 2 seconds

const navAdminMode = true; // example

// Helper
function getByID<T extends HTMLElement>(id: string): T | null {
  return document.getElementById(id) as T | null;
}

// Meta tags
const title = "guajava";
const description = "A community based Payroll Management System.";

useSeoMeta({
  title: () => title,
  description: () => description,
  charset: "utf-8",
  viewport: "width=device-width, initial-scale=1.0",
});

useHead({
  link: [
    { rel: "icon", type: "image/png", href: "/logo.png" },
    { rel: "stylesheet", href: "/reset.css" },
    { rel: "stylesheet", href: "/custom.css" },
    { rel: "preconnect", href: "https://fonts.googleapis.com" },
    { rel: "preconnect", href: "https://fonts.gstatic.com", crossorigin: "" },
    { rel: "stylesheet", href: "https://fonts.googleapis.com/css2?family=Roboto&display=swap" },
  ],
});

// Logic
function checkAuthorizations() {
  if (!accessToken.value) {
    router.push("/login");
  } else if (userLevel.value <= 2) {
    router.push("/");
  }

  const navBasic = getByID<HTMLDivElement>("gjNavBasic");
  const navAdmin = getByID<HTMLDivElement>("gjNavAdmin");
  const navLogout = getByID<HTMLDivElement>("gjNavAdminLogout");
  const simpleHello = getByID<HTMLSpanElement>("gjSimpleHello");

  if (!navBasic || !navAdmin || !navLogout || !simpleHello) return;

  navLogout.addEventListener("click", () => {
    retries.value = 0;
    username.value = "";
    accessToken.value = "";
    userLevel.value = -1;
    fullName.value = "";
    router.push("/login");
  });

  navBasic.remove();
  navAdmin.classList.remove("gj-hidden");
  simpleHello.classList.remove("gj-hidden");

  loadModal();
}

function loadModal() {
  const cookieModal = getByID<HTMLDivElement>("gjCookieModal");
  const cookieBox = getByID<HTMLDivElement>("gjCookieBox");
  const cookieX = getByID<HTMLDivElement>("gjCookieX");
  const cookieOK = getByID<HTMLDivElement>("gjCookieOK");

  if (!cookieModal || !cookieBox || !cookieX || !cookieOK) return;

  if (allowCookies.value === true) {
    allowAllCookies();
    return;
  }

  showCookiePopup();

  cookieX.addEventListener("click", () => showCookiePopup(false));
  cookieOK.addEventListener("click", () => allowAllCookies());
}

function showCookiePopup(show: boolean = true) {
  const cookieBox = getByID<HTMLDivElement>("gjCookieBox");
  const cookieModal = getByID<HTMLDivElement>("gjCookieModal");

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

function allowAllCookies() {
  if (lastCookieClicked >= Date.now() - globalDelay) return;

  lastCookieClicked = Date.now();
  allowCookies.value = true;
  showCookiePopup(false);
}

onMounted(() => {
  nextTick(() => {
    checkAuthorizations();
  });
});
</script>

<style>
.gj-nav-admin {
  background-color: #ec5228 !important;
}
.gj-nav-admin a,
.gj-nav-admin span {
  color: #fff !important;
}
.gj-nav-admin .gj-nav-link:hover,
.gj-nav-admin .gj-nav-home:hover {
  background-color: rgba(255, 255, 255, 0.1) !important;
  cursor: pointer;
}
.gj-simple-hello {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 300px;
  padding: 20px;
  background: #fff;
  border-radius: 8px;
  border: 1px solid rgba(0, 0, 0, 0.1);
  box-shadow: 0 0 1px #000000bf;
}
</style>
