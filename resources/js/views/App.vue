<template>
  <div class="container">
    <div class="container">
      <div class="nav nav-tabs justify-content-center">
        <span
          v-for="route in routes"
          :key="route.name"
          class="nav-link px-2 link-dark"
          :class="{ active: isActiveRoute(route) }"
          :aria-selected="isActiveRoute(route)"
        >
          <router-link :to="{ name: route.name }">
            {{ route.title }}
          </router-link>
        </span>
      </div>
    </div>
    <router-view />
  </div>
</template>

<script>
export default {
  name: "App",
  data: () => ({
    routes: [
      { name: "Welcome", title: "Главная" },
      { name: "Posts", title: "Посты" },
    ],
  }),
  methods: {
    isActiveRoute(route) {
      return this.$route.name === route.name;
    },
  },
};
</script>

<style scoped>
</style>
