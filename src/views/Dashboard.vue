<template>
  <div>
    <sidebar />
  </div>
</template>

<script>
import sidebar from "@/components/sidebar.vue";
export default {
  components: { sidebar },
  name: "DashboardPage",

  metaInfo: {
    title: "Tabulasi PAN",
    titleTemplate: "%s - Dashboard",
    htmlAttrs: {
      lang: "id",
      amp: true,
    },
  },
};
</script>