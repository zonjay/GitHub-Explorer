<template>
  <div id="container">
    <nav id="navbar">
      <div class="icon" @click="changeCurrentTab('search')">
        <font-awesome-icon icon="search" />
      </div>
      <div class="icon" @click="changeCurrentTab('sort')">
        <font-awesome-icon icon="sort" />
      </div>
    </nav>
    <div class="option">
      <keep-alive>
        <Search
          v-if="current === 'search'"
          @update="getRepositories"
          @append="appendToRepositories"
          v-model:isLoading="isLoading"
          v-model:page="page"
          :sortBy="sortBy"
        />
        <Sort v-else @update="changeSort" />
      </keep-alive>
    </div>
    <main id="content">
      <h1 class="title">
        GitHub Repositories {{ sortBy !== "" ? `sorted by ${sortBy}` : "" }}
      </h1>
      <ul class="lists">
        <li class="list" v-for="item in repositories" :key="item.id">
          <div
            class="image-container"
            :style="{ 'background-image': `url(${item.owner.avatar_url})` }"
          ></div>
          <div class="description">
            <h4 class="title">{{ item.name }}</h4>
            <span class="lang">{{
              item.language !== "" ? item.language : "no language"
            }}</span>
          </div>
          <a class="link" :href="item.html_url" target="_blank">GitHub Link</a>
        </li>
      </ul>
      <div v-if="isLoading" class="skelenton">
        <SkelentonBox
          :width="'calc(100% / 2 - 1rem)'"
          :height="'140px'"
          v-for="n in 10"
          :key="n"
        />
      </div>
    </main>
  </div>
</template>

<script>
import Search from "@/components/Search";
import Sort from "@/components/Sort";
import SkelentonBox from "@/components/SkelentonBox.vue";

export default {
  name: "App",
  components: {
    Search,
    Sort,
    SkelentonBox,
  },
  data() {
    return {
      current: "search",
      repositories: [],
      page: 1,
      sortBy: "",
      isLoading: false,
    };
  },
  methods: {
    getRepositories(items) {
      this.repositories = items;
    },
    appendToRepositories(items) {
      this.repositories = [...this.repositories, ...items];
    },
    onScroll() {
      // 避免 API 被呼叫好幾次
      if (this.isLoading) {
        return;
      }

      if (
        window.scrollY + window.innerHeight >
        document.body.scrollHeight - 50
      ) {
        this.isLoading = true;
        this.page++;
      }
    },
    changeSort(sortBy) {
      this.sortBy = sortBy;
    },
    changeCurrentTab(type) {
      this.current = type;
    },
  },
  mounted() {
    window.addEventListener("scroll", this.onScroll);
  },
  beforeUnmount() {
    window.removeEventListener("scroll", this.onScroll);
  },
};
</script>

<style lang="scss">
$primary-color: rgba(18, 57, 156, 1);

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  min-height: 100%;
  background-color: rgba(229, 231, 235, 1);
}

#app {
  padding: 4rem 0;

  @media all and (max-width: 951px) {
    padding: 4rem 2rem;
  }

  #container {
    background-color: rgba(249, 250, 251, 1);
    border-radius: 1.5rem;
    max-width: 1280px;
    margin: 0 auto;
  }

  #navbar {
    display: flex;
    justify-content: flex-end;
    background-color: white;
    box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.1);
    border-top-left-radius: 1.5rem;
    border-top-right-radius: 1.5rem;
    padding: 2rem;

    .icon {
      color: $primary-color;
      cursor: pointer;
      padding: 0 1.5rem;
      border-right: 2px solid #e2e8f0;
      font-size: 1.5rem;
    }
  }

  .option {
    display: flex;
    justify-content: center;
    background-color: white;
    box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.1);
    padding: 1.5rem;
  }

  #content {
    padding: 2rem 4rem;
  }

  .title {
    color: $primary-color;
    margin-bottom: 20px;
  }

  .lists {
    list-style: none;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;

    & .list {
      display: flex;
      align-items: center;
      justify-content: space-between;
      border-width: 2px;
      border-style: solid;
      border-color: rgba(243, 244, 246, 1);
      border-radius: 0.75rem;
      width: calc(100% / 2 - 1rem);
      margin: 0.5rem 0;
      text-align: center;
      transition-property: all;
      transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
      transition-duration: 0.3s;

      @media all and (max-width: 951px) {
        width: 100%;
      }

      .image-container {
        min-width: 150px;
        height: 140px;
        line-height: 0; // 避免圖片下方多一塊空白
        background-image: url("https://www.freetogame.com/g/1/thumbnail.jpg");
        background-position: center center;
        background-size: cover;
        border-top-left-radius: 0.75rem;
        border-bottom-left-radius: 0.75rem;
      }

      .lang {
        background-color: $primary-color;
        color: white;
        font-size: 0.2rem;
        padding: 3px;
        border-radius: 4px;
      }

      .link {
        text-decoration: none;
        display: inline-block;
        background-color: #68d391;
        color: white;
        padding: 8px 15px;
        margin-right: 3%;
        border-radius: 25px;
        cursor: pointer;
      }

      &:hover {
        border: 2px solid $primary-color;
        color: $primary-color;
        transform: scale(1.02);
      }
    }
  }

  img {
    width: 100%;
  }

  .skelenton {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;

    & .skelenton-box {
      margin: 0.5rem 0;
    }
  }
}
</style>
