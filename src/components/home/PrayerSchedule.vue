<template>
  <div class="mx-4 bg-teal-500/90 rounded-xl">
    <div class="px-5 pt-4 pb-3">
      <div class="text-white">Lokasi Anda</div>
      <div class="flex items-center text-xl text-yellow-400 font-semibold">
        <div class="mr-2">
          {{ jadwalSholat.lokasi }}
        </div>
        <svg width="1em" height="1em" viewBox="0 0 16 16" class="cursor-pointer" fill="currentColor"
          @click.prevent="showModal = !showModal" xmlns="http://www.w3.org/2000/svg">
          <path
            d="M11.534 7h3.932a.25.25 0 0 1 .192.41l-1.966 2.36a.25.25 0 0 1-.384 0l-1.966-2.36a.25.25 0 0 1 .192-.41zm-11 2h3.932a.25.25 0 0 0 .192-.41L2.692 6.23a.25.25 0 0 0-.384 0L.342 8.59A.25.25 0 0 0 .534 9z" />
          <path fill-rule="evenodd"
            d="M8 3c-1.552 0-2.94.707-3.857 1.818a.5.5 0 1 1-.771-.636A6.002 6.002 0 0 1 13.917 7H12.9A5.002 5.002 0 0 0 8 3zM3.1 9a5.002 5.002 0 0 0 8.757 2.182.5.5 0 1 1 .771.636A6.002 6.002 0 0 1 2.083 9H3.1z" />
        </svg>
      </div>
      <div>
      </div>
    </div>
    <div class="grid grid-cols-4 gap-1 p-2">
      <div class="bg-teal-700/70 flex flex-col items-center justify-center p-2 text-center rounded-md">
        <p class="text-white text-sm">Imsak</p>
        <p class="text-white font-semibold">{{ jadwalSholat.jadwal.imsak }}</p>
      </div>
      <div class="bg-teal-700/70 flex flex-col items-center justify-center p-2 text-center rounded-md">
        <p class="text-white text-sm">Subuh</p>
        <p class="text-white font-semibold">{{ jadwalSholat.jadwal.subuh }}</p>
      </div>
      <div class="bg-teal-700/70 flex flex-col items-center justify-center p-2 text-center rounded-md">
        <p class="text-white text-sm">Terbit</p>
        <p class="text-white font-semibold">{{ jadwalSholat.jadwal.terbit }}</p>
      </div>
      <div class="bg-teal-700/70 flex flex-col items-center justify-center p-2 text-center rounded-md">
        <p class="text-white text-sm">Dhuha</p>
        <p class="text-white font-semibold">{{ jadwalSholat.jadwal.dhuha }}</p>
      </div>
      <div class="bg-teal-700/70 flex flex-col items-center justify-center p-2 text-center rounded-md">
        <p class="text-white text-sm">Dhuhur</p>
        <p class="text-white font-semibold">{{ jadwalSholat.jadwal.dzuhur }}</p>
      </div>
      <div class="bg-teal-700/70 flex flex-col items-center justify-center p-2 text-center rounded-md">
        <p class="text-white text-sm">Ashar</p>
        <p class="text-white font-semibold">{{ jadwalSholat.jadwal.ashar }}</p>
      </div>
      <div class="bg-teal-700/70 flex flex-col items-center justify-center p-2 text-center rounded-md">
        <p class="text-white text-sm">Magrib</p>
        <p class="text-white font-semibold">
          {{ jadwalSholat.jadwal.maghrib }}
        </p>
      </div>
      <div class="bg-teal-700/70 flex flex-col items-center justify-center p-2 text-center rounded-md">
        <p class="text-white text-sm">Isya</p>
        <p class="text-white font-semibold">{{ jadwalSholat.jadwal.isya }}</p>
      </div>
    </div>
  </div>
  <div class="mt-12 mx-3">

    <!-- Modal -->
    <div class="fixed flex justify-center items-center z-40 inset-0" v-if="showModal" @click.self="showModal = false">
      <div class="fixed mx-auto w-full h-80 max-w-sm overflow-y-auto bottom-0">
        <div class="bg-white h-80 height-full-page mx-auto p-7 z-50 dark:bg-slate-800 rounded-t-2xl">
          <div>
            <header class="flex justify-between">
              <span class="text-teal-500 font-semibold">Daftar Kota</span>
              <button class="rounded bg-red-400 text-white py-1 px-2 text-xs" @click="showModal = false">
                Close
              </button>
            </header>
            <hr class="mt-2 mb-4 dark:border-teal-500" />

            <form>
              <input placeholder="Cari Kota" v-model="search" type="text"
                class="text-slate-400 border border-teal-500 rounded-sm w-full px-3 py-2 focus:outline-none focus:ring-1 focus:ring-teal-500 bg-white placeholder:text-xs mb-3 dark:bg-slate-800" />
            </form>
          </div>

          <div class="py-2 px-1 rounded hover:bg-teal-500 mb-1 cursor-pointer" v-for="lokasi in filteredCity"
            :key="lokasi.id" @click.prevent="getDynamicLocation(lokasi.id)">
            <p class="text-xs text-teal-500 hover:text-white font-semibold">
              {{ lokasi.lokasi }}
            </p>
          </div>
        </div>
      </div>
    </div>
    <div v-if="showModal" class="fixed z-30 opacity-30 bg-black inset-0"></div>
  </div>
