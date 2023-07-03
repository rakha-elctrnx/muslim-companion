<template>
  <div class="fixed absolute z-10 w-full max-w-xl bg-gradient-to-r from-white to-teal-100/70 dark:from-slate-900 dark:to-slate-900 backdrop-blur">
    <div class="px-3 py-3 flex items-center justify-between">
      <router-link to="/">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="3"
          stroke="currentColor"
          class="w-10 h-10 p-3 rounded-full text-teal-700 hover:bg-teal-200/50 dark:hover:bg-slate-500/30"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M10.5 19.5L3 12m0 0l7.5-7.5M3 12h18"
          />
        </svg>
      </router-link>
      <h3 class="font-sans text-teal-700 font-bold mx-auto">
        {{ dynamicHeaderPage }}
      </h3>
      <svg
        viewBox="0 0 15 15"
        xmlns="http://www.w3.org/2000/svg"
        fill="currentColor"
        class="text-teal-700 w-10 h-10 p-3 hover:bg-teal-200/50 dark:hover:bg-slate-500/30 rounded-full"
      >
        <path
          d="M9.5 13a1.5 1.5 0 1 1-3 0 1.5 1.5 0 0 1 3 0zm0-5a1.5 1.5 0 1 1-3 0 1.5 1.5 0 0 1 3 0zm0-5a1.5 1.5 0 1 1-3 0 1.5 1.5 0 0 1 3 0z"
        />
      </svg>
    </div>
  </div>
</template>

<script>
import { computed } from "vue";
import { useRoute } from "vue-router";
export default {
  setup() {
    const route = useRoute();

    const dynamicHeaderPage = computed(() => {
      if (route.path == "/quran") {
        return "Al-Qur'an";
      } else if (route.path == "/doa-harian") {
        return "Doa Harian";
      } else if (route.path == "/hadith") {
        return "Hadith";
      } else if (route.path == "/bacaan-tahlil") {
        return "Bacaan Tahlil";
      } else if (route.path == "/bookmark") {
        return "Bookmark";
      }
    });

    return { dynamicHeaderPage };
  },
};
</script>
