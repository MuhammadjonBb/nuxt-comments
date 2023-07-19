<template>
  <div>
    <h1 class="title">The <span style="color: #009879;">Comments</span> app</h1>
    <div class="table table__container">
      <table v-if="comments" class="styled-table">
        <thead>
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Email</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="comment in comments" :key="comment.id">
            <td>{{ comment.id }}</td>
            <td>{{ comment.name }}</td>
            <td>{{ comment.email }}</td>
          </tr>

        </tbody>
      </table>
    </div>

    <div v-if="comments" class="pagination-container">
      <div v-show="page > 1" class="pagination-number arrow" @click="tofirstPage">
        <span class="arrow:text">&lt;&lt;</span>
      </div>
      <div v-show="page > 1" class="pagination-number arrow" @click="page = page - 1">
        <span class="arrow:text">&lt;</span>
      </div>

      <div v-for="num in pagination.paginationArray" :key="num" class="pagination-number"
        :class="{ 'pagination-active': page === num }" @click="page = num">
        {{ num }}
      </div>

      <div v-show="page < pagination.pagesAmount" class="pagination-number arrow" @click="page = page + 1">
        <span class="arrow:text">&gt;</span>
      </div>
      <div v-show="page < pagination.pagesAmount" class="pagination-number arrow" @click="tolastPage">
        <span class="arrow:text">&gt;&gt;</span>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      comments: null,
      page: 40,
      pagination: {
        pagesAmount: null,
        perPage: 5,
        prevPage: null,
        nextPage: null,
        paginationArray: null
      }
    };
  },

  watch: {
    page() {
      this.getComments()
    }
  },
  mounted() {
    this.getComments()
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
    }
  },
}
</script>

<style>
.title {
  padding-top: 20px;
  margin: 0 auto;
  margin-bottom: 20px;
  text-align: center;
}

.table__container {
  overflow-x: auto;
  max-width: 100%;
  padding: 0 20px;
}

.styled-table {
  border-collapse: collapse;
  border-radius: 5px 5px 0 0;
  font-size: 16px;
  min-width: 400px;
  margin: 0 auto;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.15);
  overflow: hidden;
}

.styled-table thead tr {
  background-color: #009879;
  color: #ffffff;
  text-align: left;
}

.styled-table th,
.styled-table td {
  padding: 12px 15px;
}

.styled-table tbody tr {
  border-bottom: 1px solid #dddddd;
}

.styled-table tbody tr:last-of-type {
  border-bottom: 2px solid #009879;
}

.styled-table tbody tr:hover {
  background-color: #f3f3f3;
  cursor: pointer;
  font-weight: bold;
  color: #009879;
}


.hide {
  display: none;
  visibility: hidden;
  height: 0;
}

.pagination-container {
  margin-top: 20px;
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
}

.arrow-text {
  display: block;
  font-size: 13px;
}

.pagination-number {
  margin: 0 6px;
  border-radius: 6px;
  background: #009879;
  min-width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  padding: 0 6px;
}

.pagination-number:hover {
  background: #016955;
}

.pagination-active {
  outline: 1px solid #fff;
  outline-offset: -3px;
  position: relative;
}


@media (max-width: 1024px) {
  .styled-table {
    width: 90%;
  }

}

@media (max-width: 540px) {
  .title {
    font-size: 24px;
    margin-bottom: 10px;
  }

  .styled-table {
    font-size: 3.5vw;
  }

  .pagination-number {
    min-width: 20px;
    height: 25px;
    padding: 0 5px;
    font-size: 12px;
  }
}
</style>
