<script setup>
import router from '@/router'
import { useInfiniteScroll } from '@vueuse/core'
import { onMounted, ref } from 'vue'

const el = ref(null)
const pokeList = ref([])
const url = ref('https://pokeapi.co/api/v2/pokemon')
const isLoading = ref(false)
const searchedPokemon = ref('')

onMounted(async () => {
  await getPokemon()
})

const getPokemon = async () => {
  if (!url.value) return
  try {
    isLoading.value = true
    const response = await fetch(url.value)
    const data = await response.json()
    const pokemonWithId = data.results.map((pokemon) => {
      const id = pokemon.url.split('/').filter(Boolean).pop() // Ottiene l'id dall'URL
      return { ...pokemon, id }
    })
    pokeList.value = [...pokeList.value, ...pokemonWithId]
    url.value = data.next // update next URL for pagination
  } catch (error) {
    console.error(error)
  }
}

const pokemonDetail = (id) => {
  router.push(`/pokemon/${id}`)
  if (searchedPokemon.value) {
    router.push(`/pokemon/${searchedPokemon.value.trim().toLowerCase()}`)
  }
}

useInfiniteScroll(
  el,
  async () => {
    await getPokemon()
  },
  { distance: 10 },
)
</script>

<template>
  <div class="container my-4">
    <div class="search-container">
      <form @submit.prevent="pokemonDetail">
        <input
          class="search-box"
          type="text"
          placeholder="Search name or number"
          v-model="searchedPokemon"
        />
      </form>
    </div>
    <div class="row g-4">
      <div v-for="pokemon in pokeList" :key="pokemon.id" class="col-6 col-md-4 col-lg-3">
        <div class="card text-center" @click="pokemonDetail(pokemon.id)">
          <img
            :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${pokemon.id}.png`"
            :alt="pokemon.name"
            class="card-img-top p-3"
          />
          <div class="card-body">
            <h5 class="card-title text-capitalize">{{ pokemon.name }}</h5>
          </div>
        </div>
      </div>
      <div class="spinner-border" role="status" ref="el">
        <span class="visually-hidden">Loading...</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  border: none;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  cursor: pointer;
}

.card-img-top {
  width: 120px;
  object-fit: cover;
  margin: auto;
}

form {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  gap: 10px;
}

.search-box {
  width: 90%; /* Utilizza il 90% della larghezza dello schermo */
  max-width: 500px; /* Imposta un massimo per schermi più grandi */
  height: 50px;
  font-size: 1em; /* Riduce leggermente il font sui dispositivi più piccoli */
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin: 40px 0;
  text-align: center;
}
</style>
