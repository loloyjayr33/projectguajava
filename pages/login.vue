<template>
  <client-only>
    <NavBar :navAdminMode="navAdminMode" />
    <div id="bhMain" class="bh-main">
      <Login />
    </div>
    <Cookies />
    <Copyright />
  </client-only>
</template>

<script setup lang="ts">
import { useSeoMeta, useHead } from "@vueuse/head";
import { useRouter } from "vue-router";

const title = "Guajava";
const description = "A community based Payroll Management System";

useSeoMeta({
  title: () => title,
  description: () => description,
  charset: "utf-8",
  viewport: "width=device-width, initial-scale=1.0",
});

useHead({
  link: [
    { rel: "icon", type: "image/png", href: "/favicon.ico" },
    { rel: "stylesheet", href: "/reset.css" },
    { rel: "stylesheet", href: "/custom.css" },
    { rel: "preconnect", href: "https://fonts.googleapis.com" },
    { rel: "preconnect", href: "https://fonts.gstatic.com", crossorigin: "" },
  ],
});

const globalDelay = 500;

const allowCookies = useCookie<boolean>("allowCookies", {
  sameSite: "none",
  secure: true,
  maxAge: 60 * 60 * 24,
});
allowCookies.value = allowCookies.value ?? false;

const retries = useCookie<number>("retries", {
  sameSite: "none",
  secure: true,
  maxAge: 60 * 60,
});
retries.value = retries.value ?? 0;

const username = useCookie<string>("username", {
  sameSite: "none",
  secure: true,
  maxAge: 60 * 60 * 24,
});
username.value = username.value ?? "";

const accessToken = useCookie<string>("accessToken", {
  sameSite: "none",
  secure: true,
  maxAge: 60 * 60 * 24,
});
accessToken.value = accessToken.value ?? "";

const userLevel = useCookie<number>("userLevel", {
  sameSite: "none",
  secure: true,
  maxAge: 60 * 60 * 24,
});
userLevel.value = userLevel.value ?? -1;

const fullName = useCookie<string>("fullName", {
  sameSite: "none",
  secure: true,
  maxAge: 60 * 60 * 24,
});
fullName.value = fullName.value ?? "";

const router = useRouter();
let navAdminMode = userLevel.value >= 2 ? "bh-nav-admin" : "";
const bhServer = "https://bleedingheart-api.vercel.app";

type AuthResponse = {
  accessToken: string;
  userLevel: number;
  fullName: string;
  message: string;
};

function getByID<T extends HTMLElement>(id: string) {
  return document.getElementById(id) as T;
}

let lastLoginClicked = 0;

function checkAuthorizations() {
  if (accessToken.value) {
    if (userLevel.value <= 1) {
      router.push("/");
    } else if (userLevel.value >= 2) {
      router.push("/admin");
    }
  }

  const loginBox = getByID<HTMLDivElement>("bhLoginBox");
  const loginForm = getByID<HTMLDivElement>("bhLogInForm");
  const loginUsername = getByID<HTMLInputElement>("bhLoginUsername");
  const loginPassword = getByID<HTMLInputElement>("bhLoginPassword");
  const loginMessage = getByID<HTMLDivElement>("bhLogInMessage");
  const logInActions = getByID<HTMLDivElement>("bhLogInActions");
  const loginOK = getByID<HTMLDivElement>("bhLoginOK");

  if (!loginBox || !loginForm || !loginUsername || !loginPassword || !loginMessage || !logInActions || !loginOK) return;

  if (retries.value >= 3) {
    loginForm.classList.add("bh-hidden");
    loginMessage.classList.remove("bh-hidden");
    loginMessage.innerText = "Too many attempts, try again after 1 hour.";
    logInActions.classList.add("bh-hidden");
    return;
  }

  loginOK.dataset.busy = "0";
  loginBox.classList.remove("bh-hidden");

  loginOK.addEventListener("click", () => {
    if (loginOK.dataset.busy == "1") return;

    const tempUsername = loginUsername.value;
    const tempPassword = loginPassword.value;

    if (!tempUsername) {
      loginMessage.innerText = "Username is required.";
      loginMessage.classList.remove("bh-hidden");
      return;
    }

    if (!tempPassword) {
      loginMessage.innerText = "Password is required.";
      loginMessage.classList.remove("bh-hidden");
      return;
    }

    authorizeEmail(tempUsername, tempPassword);
  });

  loginUsername.addEventListener("keyup", (e) => {
    if (e.key === "Enter" && loginUsername.value) {
      loginPassword.focus();
    }
  });

  loginPassword.addEventListener("keyup", (e) => {
    if (e.key === "Enter") loginOK.click();
  });

  loadModal();
}

