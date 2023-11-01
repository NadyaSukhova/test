<template>
  <div id="list" v-if="ch_id == 0">
    <input v-model="name" />
    <button @click="searchCharacters">Search</button>
    <button v-if="this.search" @click="getPages">All characters</button>
    <div v-for="(ch_item, index) in ch_list" :key="index">
      Name: <a @click="this.ch_id = ch_item.id"> {{ ch_item.name }}</a> <br />
      Species: {{ ch_item.species }} <br />
      Episodes:
      <div style="display: inline;"
        v-html="getEpisodes(JSON.parse(JSON.stringify(ch_item.episode)))"
      ></div>
      <br />
      <img :src="ch_item.image" /> <br />
    </div>
  </div>
  <div v-else-if="this.episode != 0">
    <button @click="this.episode = 0">All characters</button>
    <EpisodePage :episode="episode" />
  </div>
  <div v-else>
    <button @click="this.ch_id = 0">All characters</button>
    <CharacterPage :id="ch_id" />
  </div>
</template>

<script>
import axios from "axios";
import CharacterPage from "@/components/CharacterPage.vue";
import EpisodePage from "@/components/EpisodePage.vue";
export default {
  name: "CharacterList",
  data() {
    return {
      ch_list: [],
      ch_id: 0,
      page: 0,
      name: "",
      episode: 0,
      search: false,
    };
  },
  mounted() {
    this.scroll();
  },
  components: {
    CharacterPage,
    EpisodePage,
  },

  methods: {
    getEpisodes(urls) {
      let res = "";
      let min_size = Math.min(5, urls.length);
      for (let i = 0; i < min_size; i++) {
        res +=
          "<a>" +
          urls[i].substring(urls[i].indexOf("episode/") + 8, urls[i].length) +
          "</a>" +
          ", ";
      }
      res = res.substring(0, res.length - 2);
      if (urls.length > 5) {
        res += ", ...";
      }
      return res;
    },
    scroll() {
      window.onscroll = () => {
        let bottomOfWindow =
          window.scrollY + window.innerHeight + 100 >
          document.body.scrollHeight;

        console.log(bottomOfWindow);
        if (bottomOfWindow & !this.search) {
          this.page++;
          try {
            axios
              .get(
                "https://rickandmortyapi.com/api/character/?page=" + this.page
              )
              .then(
                (response) =>
                  (this.ch_list = [...this.ch_list, ...response.data.results])
              );
            setTimeout(3000);
          } catch {
            console.log("Error!");
          }
        }
      };
    },
    searchCharacters() {
      this.search = true;
      try {
        axios
          .get("https://rickandmortyapi.com/api/character/?name=" + this.name)
          .then((response) => (this.ch_list = [...response.data.results]));
      } catch {
        console.log("Error!");
      }
    },
    getPages() {
      this.search = false;
      this.name = "";
      this.ch_list = [];
      for (let i = 1; i++; i <= this.page) {
        console.log(i);
        try {
          axios
            .get("https://rickandmortyapi.com/api/character/?page=" + String(i))
            .then(
              (response) =>
                (this.ch_list = [...this.ch_list, ...response.data.results])
            );
        } catch {
          console.log("Error!");
        }
      }
    },
  },

  beforeMount() {
    this.page += 1;
    try {
      axios
        .get("https://rickandmortyapi.com/api/character/?page=" + this.page)
        .then(
          (response) =>
            (this.ch_list = [...this.ch_list, ...response.data.results])
        );
    } catch {
      console.log("Error!");
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style></style>
