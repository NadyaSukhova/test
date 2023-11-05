<script setup>
import { useCharacterStore } from "@/stores/characters";

const charactersStore = useCharacterStore();
</script>

<template>
  <div id="list" v-if="character_id > 0">
    <router-link :to="{ name: 'all_characters' }"> All characters </router-link>
    <router-view />
  </div>
  <div v-else-if="episode_id > 0">
    <router-link :to="{ name: 'all_characters' }"> All characters </router-link>
    <router-view />
  </div>
  <div v-else-if="search">
    <router-link :to="{ name: 'all_characters' }"> All characters </router-link>
    <router-view />
  </div>
  <div v-else>
    <input v-model="character_name" />
    <select v-model="character_status">
      <option value="alive">Alive</option>
      <option value="dead">Dead</option>
      <option value="unknown">Unknown</option>
    </select>
    <router-link
      @click="this.search = true"
      :to="{
        name: 'search_page',
        query: { name: character_name, status: character_status },
      }"
    >
      Search
    </router-link>

    <div v-for="(ch_item, index) in charactersStore.characters" :key="index">
      Name:
      <router-link
        @click="this.character_id = Number(ch_item.id)"
        :to="{ name: 'character_page', params: { id: ch_item.id } }"
      >
        {{ ch_item.name }}
      </router-link>
      <br />
      Species: {{ ch_item.species }} <br />
      Episodes:
      <div
        style="display: inline"
        v-for="(ep, index) in getEpisodes(
          JSON.parse(JSON.stringify(ch_item.episode))
        )"
        :key="ep"
      >
        <div style="display: inline" v-if="index != 0">,</div>
        <router-link
          @click="this.episode_id = Number(ep)"
          :to="{ name: 'episode_page', params: { episode: ep } }"
        >
          {{ ep }}
        </router-link>
      </div>
      <div
        style="display: inline"
        v-if="
          JSON.parse(JSON.stringify(ch_item.episode)).length > 5
        "
      >
        , ...
      </div>
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
      page: useCharacterStore().characters.length / 20,
      character_id: 0,
      episode_id: 0,
      character_name: "",
      character_status: "",
      search: false,
      charactersObj: useCharacterStore(),
    };
  },
  mounted() {
    setTimeout(3000);
    this.scroll();
  },
  beforeMount() {
    this.charactersObj.firstPage();
    this.page++;
  },
  methods: {
    getEpisodes(urls) {
      let res = [];
      let min_size = Math.min(5, urls.length);
      for (let i = 0; i < min_size; i++) {
        res.push(
          urls[i].substring(urls[i].indexOf("episode/") + 8, urls[i].length)
        );
      }
      return res;
    },
    scroll() {
      window.onscroll = () => {
        let bottomOfWindow =
          window.scrollY + window.innerHeight + 100 >
          document.body.scrollHeight;
        if (
          bottomOfWindow &
          !this.search &
          (this.character_id === 0) &
          (this.episode_id === 0)
        ) {
          this.page++;
          try {
            axios
              .get(
                "https://rickandmortyapi.com/api/character/?page=" + this.page
              )
              .then((response) => {
                for (var character of response.data.results) {
                  this.charactersObj.pushCharacter(character);
                }
              });
          } catch (error) {
            console.log("Error!");
            console.log(error);
          }
        }
      };
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style></style>
