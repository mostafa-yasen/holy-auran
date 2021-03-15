<template>
  <div class="home" id="home">
  </div>
</template>

<script lang="ts">
import { Vue, Component, Watch } from 'vue-property-decorator'
import axios from 'axios';


@Component
export default class Home extends Vue {
  private image: object | null = null;
  private keywords: string[] = ['earth', 'sky', 'sea', 'islam', 'mosque', 'quran', 'mekkah'];
  private homeElement: HTMLDivElement | null = null;

  public getRandomInt(max: number) {
    return Math.floor(Math.random() * Math.floor(max));
  }

  public async getRandomImage(keyword: string | null = null) {
    const res = await axios.get('https://api.unsplash.com/search/photos', {
      headers: {
        Authorization: `Client-ID ${process.env.VUE_APP_UNSPLASH_ACCESS_KEY}`,
      },
      params: {
        query: keyword || this.keywords[this.getRandomInt(this.keywords.length)],
        page: 1,
        per_page: 10,
        orientation: 'landscape',
        content_filter: 'high',
      },
    });

    if (res.status !== 200) {
      return false;
    }
    return res.data.results[this.getRandomInt(res.data.results.length)];
  }

  private async mounted() {
    this.homeElement = document.querySelector('#home');
    await this.setNewImage();
  }

  public async setNewImage() {
    this.image = await this.getRandomImage();
  }

  @Watch('image', {
    immediate: true, deep: true
  })
  imageChanged(newVal: any) {
    if (!newVal) {
      return;
    }
    this.homeElement!.style.backgroundImage = "url(" + newVal.urls.regular + ")";
  }
}
</script>

<style lang="scss">
#home {
  height: 100vh;
  width: 100%;
  background-position: center;
  background-size: cover;
  display: flex;
}
</style>
