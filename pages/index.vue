<template>
  <div class="container">
    <h1 class="title">The <span style="color: #009879;">Comments</span> app</h1>
    <div class="table table__container">
      <table v-if="comments" class="table__content">
        <thead>
          <tr>
            <th class="table__id" clickable @click="sortById">
              ID
              <span class="sort-arrow" :style="arrowStyle">&uarr;</span>
            </th>
            <th>Name</th>
            <th>Email</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="comment in comments" :key="comment.id" @click="toCommentPage(comment.id)">
            <td>{{ comment.id }}</td>
            <td>{{ comment.name }}</td>
            <td>{{ comment.email }}</td>
          </tr>

        </tbody>
      </table>
    </div>

    <div v-if="comments" class="pagination__container">
      <div v-show="page > 1" class="pagination__number arrow" @click="tofirstPage">
        <span class="arrow-text">&lt;&lt;</span>
      </div>
      <div v-show="page > 1" class="pagination__number arrow" @click="page = page - 1">
        <span class="arrow-text">&lt;</span>
      </div>

      <div v-for="num in pagination.paginationArray" :key="num" class="pagination__number"
        :class="{ 'pagination__active': page === num }" @click="setTablePage(num)">
        {{ num }}
      </div>

      <div v-show="page < pagination.pagesAmount" class="pagination__number arrow" @click="page = page + 1">
        <span class="arrow-text">&gt;</span>
      </div>
      <div v-show="page < pagination.pagesAmount" class="pagination__number arrow" @click="tolastPage">
        <span class="arrow-text">&gt;&gt;</span>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      comments: null,
      page: 1,
      pagination: {
        pagesAmount: null,
        perPage: 5,
        prevPage: null,
        nextPage: null,
        paginationArray: null
      },
      sortOrder: 'original'
    };
  },
  computed: {
    arrowStyle() {
      if (this.sortOrder === 'asc') {
        return { transform: 'rotate(0deg)' };
      } else if (this.sortOrder === 'desc') {
        return { transform: 'rotate(180deg)' };
      } else {
        return { display: 'none' };
      }
    }
  },
  watch: {
    page() {
      this.getComments()
      this.$router.push({ query: { page: this.page } });
      this.sortOrder = 'original'
    }
  },
  mounted() {
    this.getComments()
  },
  created() {
    const { page } = this.$route.query;
    if (page) {
      this.$router.push({ query: { page } });
      this.page = parseInt(page);
    } else {
      this.$router.push({ query: { page: 1 } });
    }
  },
  methods: {
    async getComments() {
      this.comments = await fetch(`https://jsonplaceholder.typicode.com/comments/?_page=${this.page}`)
        .then(res => {
          const headers = res.headers;
          const totalCount = headers.get('x-total-count');
          this.pagination.pagesAmount = Math.ceil(totalCount / 10);
          this.setUpPagination()
          return res.json()
        })
    },
    setUpPagination() {
      this.prevPage = this.page - 1 > 0 ? this.page - 1 : null;
      this.pagination.paginationArray = this.getPaginationRange(this.page, this.pagination.pagesAmount, this.pagination.perPage);
    },
    getPaginationRange(currentPage, totalPages, maxPagesToShow) {
      const halfMaxPages = Math.floor(maxPagesToShow / 2);
      let startPage = Math.max(currentPage - halfMaxPages, 1);
      let endPage = Math.min(currentPage + halfMaxPages, totalPages);

      if (endPage - startPage + 1 < maxPagesToShow) {
        if (startPage === 1) {
          endPage = Math.min(startPage + maxPagesToShow - 1, totalPages);
        } else {
          startPage = Math.max(endPage - maxPagesToShow + 1, 1);
        }
      }

      return Array.from({ length: endPage - startPage + 1 }, (_, i) => startPage + i);
    },
    tofirstPage() {
      this.page = 1
    },
    tolastPage() {
      this.page = this.pagination.pagesAmount
    },
    toCommentPage(id) {
      this.$router.push(`/comment/${id}`)
    },
    setTablePage(num) {
      this.page = num
    },
    sortById() {
      if (this.sortOrder === 'asc') {
        this.sortOrder = 'desc';
        return this.comments.sort((a, b) => b.id - a.id);
      } else if (this.sortOrder === 'desc') {
        this.sortOrder = 'original';
        return this.comments.sort((a, b) => a.id - b.id);
      } else {
        this.sortOrder = 'asc';
        return this.comments;
      }
    }
  },
}
</script>

<style>
@import '~assets/css/table.css';
@import '~assets/css/pagination.css';


.title {
  padding-top: 20px;
  margin: 0 auto;
  margin-bottom: 20px;
  text-align: center;
}

@media (max-width: 540px) {
  .title {
    font-size: 24px;
    margin-bottom: 10px;
  }
}
</style>
