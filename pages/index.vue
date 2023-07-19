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
        perPage: 10
      }
    };
  },
  async fetch() {
    this.comments = await fetch(`https://jsonplaceholder.typicode.com/comments/?_page=${this.page}`)
      .then(res => {
        const headers = res.headers;
        const totalCount = headers.get('x-total-count');
        this.pagination.pagesAmount = Math.ceil(totalCount / this.pagination.perPage);
        return res.json()
      })
  }
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
}
</style>
