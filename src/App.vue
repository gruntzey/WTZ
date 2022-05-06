<template>
  <div class="posts" v-cloak>
    <div class="post" v-for="image of images[currentPage]" :key="image.id">
      <div class="post__header">
        <img class="post__userpic" :src="image.user.profile_image.small">
        <div class="user-info">
          <span class="user-info__full-name">{{image.user.first_name}} {{image.user.last_name}}</span>
          <a
            target="_blank"
            class="user-info__username" 
            :href="`https://unsplash.com/@${image.user.username}`">@{{image.user.username}}</a>
        </div>
      </div>
      <div class="post__image">
        <img :src="image.urls.small">
      </div>
      <div class="post__statistics">
        <span class="post__views">{{beautifiedNumber(image.views)}}</span>
        <span class="post__eye icon-eye"></span>
      </div>
    </div>
  </div>

  <div class="pagination" 
  @click="changePage($event)"
  >
    <span class="pagination__first" v-if="currentPage > 2">1</span>
    <div class="pagination__dots" v-if="currentPage > 3">…</div>
    <div class="pagination__closest">
      <span class="pagination__prev" v-if="currentPage > 1">{{currentPage - 1}}</span>
      <div class="pagination__cur">{{currentPage}}</div>
      <span class="pagination__next" v-if="currentPage < config.pages">{{currentPage + 1}}</span>
    </div>
    <div class="pagination__dots" v-if="currentPage < config.pages - 2">…</div>
    <span class="pagination__last" v-if="currentPage < config.pages - 1">{{config.pages}}</span>
  </div>
</template>

<script>
import { createApi } from 'unsplash-js'
export default {
  name: 'App',
  data() {
    return {
      config: {
        pages: 59,
        itemsPerPage: 10,
      },
      images: {},
      currentPage: null,
    }
  },
  methods: {
    async getRandomImages() {

      const unsplash = createApi({
        accessKey: 'VWe2E1bVnfzQOmlegqBNvaT99xhA6AqCJmVMHHirvE8',
      });

      let response = await unsplash.photos.getRandom({
        count: this.config.itemsPerPage,
      })

      this.images[this.currentPage] = response.response
    },

    //can't be a computed property, because it's not relying on data()
    //1234567 -> 1 234 567
    beautifiedNumber(num) {
      let result = []
      String(num).split('').forEach((el, i, arr) => {
        result.push(el)
        if ((arr.length - (i + 1)) % 3 == 0) result.push(' ')
      })
      result = result.join('')
      return result
    },

    changePage(e){
      if (e.target.tagName == 'SPAN' &&
      !e.target.classList.contains('post__cur')
      ) {
        this.currentPage = Number(e.target.textContent)
      }
      
      if (e.code == "ArrowRight" && this.currentPage !== this.config.pages) this.currentPage++
      if (e.code == "ArrowLeft" && this.currentPage !==1) this.currentPage--
    }
  },
  computed: {
    
  },
  watch: {
    currentPage() {
      console.log('current Page changed')
      if (!this.images[this.currentPage]) {
        this.getRandomImages()
      }
    }
  },
  created() {
    
  },
  mounted() {
    this.currentPage = 1
    document.addEventListener('keydown', this.changePage)
  }
}
</script>

<style lang="scss">

  @import './assets/css/fonts.css';

  [v-cloak] {
    display: none;
  }

  :root {
    --main-gray: #8D8D8D;
    --pagination-height: 60px;
  }
  *, *::after, *::before {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    font-family: Roboto, sans-serif;
  }

  a {
    text-decoration: none;
  }

  body {
    margin-bottom: var(--pagination-height);
  }

  .posts {
    display: grid;
    grid-template-columns: 1fr;
  }
  .post {
    &__header {
      position: relative;
      height: 50px;
      display: flex;
      align-items: center;
      & > * {
        margin-left: 10px;;
      }
    }

    &__userpic {
      border-radius: 50%;
    }

    &__image {
      width: 100%;
      & img {
      width: 100%;
      object-fit: cover;
      }
    }

    &__statistics {
      display: flex;
      justify-content: flex-end;
      color: var(--main-gray);
    }

    &__views {
      font-weight: 700;
    }

    &__eye {
        margin-left: 5px;
        margin-right: 10px;
      }
  }

  .pagination {
    position: fixed;
    bottom: 0;
    left: 0;
    height: 60px;
    width: 100%;
    background-color: black;
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
    user-select: none;
    & span {
      cursor: pointer;
    }
    &__closest {
      display: flex;
      align-items: center;
    }
    & *:not(:last-child) {
      margin-right: 15px;
    }
    &__cur {
      font-size: 150%;
      font-weight: bold;
    }
  }

  .user-info {
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    font-size: 12px;
    &__full-name {
      font-weight: bold;
      display: block;
      margin-bottom: 3px;
    }

    &__username {
      display: block;
      color: var(--main-gray);

    }
  }

  .pagination {
    height: var(--pagination-height);
  }
  @media (min-width: 320px) {
    
  }

  @media (min-width: 480px) {
    .posts {
      padding: 0 20px;
    }
  }

  @media (min-width: 768px) {
    .posts {
      column-gap: 20px;
      grid-template-columns: 320px 320px;
      justify-content: center;
    }
  }

  @media (min-width: 1000px) {
    .posts {
      grid-template-columns: 500px 500px;
    }
  }

  
</style>