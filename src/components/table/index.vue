<template>
  <oz-table
    :rows="rows"
    :total-pages="100"
    :current-page="currentPage"
    :static-paging="changePaging"
    @getPage="getPage"
    @infGetPage="infGetPage"
    @getCurrentUrl="getCurrentUrl"
    @getCurrentFilterUrl="getCurrentFilterUrl"
    @getCurrentPage="getCurrentPage"
  >
    <oz-table-column prop="id" title="ID" />
    <oz-table-column prop="postId" title="Post ID" />

    <oz-table-column prop="email">
      <template #title>
        <b>Email</b>
      </template>

      <template #body="{ row }">
        <a :href="`mailto:${row.email}`">{{ row.email }}</a>
      </template>
    </oz-table-column>

    <oz-table-column prop="name" title="Name" />
  </oz-table>
</template>

<script>
import OzTable from "./oz-table";
import OzTableColumn from "./oz-table-column";

export default {
  name: "SortWrapper",
  components: {
    OzTableColumn,
    OzTable,
  },
  props: ["changePaging"],
  created() {
    this.blockingPromise = this.getPage(1);
  },
  data() {
    return {
      rows: [],
      currentPage: 1,
      currentUrl: "",
      currentFilterUrl: ""
    };
  },
  methods: {
    getCurrentUrl(currentUrl){
      this.currentUrl = currentUrl
    },
    getCurrentFilterUrl(currentFilterUrl){
      this.currentFilterUrl = currentFilterUrl
    },
    getCurrentPage(currentPage){
      this.currentPage = currentPage
      console.log(this.currentPage, "@@@@");
    },
    async getPage(number) {
      const res = await fetch(
         `https://jsonplaceholder.typicode.com/comments?_page=${number}&_limit=10${this.currentUrl}${this.currentFilterUrl}`
      );
      this.rows = await res.json();
      this.currentPage = number;
    },
    async infGetPage() {
      this.blockingPromise && (await this.blockingPromise);
      const res = await fetch(
        `https://jsonplaceholder.typicode.com/comments?_page=${this.currentPage + 1}&_limit=10${this.currentUrl}${this.currentFilterUrl}`
      );
      const newRows = await res.json();
      this.rows = [...this.rows, ...newRows];
      this.currentPage++;
      console.log(`https://jsonplaceholder.typicode.com/comments?_page=${this.currentPage + 1}&_limit=5${this.currentUrl}${this.currentFilterUrl}`);
    },
    
  },
};
</script>
