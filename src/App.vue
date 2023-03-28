<template>
  <v-app style="background: #E5E5E5;">
    <v-main class="mt-5">
      <staff-page/>
    </v-main>
  </v-app>
</template>

<script>
import StaffPage from "@/components/StaffPage.vue";

export default {
  name: 'App',
  components: {StaffPage}
}
</script>
