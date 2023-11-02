<template>
  <div class="episode">
    Name: {{ item.name }} <br />
    Date air: {{ item.air_date }} <br />
    Characters: {{ gotImages() }}
    <div v-for="(path, index) in got_pics" :key="path">
      <router-link :to="'/' + pics[index]"><img :src="path" /></router-link>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "EpisodePage",
  props: {
    episode: String,
  },
  data() {
    return {
      item: [],
      pics: [],
      got_pics: [],
      flag: true,
    };
  },
  mounted() {
    axios
      .get(
        "https://rickandmortyapi.com/api/episode/" +
          String(this.$route.params.episode)
      )
      .then(
        (response) => (
          (this.item = response.data),
          (this.pics = response.data.characters.map((element) => {
            return element.substring(
              element.indexOf("api/") + 4,
              element.length
            );
          }))
        )
      );
  },
  methods: {
    gotImages() {
      if (this.flag & (this.pics.length > 0)) {
        this.flag = false;
        this.got_pics = [];
        for (let pic of JSON.parse(JSON.stringify(this.pics))) {
          axios
            .get("https://rickandmortyapi.com/api/" + pic)
            .then((response) => this.got_pics.push(response.data.image));
        }
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style></style>
