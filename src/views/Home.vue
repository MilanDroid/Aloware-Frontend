<template>
  <div
    class="home w-100 container-fluid d-flex justify-content-center align-items-center"
  >
    <div class="row">
      <AppPost class="col-12 my-5" />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import AppPost from "@/components/AppPost.vue"; // @ is an alias to /src

export default defineComponent({
  name: "Home",
  components: {
    AppPost,
  },
});
</script>
