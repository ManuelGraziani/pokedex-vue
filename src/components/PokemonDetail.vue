<script setup>
import { onMounted, ref } from 'vue';
import { useRoute } from 'vue-router';

const route = useRoute();
const pokemon = ref({});
const isLoading = ref(false);
const audioUrl = ref('');

const playAudio = () => {
  if (audioUrl.value) {
    const audio = new Audio(audioUrl.value);
    audio.play();
  }
};

onMounted(() => {
  getPokemon(route.params.id);
});

const getPokemon = async (id) => {
  const url = `https://pokeapi.co/api/v2/pokemon/${id}`;
  try {
    isLoading.value = true;
    const response = await fetch(url);
    const data = await response.json();
    pokemon.value = data;
    audioUrl.value = pokemon.value.cries?.latest;
    isLoading.value = false;

    if (data.cries && data.cries.latest) {
      audioUrl.value = data.cries.latest;
      console.log(audioUrl.value);
      //   playAudio(audioUrl.value);
    }
  } catch (error) {
    console.error(error);
  }
};
</script>

<template>
  <div class="container mt-5 d-flex justify-content-center align-items-center">
    <div v-if="isLoading" class="spinner-border" role="status">
      <span class="visually-hidden">Loading...</span>
    </div>
    <div v-else class="card mx-auto" style="width: 18rem">
      <img
        :src="pokemon.sprites?.versions['generation-v']['black-white'].animated.front_default"
        :alt="pokemon.name"
        class="card-img-top"
      />

      <div class="card-body text-center">
        <h5 class="card-title text-capitalize">#{{ pokemon.id }} - {{ pokemon.name }}</h5>

        <div class="mb-3">
          <span
            v-for="(type, index) in pokemon.types"
            :key="index"
            class="badge rounded-pill bg-primary me-1"
          >
            {{ type.type.name }}
          </span>
        </div>

        <ul class="list-group list-group-flush">
          <li
            v-for="(stat, index) in pokemon.stats"
            :key="index"
            class="list-group-item d-flex justify-content-between align-items-center"
          >
            <span class="text-capitalize">{{ stat.stat.name }}</span>
            <span class="badge bg-secondary">{{ stat.base_stat }}</span>
          </li>
        </ul>

        <button v-if="audioUrl" @click="playAudio" class="btn btn-danger mt-3">Listen Cry</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}
.card-img-top {
  width: 120px;
  object-fit: cover;
  margin: auto;
}
</style>
