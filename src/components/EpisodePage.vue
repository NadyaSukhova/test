<template>
    <div class="episode">
      Name: {{ item.name }} <br />
      Date air: {{ item.air_date }} <br />
      Characters: {{ item.characters }} <br />
    </div>
  </template>
  
  <script>
  import axios from "axios";
  
  export default {
    name: "EpisodePage",
    props: {
      episode: Number,
    },
    data() {
      return {
        item: [],
      };
    },
    mounted() {
      axios
      .get("https://rickandmortyapi.com/api/character/" + String(this.$props.episode))
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
  