<template>
  <div class="btn-group dropup">
    <button
      class="btn btn-secondary btn-sm dropdown-toggle"
      :class="{ show: isOpen }"
      type="button"
      id="dropdown"
      data-bs-toggle="dropdown"
      :aria-expanded="isOpen"
      @click="isOpen = !isOpen"
    >
      {{ title }}
    </button>
    <ul
      class="dropdown-menu"
      :class="{ show: isOpen }"
      aria-labelledby="dropdown"
    >
      <li
        v-for="(option, index) in options"
        :key="index"
        @click="onSelect(option)"
      >
        <span class="dropdown-item" :class="{ active: value === option }">{{
          option
        }}</span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "Dropdown",
  props: {
    options: {
      type: Array,
      default: () => [],
    },
    value: {
      type: String | Number,
      default: null,
    },
  },
  data: () => ({
    isOpen: false,
    title: 5,
  }),
  methods: {
    onSelect(option) {
      this.$emit("input", option);
      this.title = option;
      this.isOpen = false;
    },
  },
};
</script>

<style>
</style>