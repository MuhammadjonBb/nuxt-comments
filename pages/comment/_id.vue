<template>
  <div v-if="comment" class="container comment">
    <span class="comment__id"> ID: {{ comment.id }} </span>
    <span class="comment__id">Post ID: {{ comment.postId }}</span>
    <button class="comment__btn-back" @click="$router.go(-1)">Back to previous page</button>
    <div class="comment__info">
      <h1 class="comment__name">{{ comment.name }}</h1>
      <span class="comment__email">{{ comment.email }}</span>
    </div>
    <p class="comment__text">{{ comment.body }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      comment: null
    };
  },
  mounted() {
    this.getComment()
  },
  methods: {
    async getComment() {
      this.comment = await fetch(`https://jsonplaceholder.typicode.com/comments/${this.$route.params.id}`)
        .then(res => res.json())
    },
  }
};
</script>

<style>
@import '~assets/css/comment.css';
</style>
