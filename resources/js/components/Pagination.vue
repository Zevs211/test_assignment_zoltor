<template>
  <nav>
    <ul class="pagination justify-content-center">
      <li class="page-item" :class="{ disabled: page === 1 }">
        <span class="page-link" @click="setPage(page - 1)">Предыдущая</span>
      </li>
      <li
        class="page-item"
        :class="{ active: page === item }"
        v-for="item in paginationItems"
        :key="item"
        @click="setPage(item)"
      >
        <span class="page-link">{{ item }}</span>
      </li>
      <li class="page-item" :class="{ disabled: page === totalPages }">
        <span class="page-link" @click="setPage(page + 1)">Следующая</span>
      </li>
    </ul>
  </nav>
</template>

<script>
import { clamp } from "../helpers";

export default {
  name: "Pagination",
  props: {
    totalItems: {
      type: Array,
      default: () => [],
    },
  },
  data: () => ({
    itemsPerPage: 5,
    itemsPerPageOptions: [5, 10, 15, "All"], // TODO
    page: 1,
  }),
  computed: {
    totalPages() {
      return Math.ceil(this.totalItems.length / this.itemsPerPage);
    },
    paginationItems() {
      return new Array(this.totalPages).fill().map((_, index) => index + 1);
    },
  },
  watch: {
    totalItems() {
      this.$emit("on-page-change", this.calculateItems());
    },
  },
  methods: {
    setPage(page) {
      this.page = clamp(page, 1, this.totalPages);
      this.$emit("on-page-change", this.calculateItems());
    },
    calculateItems() {
      if (this.itemsPerPage === "all") return this.totalItems;
      return this.totalItems.slice(
        (this.page - 1) * this.itemsPerPage,
        this.page * this.itemsPerPage
      );
    },
  },
};
</script>
