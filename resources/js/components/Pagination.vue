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
      <dropdown
        class="ml-4"
        :options="itemsPerPageOptions"
        v-model="itemsPerPage"
      />
    </ul>
  </nav>
</template>

<script>
import { clamp } from "../helpers";
import Dropdown from "./Dropdown.vue";

const ALL_OPTION = "All";

export default {
  components: { Dropdown },
  name: "Pagination",
  props: {
    totalItems: {
      type: Array,
      default: () => [],
    },
  },
  data: () => ({
    itemsPerPage: 5,
    itemsPerPageOptions: [5, 10, 15, ALL_OPTION],
    page: 1,
  }),
  computed: {
    totalPages() {
      const itemsPerPage = _.isString(this.itemsPerPage)
        ? this.totalItems.length
        : this.itemsPerPage;
      return Math.ceil(this.totalItems.length / itemsPerPage);
    },
    paginationItems() {
      return new Array(this.totalPages).fill().map((_, index) => index + 1);
    },
  },
  watch: {
    totalItems() {
      this.$emit("on-page-change", this.calculateItems());
    },
    itemsPerPage() {
      this.page = 1;
      this.$emit("on-page-change", this.calculateItems());
    },
  },
  methods: {
    setPage(page) {
      this.page = clamp(page, 1, this.totalPages);
      this.$emit("on-page-change", this.calculateItems());
    },
    calculateItems() {
      if (this.itemsPerPage === ALL_OPTION) return this.totalItems;
      return this.totalItems.slice(
        (this.page - 1) * this.itemsPerPage,
        this.page * this.itemsPerPage
      );
    },
  },
};
</script>
