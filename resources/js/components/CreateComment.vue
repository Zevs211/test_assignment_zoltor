<template>
        <div class="card mt-2 px-5 py-4">
            <div class="form-group">
                <label class="form__label" for="title">Имя</label>
                <input
                    id="name"
                    type="text"
                    class="form-control"
                    :class="{ 'is-invalid': $v.name.$error }"
                    placeholder="name"
                    v-model.trim="$v.name.$model">
                <div v-if="!$v.name.required" class="invalid-feedback">
                    Name is required
                </div>
                <div v-if="!$v.name.maxLength" class="invalid-feedback">
                    Name must have at most {{ $v.name.$params.maxLength.max }} letters.
                </div>
            </div>
            <div class="form-group">
                <label class="form__label" for="description">Комментарий</label>
                <textarea
                    id="commentary"
                    rows="6"
                    class="form-control"
                    :class="{ 'is-invalid': $v.text.$error }"
                    placeholder="Комментарий"
                    v-model="$v.text.$model" />
                <div v-if="!$v.text.required" class="invalid-feedback">
                 Commentary is required
                </div>
            </div>
            <button type="button" class="btn btn-primary" @click="createComment">Отправить</button>
        </div>
</template>

<script>
import axios from 'axios'
import { validationMixin } from 'vuelidate'
import { required, maxLength } from 'vuelidate/lib/validators'

export default {
  name: 'CreateComment',
  mixins: [validationMixin],
  props: {
    postId: {
      type: String | Number
    }
  },
  data: () => ({
    name: '',
    text: '',
  }),
  validations: {
      name: {
        required,
        maxLength: maxLength(255)
      },
      text: {
        required,
      }
  },
  methods: {
    async createComment() {
      this.$v.$touch()
      if (this.$v.$invalid) {
        return
      }
      await axios.post(
        'http://localhost:8000/api/comments/create',
        {
          name: this.name,
          text: this.text,
          post_id: this.postId
        })
      this.$emit('on-comment-create')
      this.name = ''
      this.text = ''
      this.$v.$reset()
    }
  }
}
</script>

<style lang="scss" scoped>

</style>