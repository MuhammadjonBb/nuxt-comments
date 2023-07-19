<template>
  <div v-if="comment" class="container comment">
    <span class="comment__id"> ID: {{ comment.id }} </span>
    <span class="comment__id">Post ID: {{ comment.postId }}</span>
    <nuxt-link to="/" class="comment__link">Back to commentators list</nuxt-link>
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
    console.log(this.$router.history);
    this.getComment()
  },
  methods: {
    async getComment() {
      this.comment = await fetch(`https://jsonplaceholder.typicode.com/comments/${this.$route.params.id}`)
        .then(res => res.json())
    }
  }
};
</script>

<style>
.container {
  margin: 0 auto;
  max-width: 1280px;
}

.comment {
  padding: 10px 20px 0;
}

.comment__id {
  display: block;
  color: #666;
}

.comment__name {
  margin-right: 20px;
  color: #009879;
}

.comment__info {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.comment__email {
  color: #666;
  font-size: 18px;
}

.comment__text {
  color: #333;
  font-size: 22px;
}

.comment__link {
  display: block;
  margin-top: 20px;
  color: #00bb96;
}

@media (max-width: 640px) {
  .comment__link {
    margin-top: 10px;
    font-size: 3vw;
  }

  .comment__id {
    font-size: 3vw;
  }

  .comment__name {
    font-size: 4.5vw;
  }

  .comment__email {
    font-size: 3.5vw;
  }

  .comment__info {
    flex-wrap: wrap;
  }

  .comment__text {
    font-size: 3.5vw;
  }
}
</style>
