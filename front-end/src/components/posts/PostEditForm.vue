<template>
  <div class="contents">
    <h1 class="page-header">Edit Post</h1>
    <div class="form-wrapper">
      <form class="form" @submit.prevent="submitForm">
        <div>
          <label for="title">Title: </label>
          <input id="title" v-model="title" type="text" />
        </div>
        <div>
          <label for="contents">Contents: </label>
          <textarea id="contents" v-model="contents" rows="5" />
          <p v-if="!isContentsValid" class="validation-text warning">
            Contents length must be less than 200
          </p>
        </div>
        <button type="submit" class="btn">Edit</button>
      </form>
      <p class="log">{{ logMessage }}</p>
    </div>
  </div>
</template>

<script>
  import { fetchPost, editPost } from '@/apis/posts';

  export default {
    data() {
      return {
        title: '',
        contents: '',
        logMessage: '',
      };
    },
    computed: {
      isContentsValid() {
        return this.contents.length <= 200;
      },
    },
    async created() {
      const { data } = await fetchPost(this.$route.params.id);
      console.log(data);

      this.title = data.title;
      this.contents = data.contents;
    },
    methods: {
      async submitForm() {
        try {
          await editPost(this.$route.params.id, {
            title: this.title,
            contents: this.contents,
          });

          this.$router.push('/main');
        } catch (error) {
          console.error(error?.response ?? error);
          this.logMessage = error?.response?.data?.message;
        }
      },
    },
  };
</script>

<style scoped>
  .form-wrapper .form {
    width: 100%;
  }

  .btn {
    color: white;
  }
</style>
