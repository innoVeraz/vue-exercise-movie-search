<script setup lang="ts">
import { onMounted, ref } from "vue";
import { IMovie } from "../models/IMovie";
import axios from "axios";
import { IOmdbResponse } from "../models/IOmdbResponse";

const movies = ref<IMovie[]>();

const userInput = ref("");

onMounted(() => {
  const searchText = localStorage.getItem("searchText") || "Harry";
  axios
    .get<IOmdbResponse>(
      "http://www.omdbapi.com/?apikey=4a05f679&s=" + searchText
    )
    .then((response) => (movies.value = response.data.Search));
});

const handleSubmit = async () => {
  try {
    let response = await axios.get<IOmdbResponse>(
      "http://www.omdbapi.com/?apikey=4a05f679&s=" + userInput.value
    );
    movies.value = response.data.Search;
    localStorage.setItem("searchText", userInput.value);
    userInput.value = "";
  } catch (error) {
    console.error(error);
  }
};
</script>

<template>
  <form @submit.prevent="handleSubmit">
    <input type="text" v-model="userInput" />
    <button>Sök</button>
  </form>

  <div v-for="movie in movies" :key="movie.imdbID">
    <h3>{{ movie.Title }}</h3>
    <div>
      <img :src="movie.Poster" :alt="movie.Title" />
    </div>
    <a href="#">Läs mer...</a>
  </div>
</template>

<style scoped></style>
