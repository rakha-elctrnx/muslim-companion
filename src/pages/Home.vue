<template>
  <default-container>
    <Header />
    <PrayerSchedule />
    <Main />

  </default-container>
</template>

<script>
import Header from "../components/home/Header.vue";
import Main from "../components/home/Main.vue";
import PrayerSchedule from "../components/home/PrayerSchedule.vue";
import DefaultContainer from "../components/reusable/DefaultContainer.vue";
export default {
  components: { Header, Main, PrayerSchedule, DefaultContainer },
};
</script>

<style></style>
