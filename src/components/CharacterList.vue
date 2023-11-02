<template>
  <div id="list" v-if="ch_id > 0">
    <router-link :to="{ name: 'all_characters' }" @click="getPages">
      All characters
    </router-link>
    <router-view />
  </div>
  <div v-else-if="episode > 0">
    <router-link :to="{ name: 'all_characters' }" @click="getPages">
      All characters
    </router-link>
    <router-view />
  </div>
  <div v-else>
    <input v-model="name" />
    <button @click="searchCharacters">Search</button>
    <router-link :to="{ name: 'all_characters' }" @click="getPages">
      All characters
    </router-link>
    <div v-for="(ch_item, index) in ch_list" :key="index">
      Name:
      <router-link
        @click="this.ch_id = Number(ch_item.id)"
        :to="{ name: 'charater_page', params: { id: ch_item.id } }"
        >{{ ch_item.name }}</router-link
      >
      <br />
      Species: {{ ch_item.species }} <br />
      Episodes:
      <div style="display: inline"
        v-for="ep in getEpisodes(JSON.parse(JSON.stringify(ch_item.episode)))" :key=ep
      >
        <router-link
          @click="this.episode = Number(ep)"
          :to="{ name: 'episode_page', params: { episode: ep } }"
        >
          {{ ep }}
        </router-link>
      </div>, ...
      <br />
      <img :src="ch_item.image" /> <br />
    </div>
  </div>
</template>

<script>
import axios from "axios";

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

  methods: {
    getEpisodes(urls) {
      let res = "";
      let min_size = Math.min(5, urls.length);
      for (let i = 0; i < min_size; i++) {
        res +=
          urls[i].substring(urls[i].indexOf("episode/") + 8, urls[i].length) +
          ", ";
      }
      res = res.substring(0, res.length - 2);
      return res;
    },
    scroll() {
      window.onscroll = () => {
        let bottomOfWindow =
          window.scrollY + window.innerHeight + 100 >
          document.body.scrollHeight;
        if (bottomOfWindow & !this.search) {
          setTimeout(3000);
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
      for (let i = 1; i <= this.page; i++) {
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
