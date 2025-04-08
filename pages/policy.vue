<script lang="ts"></script>
<template>
<client-only>
<NavBar />
<div id="gjMain" class="gj-main"></div>
<Cookies />
<Copyright />
</client-only>
</template>
<script setup lang="ts">
import { useSeoMeta, useHead } from '@vueuse/head';
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
{rel: 'icon', type: 'image/png', href: '/logo.png'},
{rel: 'stylesheet', href: '/reset.css'},
{rel: 'stylesheet', href: '/custom.css'} ,
{rel: 'preconnect', href: 'https://fonts.googleapis.com'},
{rel: 'preconnect', href: 'https://fonts.gstatic.com', crossorigin: ''},
{rel: 'stylesheet', href: 'https://fonts.google.com/share?selection.family=Bungee+Spice|Roboto+Condensed:ital,wght@0,100..900;1,100..900'}
]
});
</script>
<style scoped></style>
<style></style>