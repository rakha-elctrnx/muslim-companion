<template>
  <div class="height-full-page">
    <router-view v-slot="{ Component }">
      <transition name="route">
        <component :is="Component" />
      </transition>
    </router-view>
  </div>
</template>


<script>
export default {};
</script>
<style>
@font-face {
  font-family: 'LPMQ IsepMisbah';
  src: url('../assets/LPMQ_IsepMisbah.ttf');
}

.teks-arab {
  font-family: 'LPMQ IsepMisbah', serif;
  direction: rtl
}

html, body, .height-full-page {
    overflow: auto;
}

a, .no-highlight, button {
    -webkit-tap-highlight-color: transparent;
}

.route-enter-from {
  opacity: 0;
  transform: translateX(100px);
}

.route-enter-active {
  transition: all 0.3s ease-out;
}

.route-leave-to {
  opacity: 0;
  transform: translateX(-100px);
}

.route-leave-active {
  transition: all 0.3s ease-in;
}

html.dark {
  color-scheme: dark;
}
</style>
