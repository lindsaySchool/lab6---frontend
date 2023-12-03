<script setup>
import { ref, onMounted } from 'vue';

const scores = ref([]);
const teams = ref([]);
const selectedTeam = ref(null);
const newScore = ref('');

//haal alle scores op
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

//haal alle teams op
const fetchTeams = async () => {
  try {
    const response = await fetch('http://localhost:3000/api/v1/teams');
    const data = await response.json();
    teams.value = data.data.teams;
  } catch (err) {
    console.log(err);
  }
};


onMounted(() => {
  fetchScore();
  fetchTeams();
}); 

</script>

<template>
  <div>
    <h1>ScoreList</h1>
    <div class="updateScore">
      <!--Add een dropdown box where I can select a team-->
      <select v-model="selectedTeam">
        <option v-for="team in teams" :key="team.team" :value="team.team">
          {{ team.team }}
        </option>
      </select>
      <input type="text" v-model="newScore" />
      <button @click="updateScore">Update</button>
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