</template>

<script>
import axios from "axios";
import { onMounted, ref, computed, reactive } from "vue";
import { useDark } from "@vueuse/core";

export default {
  data() {
    return {
      // city: null,
      postcode: null
    };
  },
  setup() {
    const isDark = useDark();
    const showModal = ref(false);
    const search = ref("");
    const jadwalSholat = reactive({
      id: null,
      lokasi: "",
      jadwal: [],
    });
    const allDatalokasi = ref([]);

    // method buat get data lokasi
    const getAllLocation = async () => {
      try {
        const response = await axios.get(
          "https://api.myquran.com/v1/sholat/kota/semua"
        );
        allDatalokasi.value = response.data;
      } catch (error) {
        console.log(error);
      }
    };

    // method mengambil tanggal saat ini dengan format 2023/04/1
    const getDate = () => {
      const current = new Date();
      const date = `${current.getFullYear()}/${current.getMonth() + 1
        }/${current.getDate()}`;
      return date;
    };
    const getPostcode = (latitude, longitude) => {
      const endpoint = `https://geocode.maps.co/reverse?lat=${latitude}&lon=${longitude}`;

      axios
        .get(endpoint)
        .then(response => {
          getCity(response.data.address.postcode);
        })
        .catch(error => {
          console.error(error);
        });
    };

    const getCity = (postcode) => {
      const apiKey = "fafa27c0-1981-11ee-a09b-b3b5ca7ab173";
      const endpoint = `https://app.zipcodebase.com/api/v1/search?apikey=${apiKey}&codes=${postcode}&country=id`;

      axios
        .get(endpoint)
        .then(response => {
          const city = response.data.results[postcode][0]['city'];
          console.log(city);
          getCityId(city);
        })
        .catch(error => {
          console.error(error);
        });
    };

    const getCityId = (city) => {
      const endpoint = `https://api.myquran.com/v1/sholat/kota/cari/${city}`;

      axios
        .get(endpoint)
        .then(response => {
          const id = response.data.data[0].id;
          getJadwal(id);
        })
        .catch(error => {
          console.error(error);
        });
    };

    const getJadwal = (id) => {
      const endpoint = `https://api.myquran.com/v1/sholat/jadwal/${id}/` + getDate();

      axios
        .get(endpoint)
        .then(response => {
          let { data } = response.data;
          jadwalSholat.lokasi = data.lokasi;
          jadwalSholat.jadwal = data.jadwal;
        })
        .catch(error => {
          console.error(error);
        });
    };

    // method untuk mendapatkan jadwal berdasarkan lokasi dan tgl saat ini
    const getDefaultLocation = async (city) => {
      const idLocation = JSON.parse(localStorage.getItem("lokasi"));
      if (idLocation) {
        try {
          const response = await axios.get(
            "https://api.myquran.com/v1/sholat/jadwal/" +
            idLocation.id +
            "/" +
            getDate()
          );
          let { data } = response.data;
          jadwalSholat.lokasi = data.lokasi;
          jadwalSholat.jadwal = data.jadwal;
        } catch (error) {
          console.log(error);
        }
      } else {
        requestLocation
      }
    };

    const requestLocation = async () => {
      if (navigator.geolocation) {
          navigator.geolocation.getCurrentPosition(
            position => {
              const latitude = position.coords.latitude;
              const longitude = position.coords.longitude;
              getPostcode(latitude, longitude);
            },
            error => {
              console.error(error);
            }
          );
        } else {
          console.error("Geolocation is not supported by this browser.");
        }
    };

    // method untuk mendapatkan lokasi dinamis
    const getDynamicLocation = async (id) => {
      try {
        const response = await axios.get(
          "https://api.myquran.com/v1/sholat/jadwal/" + id + "/" + getDate()
        );
        let { data } = response.data;
        jadwalSholat.lokasi = data.lokasi;
        jadwalSholat.jadwal = data.jadwal;
        jadwalSholat.id = data.id;
        showModal.value = false;
        search.value = "";
        localStorage.setItem(
          "lokasi",
          JSON.stringify({
            id: jadwalSholat.id,
          })
        );
      } catch (error) {
        console.log(error);
      }
    };

    // untuk filter kota dengan computed
    const filteredCity = computed(() => {
      return allDatalokasi.value.filter((city) =>
        city.lokasi.toLowerCase().includes(search.value.toLowerCase())
      );
    });

    onMounted(() => {
      getAllLocation();
      getDefaultLocation();
      requestLocation();
      isDark.value;
    });

    return {
      showModal,
      filteredCity,
      search,
      jadwalSholat,
      getDynamicLocation,
    };
  },
};
</script>

<style scoped></style>
