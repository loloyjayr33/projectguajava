<template>
  <client-only>
    <div id="gjCookieModal" class="gj-cookie-modal bh-hidden"></div>
    <div id="gjCookieBox" class="gj-cookie-box bh-hidden">
      <span id="gjCookieX" class="gj-cookie-x">✕</span>
      <div class="gj-cookie-header">Cookies and Privacy</div>
      <div class="gj-cookie-message">
        We use cookies to ensure you get the best experience on our website.
      </div>
      <div class="gj-cookie-actions">
        <a href="/policy" class="gj-cookie-link" target="_blank">
          <div class="gj-cookie-action gj-cookie-more">Read more</div>
        </a>
        <div id="gjCookieOK" class="gj-cookie-action gj-cookie-ok">
          Accept all
        </div>
      </div>
    </div>
  </client-only>
</template>