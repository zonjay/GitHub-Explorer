<template>
  <label>
    <input type="radio" value="best-match" v-model="sortBy" />
    Sort By Best Match
  </label>
  <label>
    <input type="radio" value="stars" v-model="sortBy" />
    Sort By Stars
  </label>
  <label>
    <input type="radio" value="forks" v-model="sortBy" />
    Sort By Forks
  </label>
</template>

<script>
export default {
  name: "Sort",
  emits: ["update"],
  data() {
    return {
      sortBy: "best-match",
    };
  },
  watch: {
    sortBy(val) {
      this.$emit("update", val);
    },
  },
};
</script>

<style lang="scss">
label {
  padding-right: 25px;
}
</style>