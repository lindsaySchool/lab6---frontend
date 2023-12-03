<script setup>
import { ref, onMounted } from 'vue';

const scores = ref([]);
const teams = ref([]);
const selectedTeam = ref(null);
const newScore = ref('');

const fetchScore = async () => {
  try{
    const response = await fetch('http://localhost:3000/api/v1/scores');
    const data = await response.json();
    scores.value = data.data.scores;
    teams.value = data.data.teams;
  } catch (err) {
    console.log(err);
  }
};

onMounted(() => {
  fetchScore();
}); 

</script>

<template>
  <div>
    <h1>ScoreList</h1>
    <div class="updateScore">
      <!--Add een dropdown box where I can select a team-->
      <select v-model="selectedTeam">
        <option v-for="team in teams" :key="team.id" :value="team.id">
          {{ team.name }}
        </option>
      </select>
      <input type="text" v-model="newScore" />
      <button @click="addScore">Update</button>
    </div>
    <div>
      <ul>
        <li v-for="score in scores" :key="score.id">
          {{ score.team }}: {{ score.score }}
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
</style>
