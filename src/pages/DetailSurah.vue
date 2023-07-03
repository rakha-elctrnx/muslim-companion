<template>
  <div>
    <default-container>
      <header
        class="fixed z-20 w-full max-w-xl bg-gradient-to-r from-white to-teal-100/70 dark:from-slate-900 dark:to-slate-900 backdrop-blur">
        <div class="px-3 py-3 flex items-center justify-between">
          <router-link to="/quran">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="3" stroke="currentColor"
              class="w-10 h-10 p-3 rounded-full text-teal-700 hover:bg-teal-200/50 dark:hover:bg-slate-500/30">
              <path stroke-linecap="round" stroke-linejoin="round" d="M10.5 19.5L3 12m0 0l7.5-7.5M3 12h18" />
            </svg>
          </router-link>
          <h3 class="font-sans text-teal-700 font-bold mx-auto flex">
            {{ detailsurah.namaLatin }}
          </h3>
          <svg viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" fill="currentColor"
            class="text-teal-700 w-10 h-10 p-3 hover:bg-teal-200/50 dark:hover:bg-slate-500/30 rounded-full">
            <path
              d="M9.5 13a1.5 1.5 0 1 1-3 0 1.5 1.5 0 0 1 3 0zm0-5a1.5 1.5 0 1 1-3 0 1.5 1.5 0 0 1 3 0zm0-5a1.5 1.5 0 1 1-3 0 1.5 1.5 0 0 1 3 0z" />
          </svg>
        </div>
      </header>

      <div>

        <div
          class="mt-20 p-5 bg-gradient-to-tr from-teal-700 to-teal-500 mx-8 rounded-3xl flex flex-col justify-center items-center">
          <div class="text-yellow-400 text-lg font-semibold">
            {{ detailsurah.namaLatin }}
          </div>
          <div class="text-white text-sm italic">
            {{ detailsurah.arti }}
          </div>
          <div class="text-white text-xs mt-3">
            {{ detailsurah.tempatTurun + ' • ' + detailsurah.jumlahAyat + ' Ayat' }}
          </div>
          <div>
            <audio ref="audioPlayer" :src="audio"></audio>
          </div>
          <div class="flex flex-row justify-center gap-10 items-center w-full mt-5">
            <button id="prev" type="button" @click="prevSurah">
              <svg class="text-white w-7" :class="{'opacity-40' : detailsurah.nomor == 1}" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 256 256">
                <path fill="currentColor"
                  d="M208,47.88V208.12a16,16,0,0,1-24.43,13.43L64,146.77V216a8,8,0,0,1-16,0V40a8,8,0,0,1,16,0v69.23L183.57,34.45A15.95,15.95,0,0,1,208,47.88Z">
                </path>
              </svg>
            </button>
            <button id="play" type="button" @click="togglePlayback">
              <svg v-if="!isPlaying" class="w-10 text-white" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 256 256">
                <path fill="currentColor"
                  d="M240,128a15.74,15.74,0,0,1-7.6,13.51L88.32,229.65a16,16,0,0,1-16.2.3A15.86,15.86,0,0,1,64,216.13V39.87a15.86,15.86,0,0,1,8.12-13.82,16,16,0,0,1,16.2.3L232.4,114.49A15.74,15.74,0,0,1,240,128Z">
                </path>
              </svg>
              <svg v-else class="w-10 text-white" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 256 256">
                <path fill="currentColor"
                  d="M216,48V208a16,16,0,0,1-16,16H160a16,16,0,0,1-16-16V48a16,16,0,0,1,16-16h40A16,16,0,0,1,216,48ZM96,32H56A16,16,0,0,0,40,48V208a16,16,0,0,0,16,16H96a16,16,0,0,0,16-16V48A16,16,0,0,0,96,32Z">
                </path>
              </svg>
            </button>
            <button id="next" type="button" @click="nextSurah">
              <svg class="text-white w-7" :class="{'opacity-40' : detailsurah.nomor == 114}" viewBox="0 0 256 256">
                <path fill="currentColor"
                  d="M208,40V216a8,8,0,0,1-16,0V146.77L72.43,221.55A15.95,15.95,0,0,1,48,208.12V47.88A15.95,15.95,0,0,1,72.43,34.45L192,109.23V40a8,8,0,0,1,16,0Z">
                </path>
              </svg>
            </button>
          </div>
        </div>

        <keep-alive>
          <component :is="currentTab" :ayats="detailsurah.ayat" :nomor="detailsurah.nomor"
            :namalatinsurah="detailsurah.namaLatin" />
        </keep-alive>
      </div>

    </default-container>

    <!-- Scoll on top -->
    <ScrollOnTop @scrollOnTop="handleEvent" />
  </div>
