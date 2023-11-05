<template>
  <div id="list" v-if="character_id > 0">
    <router-link :to="{ name: 'all_characters' }"> All characters </router-link>
    <router-view />
  </div>
  <div v-else-if="episode_id > 0">
    <router-link :to="{ name: 'all_characters' }"> All characters </router-link>
    <router-view />
  </div>
  <div v-else>
    <div v-for="(ch_item, index) in ch_list" :key="index">
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
        v-for="ep in getEpisodes(JSON.parse(JSON.stringify(ch_item.episode)))"
        :key="ep"
      >
        <router-link
          @click="this.episode_id = Number(ep)"
          :to="{ name: 'episode_page', params: { episode: ep } }"
        >
          {{ ep }}, 
        </router-link>
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
  props: {
    name: String,
    status: String,
  },
  data() {
    return {
      character_id: 0,
      episode_id: 0,
      character_name: "",
      ch_list: [],
    };
  },
  mounted() {
    var path ='';
    if (this.$route.query.name != "") {
      if (this.$route.query.status != "") {
        path = `https://rickandmortyapi.com/api/character/?name=${this.$route.query.name}&status=${this.$route.query.status}`;
      } else {
        path = `https://rickandmortyapi.com/api/character/?name=${this.$route.query.name}`;
      }
    } else {
      if (this.$route.query.status != "") {
        path = `https://rickandmortyapi.com/api/character/?status=${this.$route.query.status}`;
      }
      else {
        alert('No query!'); 
        window.location.replace("/");
      }
    }
    try {
      axios
        .get(path)
        .then((response) => {
          for (var character of response.data.results) {
            this.ch_list.push(character);
          }
        });
    } catch (error) {
      console.log("Error!");
      console.log(error);
    }
  },
  methods: {
    getEpisodes(urls) {
      let res = [];
      let min_size = Math.min(5, urls.length);
      for (let i = 0; i < min_size; i++) {
        res.push(urls[i].substring(urls[i].indexOf("episode/") + 8, urls[i].length))
      }
      return res;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style></style>