async function authorizeEmail(tempUsername: string, tempPassword: string) {
  if (lastLoginClicked >= Date.now() - globalDelay) return;
  lastLoginClicked = Date.now();

  const loginUsername = getByID<HTMLInputElement>("bhLoginUsername");
  const loginPassword = getByID<HTMLInputElement>("bhLoginPassword");
  const loginMessage = getByID<HTMLDivElement>("bhLogInMessage");
  const loginOK = getByID<HTMLDivElement>("bhLoginOK");

  if (!loginUsername || !loginPassword || !loginOK || !loginMessage) return;

  loginMessage.innerText = "";
  loginMessage.classList.add("bh-hidden");

  loginUsername.disabled = true;
  loginPassword.disabled = true;
  loginOK.dataset.busy = "1";

  try {
    userLevel.value = -1;

    const response = await $fetch(`${bhServer}/authorize`, {
      headers: { "Content-Type": "application/json" },
      method: "POST",
      body: { username: tempUsername, password: tempPassword },
    }) as AuthResponse;

    accessToken.value = String(response.accessToken);
    userLevel.value = Number(response.userLevel);
    fullName.value = String(response.fullName);

    if (accessToken.value) {
      username.value = tempUsername;
    }

    loginOK.dataset.busy = "0";

    if (!accessToken.value) {
      retries.value = (retries.value || 0) + 1;
    }

    if (response.message) {
      loginMessage.innerText = response.message;
      loginMessage.classList.remove("bh-hidden");
    }
  } catch (e: any) {
    loginMessage.innerText = `Error encountered: ${e}`;
    loginMessage.classList.remove("bh-hidden");
  }

  loginUsername.disabled = false;
  loginPassword.disabled = false;
  loginUsername.value = "";
  loginPassword.value = "";
  loginOK.dataset.busy = "0";

  checkAuthorizations();
}

function loadModal() {
  const cookieModal = getByID<HTMLDivElement>("bhCookieModal");
  const cookieBox = getByID<HTMLDivElement>("bhCookieBox");
  const cookieX = getByID<HTMLDivElement>("bhCookieX");
  const cookieOK = getByID<HTMLDivElement>("bhCookieOK");

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

function showCookiePopup(show: boolean = true) {
  const cookieBox = getByID<HTMLDivElement>("bhCookieBox");
  const cookieModal = getByID<HTMLDivElement>("bhCookieModal");

  if (!cookieBox || !cookieModal) {
    setTimeout(() => showCookiePopup(show), 50);
    return;
  }

  if (!show) {
    cookieBox.classList.add("bh-hidden");
    cookieModal.classList.add("bh-hidden");
    return;
  }

  cookieBox.classList.remove("bh-hidden");
  cookieModal.classList.remove("bh-hidden");
}

let lastCookieClicked = 0;
function allowAllCookies() {
  if (lastCookieClicked >= Date.now() - globalDelay) return;
  lastCookieClicked = Date.now();
  allowCookies.value = true;
  showCookiePopup(false);
}

onMounted(() => {
  nextTick(checkAuthorizations);
});
</script>
