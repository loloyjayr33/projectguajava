<script setup lang="ts"> 
const title = ref("ProjectGuajava | Home"); 
const description = ref("A community based Payroll Management System"); 
 
useSeoMeta({ 
  title: () => title.value, 
  description: () => `${title.value} - ${description.value}`, 
  charset: "utf-8", 
  viewport: "width=device-width, initial-scale=1.0", 
  robots: "index, follow", 
 
  ogTitle: () => title.value, 
  ogDescription: () => `${title.value} - ${description.value}`, 
 
  // ogImage: "https://projectguavajava.vercel.app/featured.jpg", 
  // twitterCard: "summary_large_image", 
}); 
</script>

<template> 
  <Html lang="en"> 
      <Head> 
      <Link rel="icon" type="image/png" href="/payment-check.png"></Link> 
      <link href="/reset.css" rel="stylesheet" /> 
      </Head> 
  </Html> 
      
  <client-only> 
    <Welcome /> 
  </client-only> 
</template> 

<style scoped></style> 