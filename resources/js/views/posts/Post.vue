<template>
  <div class="py-5">
    <upsert-post
      v-if="showUpdatePostModal"
      :submitMethod="updatePost"
      :post="post"
      @on-modal-close="showUpdatePostModal = false"
      @on-post-submit="getPost"
    />
    <div class="card px-5 py-4 position-relative">
      <button
        type="button"
        class="btn btn-light position-absolute"
        style="top: 24px; right: 24px"
        @click="editPost"
      >
        <i class="bi bi-pencil mr-2" />
        <span>Редактировать пост</span>
      </button>
      <h1 class="text-center">{{ post.title }}</h1>
      <div class="mt-4 text-justify">{{ post.description }}</div>
    </div>
    <h2 class="mt-4">Оставить комментарий</h2>
    <create-comment class="mt-4" :postId="id" @on-comment-create="getPost" />
    <h2 class="my-4">Комментарии</h2>
    <div v-if="comments.length === 0">Никто еще не оставил комментарий</div>
    <div
      class="card mt-2 px-4 py-2"
      v-for="comment in comments"
      :key="comment.id"
    >
      <div class="font-weight-bold">
        {{ comment.name }}
      </div>
      <div class="text-justify">
        {{ comment.text }}
      </div>
    </div>
    <pagination
      class="mt-5"
      :totalItems="allComments"
      @on-page-change="onPageChange"
    />
  </div>
</template>

<script>
import axios from "axios";
import CreateComment from "../../components/CreateComment.vue";
import Pagination from "../../components/Pagination.vue";
import UpsertPost from "../../components/UpsertPost.vue";

export default {
  components: { CreateComment, Pagination, UpsertPost },
  name: "Post",
  props: {
    id: {
      type: String | Number,
      default: null,
    },
  },
  data: () => ({
    post: {},
    allComments: [],
    comments: [],
    showUpdatePostModal: false,
  }),
  created() {
    this.getPost();
  },
  methods: {
    async getPost() {
      try {
        const { data } = await axios.post(`http://localhost:8000/api/post`, {
          id: this.id,
        });
        this.post = data;
        this.allComments = this.post.comments;
      } catch (error) {
        console.error(error);
      }
    },
    onPageChange(items) {
      console.log(items);
      this.comments = items;
    },
    editPost() {
      this.showUpdatePostModal = true;
    },
    updatePost(title, description, id) {
      return axios.post("http://localhost:8000/api/posts/update", {
        id,
        title,
        description,
      });
    },
  },
};
</script>

<style scoped>
</style>
