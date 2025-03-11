<script lang="ts"> 
if (typeof (window as any).global === "undefined") { 
  (window as any).global = window; 
} 
 
export default { 
  components: true, 
}; 
</script> 
 
<template> 
  <client-only> 
    <Header /> 
    <Footer /> 
  </client-only> 
</template> 
 
<script lang="ts" setup></script> 
 
<style scoped></style> 
 
<style></style>