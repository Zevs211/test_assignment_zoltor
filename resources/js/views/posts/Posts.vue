<template>
  <div>
    <button class="btn btn-primary" @click="showCreatePostModal = true">
      Создание поста
    </button>
    <upsert-post
      v-if="showCreatePostModal"
      :submitMethod="createPost"
      @on-modal-close="showCreatePostModal = false"
      @on-post-submit="getAllPosts"
    />
    <div class="card mt-4" v-for="post in posts" :key="post.id">
      <router-link
        style="text-decoration: none; color: inherit"
        :to="{
          name: 'Post',
          params: { id: post.id },
        }"
      >
        <div class="card-body">
          <h2 class="font-weight-bold">{{ post.title }}</h2>
          <p>{{ sliceDescription(post.description) }}</p>
          <div class="d-flex justify-content-end">
            <i class="bi bi-chat mr-2"></i> {{ post.comments.length }}
          </div>
        </div>
      </router-link>
    </div>
    <pagination
      class="mt-5"
      :totalItems="allPosts"
      @on-page-change="onPageChange"
    />
  </div>
</template>

<script>
import axios from "axios";
import UpsertPost from "../../components/UpsertPost.vue";
import Pagination from "../../components/Pagination.vue";

export default {
  name: "Posts",
  components: {
    UpsertPost,
    Pagination,
  },
  data: () => ({
    loading: false,
    allPosts: [],
    posts: [],
    showCreatePostModal: false,
  }),
  created() {
    this.getAllPosts();
  },
  methods: {
    async getAllPosts() {
      try {
        this.loading = true;
        const { data } = await axios.post("http://127.0.0.1:8000/api/posts");
        this.allPosts = data;
      } catch (error) {
        // TODO: implement notification system
        console.error(error);
      } finally {
        this.loading = false;
      }
    },
    createPost(title, description) {
      return axios.post("http://localhost:8000/api/posts/create", {
        title,
        description,
      });
    },
    onPageChange(items) {
      this.posts = items;
    },
    sliceDescription(description) {
      const maxDescriptionLength = 255;
      const elipsis = description.length > maxDescriptionLength ? "..." : "";
      return `${description.slice(0, maxDescriptionLength + 1)}${elipsis}`;
    },
  },
};
</script>


