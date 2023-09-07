<template>
  <div class="w-full">
    <div>
      <pv-menubar class="sticky bg-primary">
        <template #start>
          <pv-button label="CatchUp" icon="pi pi-bars"
                     @click="toggleSidebar"></pv-button>
          <side-menu v-model:visible="sidebarVisible"
                     v-on:source-selected="setSource"></side-menu>
        </template>
      </pv-menubar>
    </div>
  </div>
  <main-content :articles="articles"></main-content>
</template>

<script>
import {NewsApiService} from "./news/services/news-api.service.js";
import SideMenu from "./news/components/side-menu.component.vue";
import MainContent from "./news/components/main-content.component.vue";

export default {
  name: 'App',
  components: {MainContent, SideMenu},
  data() {
    return {
      sidebarVisible: false,
      articles: [],
      errors: [],
      newsApi: new NewsApiService()
    }
  },
  created() {
    this.getArticlesForSource('bbc-news');
  },
  methods: {
    // Fetch articles for selected Source
    getArticlesForSource(sourceId) {
      this.newsApi.getArticlesForSource(sourceId)
        .then(response => {
          this.articles = response.data.articles;
          console.log(response.data.articles);
        })
        .catch(e => {
          this.errors.push(e);
        });
    },
    // Fetch articles for selected Source with Logo URL
    getArticlesForSourceWithLogo(source) {
      this.newsApi.getArticlesForSource(source.id)
          .then(response => {
            this.articles = response.data.articles;
            this.articles.forEach(article => {
              article.source.urlToLogo = source.urlToLogo;

            });
            console.log(response.data.articles);
          })
          .catch(e => this.errors.push(e));

  },
    // Toggle Sidebar
    toggleSidebar() {
      this.sidebarVisible = !this.sidebarVisible;
    },
    // On Source Selected
    setSource(source) {
    this.getArticlesForSourceWithLogo(source);
    this.toggleSidebar();
    }
  }
}
</script>