<template>
  <div>
    <transition name="modal">
      <div class="modal-mask">
        <div class="modal-wrapper" @click.self="onModalClose">
          <div class="modal-dialog" role="document">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title">{{ modalTitle }}</h5>
                <button
                  type="button"
                  class="close"
                  data-dismiss="modal"
                  aria-label="Close"
                  @click="onModalClose"
                >
                  <span aria-hidden="true" @click="showModal = false"
                    >&times;</span
                  >
                </button>
              </div>
              <div class="modal-body">
                <div class="form-group">
                  <label class="form__label" for="title">Title</label>
                  <input
                    id="title"
                    type="text"
                    class="form-control"
                    :class="{ 'is-invalid': $v.title.$error }"
                    placeholder="title"
                    v-model.trim="$v.title.$model"
                  />
                  <div v-if="!$v.title.required" class="invalid-feedback">
                    Title is required
                  </div>
                  <div v-if="!$v.title.maxLength" class="invalid-feedback">
                    Title must have at most
                    {{ $v.title.$params.maxLength.max }} letters.
                  </div>
                </div>
                <div class="form-group">
                  <label class="form__label" for="description">Title</label>
                  <textarea
                    id="description"
                    rows="10"
                    class="form-control"
                    :class="{ 'is-invalid': $v.description.$error }"
                    placeholder="description"
                    v-model="$v.description.$model"
                  />
                  <div v-if="!$v.description.required" class="invalid-feedback">
                    Description is required
                  </div>
                </div>
              </div>
              <div class="modal-footer">
                <button
                  type="button"
                  class="btn btn-secondary"
                  @click="onModalClose"
                >
                  Закрыть
                </button>
                <button type="button" class="btn btn-primary" @click="submit">
                  Создать
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import { validationMixin } from "vuelidate";
import { required, maxLength } from "vuelidate/lib/validators";

export default {
  name: "UpsertPost",
  mixins: [validationMixin],
  props: {
    submitMethod: {
      type: Function,
      default: () => function () {},
    },
    post: {
      type: Object,
      default: () => ({}),
    },
  },
  data: () => ({
    title: "",
    description: "",
  }),
  validations: {
    title: {
      required,
      maxLength: maxLength(255),
    },
    description: {
      required,
    },
  },
  computed: {
    modalTitle() {
      return _.isNil(this.post.id) ? "Создание поста" : "Редактирование поста";
    },
  },
  watch: {
    post: {
      immediate: true,
      deep: true,
      handler: function (newValue) {
        if (newValue !== null) {
          this.title = newValue.title;
          this.description = newValue.description;
        }
      },
    },
  },
  methods: {
    async submit() {
      this.$v.$touch();
      if (this.$v.$invalid) {
        return;
      }
      await this.submitMethod(this.title, this.description, this.post.id);
      this.$emit("on-post-submit");
      this.onModalClose();
      this.title = "";
      this.description = "";
    },
    onModalClose() {
      this.$emit("on-modal-close");
    },
  },
};
</script>

<style scoped>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}
</style>