</template>

<script>
import axios from "axios";
import DefaultContainer from "../components/reusable/DefaultContainer.vue";
import ListCardAyat from "../components/quran/ListCardAyat.vue";
import TafsirSurah from "../components/quran/TafsirSurah.vue";
import ScrollOnTop from "../components/reusable/ScrollOnTop.vue";
import { onMounted, reactive, ref } from "vue";
import { useRoute } from "vue-router";
import router from '../router/index';
import { useDark } from "@vueuse/core";

export default {
  methods: {
    togglePlayback() {
      const audioPlayer = this.$refs.audioPlayer;
      if (audioPlayer.paused) {
        audioPlayer.play();
        this.isPlaying = true;
      } else {
        audioPlayer.pause();
        this.isPlaying = false;
      }
    }
  },
  components: { DefaultContainer, ListCardAyat, TafsirSurah, ScrollOnTop },
  setup() {
    const route = useRoute();
    const audio = ref("");
    const detailsurah = ref([]);
    const currentTab = ref("ListCardAyat");
    const isDark = useDark();
    const isPlaying = ref(false);

    const colorDefaultButton = reactive({
      class: "bg-gradient-to-r from-emerald-700 to-teal-500 text-white",
      ListAyat: true,
      TafsirSurah: false,
    });

    const handleEvent = () => {
      window.scrollTo({
        top: 0,
        left: 0,
        behavior: "smooth",
      });
    };

    const getDetailSurah = async () => {
      try {
        const response = await axios.get(
          "https://equran.id/api/v2/surat/" + route.params.id
        );
        let { data } = response.data;
        detailsurah.value = data;
        audio.value = detailsurah.value.audioFull["05"];
      } catch (error) {
        console.log(error);
      }
    };

    const prevSurah = async () => {
        const currentSurahId = parseInt(route.params.id);
        const nextSurahId = currentSurahId +-1;

        if (currentSurahId !== 1) {          
          const response = await axios.get(
            `https://equran.id/api/v2/surat/${nextSurahId}`
          );
          const { data } = response.data;
  
          detailsurah.value = data;
  
          audio.value = detailsurah.value.audioFull["05"];
          isPlaying.value = false;
          router.push(`/surat/${nextSurahId}`);
        }
      
    };

    const nextSurah = async () => {
        const currentSurahId = parseInt(route.params.id);
        const nextSurahId = currentSurahId + 1;

        if (currentSurahId !== 114 ) {          
          const response = await axios.get(
            `https://equran.id/api/v2/surat/${nextSurahId}`
          );
          const { data } = response.data;
  
          detailsurah.value = data;
  
          audio.value = detailsurah.value.audioFull["05"];
          isPlaying.value = false;
          router.push(`/surat/${nextSurahId}`);
        }

    };

    const showSurah = () => {
      currentTab.value = "ListCardAyat";
      colorDefaultButton.TafsirSurah = false;
      colorDefaultButton.ListAyat = true;
    };

    const showTafsir = () => {
      currentTab.value = "TafsirSurah";
      colorDefaultButton.ListAyat = false;
      colorDefaultButton.TafsirSurah = true;
    };

    onMounted(() => {
      getDetailSurah();
      isDark.value;
    });

    return {
      detailsurah,
      audio,
      currentTab,
      showSurah,
      prevSurah,
      nextSurah,
      showTafsir,
      colorDefaultButton,
      handleEvent,
      isPlaying
    };
  },
};
</script>

<style scoped></style>
