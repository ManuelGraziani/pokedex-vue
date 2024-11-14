<script setup>
import { onMounted, ref } from 'vue'
const pokeList = ref([])
onMounted(() => {
  getPokemon()
})

const getPokemon = async () => {
  const url = 'https://pokeapi.co/api/v2/pokemon'
  try {
    const response = await fetch(url)
    const data = await response.json()
    pokeList.value = data.results
  } catch (error) {
    console.error(error)
  }
}
</script>

<template>
  <div class="container my-4">
    <div class="row g-4">
      <div v-for="(pokemon, index) in pokeList" :key="index" class="col-6 col-md-4 col-lg-3">
        <div class="card text-center">
          <img
            :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${index + 1}.png`"
            :alt="pokemon.name"
            class="card-img-top p-3"
          />
          <div class="card-body">
            <h5 class="card-title text-capitalize">{{ pokemon.name }}</h5>
          </div>
        </div>
      </div>
    </div>
    <!-- <div class="search-container text-center">
      <input class="search-box" type="text" placeholder="Search..." v-model="searchTerm" />
    </div> -->
  </div>
</template>

<style scoped>
.card {
  border: none;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.card-img-top {
  width: 150px;
  height: 100px;
  object-fit: cover;
  margin: auto;
}
</style>
