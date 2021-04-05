<template>
  <input type="text" placeholder="Search Repository" v-model="keyword" />
</template>

<script>
import debounce from "es6-promise-debounce";

const SEARCH_API = "https://api.github.com/search/repositories";

export default {
  name: "Search",
  props: ["page", "isLoading", "sortBy"],
  emits: ["update:isLoading", "update:page", "update", "append"],
  data() {
    return {
      keyword: "",
    };
  },
  methods: {
    search() {
      return this.axios.get(
        `${SEARCH_API}?q=${this.keyword}&page=${this.page}&sort=${this.sortBy}`
      );
    },
  },
  watch: {
    keyword(val) {
      this.$emit("update:isLoading", true);

      if (this.page > 1) {
        this.$emit("update:page", 1);
      }

      // 搜尋關鍵字改變時，直接替換資料
      this.searchDebounced(val).then((response) => {
        const { items } = response.data;
        this.$emit("update", items);
        this.$emit("update:isLoading", false);
      });
    },
    page(val) {
      // 讀取新一頁資料時，直接附加在原有陣列後
      this.searchDebounced(val).then((response) => {
        const { items } = response.data;
        this.$emit("append", items);
        this.$emit("update:isLoading", false);
      });
    },
    sortBy(val) {
      // 當有輸入搜尋關鍵字，並切換排序方式時，重打 API
      if (this.keyword !== "") {
        this.searchDebounced(val).then((response) => {
          const { items } = response.data;
          if (this.page === 1) {
            this.$emit("update", items);
          } else {
            this.$emit("append", items);
          }
          this.$emit("update:isLoading", false);
        });
      }
    },
  },
  created() {
    // 避免連續觸發 API
    this.searchDebounced = debounce(this.search, 500);
  },
};
</script>

<style lang="scss">
$primary-color: #12399c;

input[type="text"] {
  width: 90%;
  outline: transparent;
  border-radius: 10px;
  padding: 10px;
  border: 2px solid $primary-color;

  &::placeholder {
    color: $primary-color;
  }
}
</style>