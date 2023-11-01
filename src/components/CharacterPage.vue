<template>
  <div class="character">
    Name: {{ item.name }} <br />
    Species: {{ item.species }} <br />
    <div v-if="item.type != ''">Type: {{ item.type }} <br /></div>
    Origin: {{ getName(this.item.origin) }} <br />
    Location: {{ getName(this.item.location) }} <br />
    Url: {{ item.url }} <br />
    <img :src="item.image" /> <br />
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "CharacterPage",
  props: {
    id: Number,
  },
  data() {
    return {
      item: [],
    };
  },
  mounted() {
    axios
    .get("https://rickandmortyapi.com/api/character/" + String(this.$props.id))
      .then((response) => (this.item = response.data));
  },
  methods: {
    getName(obj) {
      if (obj) {
        return obj.name;
      }
      else return "";
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style></style>
