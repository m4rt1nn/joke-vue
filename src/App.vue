<template>
  <div class="joke-container">
    <div v-if="joke" class="joke-card">
      <h1 class="joke-setup">{{ joke?.setup }}</h1>
      <p class="joke-punchline">{{ joke?.punchline }}</p>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref } from "vue";
import { JokeValue } from "./model/joke-value.model";
import axios from "axios";

// Definiera gränssnitt för Skämtet
interface Joke {
  setup: string;
  punchline: string;
}

export default defineComponent({
  name: "App",
  setup() {
    // Skapa reaktiva variabler för skämt och felmeddelanden
    const joke = ref<JokeValue | null>(null);
    const error = ref<string | null>(null);

    // Asynkron metod för att hämta skämt
    const fetchJoke = async (): Promise<void> => {
      error.value = null; // Nollställ felet vid ny förfrågan
      try {
        const response = await axios.get<JokeValue>(
          "http://joke-spring.ubk3s/api/jokes/random"
        );
        joke.value = response.data; // Uppdatera skämtet med resultatet
      } catch (err) {
        error.value = "Failed to fetch joke."; // Om det sker ett fel, sätt ett felmeddelande
        console.error(err);
      }
    };

    onMounted(fetchJoke);

    return {
      joke,
      error,
    };
  },
});
</script>

<style>
body {
  font-family: "Arial", sans-serif;
  background-color: #f0f4f8;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0;
}

.joke-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
}

.joke-card {
  background-color: #fff;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  width: 300px;
  padding: 20px;
  text-align: center;
  transition: transform 0.3s ease-in-out;
}

.joke-card:hover {
  transform: scale(1.05);
}

.joke-setup {
  font-size: 1.5rem;
  font-weight: 600;
  margin-bottom: 20px;
  color: #333;
}

.joke-punchline {
  font-size: 1.25rem;
  color: #555;
  margin-top: 20px;
}
</style>
