<template>
  <client-only>
    <header :class="['gj-navbar', navAdminMode]">
      <nav class="gj-nav-left">
        <a href="/" class="gj-nav-home">
          <img src="/favicon.ico" width="20" height="20" class="gj-nav-logo" />
          <span>guajavat</span>
        </a>
      </nav>
      <nav id="gjNavBasic" class="gj-nav-right gj-hidden">
        <ul>
          <li><a href="#" class="gj-nav-link">3R Guide</a></li>
          <li><a href="#" class="gj-nav-link">Leaderboard</a></li>
          <li><a href="#" class="gj-nav-link">Contribute</a></li>
          <li><span id="gjNavBasicLogout" class="gj-nav-link">Logout</span></li>
        </ul>
      </nav>
      <nav id="gjNavAdmin" class="gj-nav-right gj-hidden">
        <ul>
          <li><a href="#" class="gj-nav-link">Settings</a></li>
          <li><a href="#" class="gj-nav-link">Reports</a></li>
          <li><span id="gjNavAdminLogout" class="gj-nav-link">Logout</span></li>
        </ul>
      </nav>
    </header>
  </client-only>
</template>
<script setup lang="ts">
const props = defineProps<{ navAdminMode: string }>();
</script>
