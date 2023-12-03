<script setup>
import { ref, onMounted } from 'vue';

const scores = ref([]);
const teams = ref([]);
const selectedTeam = ref(null);
const newScore = ref('');


//haal alle scores op
const fetchScore = async () => {
  try{
    const response = await fetch('https://lab6-ej2l.onrender.com/api/v1/scores');
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
    const response = await fetch('https://lab6-ej2l.onrender.com/api/v1/teams');
    const data = await response.json();
    teams.value = data.data.teams;
  } catch (err) {
    console.log(err);
  }
};

// update de score op basis van het geselecteerde team
const updateScore = async () => {
  try {
    // Controleer of een team is geselecteerd
    if (!selectedTeam.value) {
      console.error('Geen team geselecteerd voor update');
      return;
    }

    // Zoek de score op basis van het geselecteerde team
    const selectedScore = scores.value.find(score => score.team === selectedTeam.value);

    if (!selectedScore) {
      console.error('Kan de score niet vinden voor het geselecteerde team');
      return;
    }

    // Stuur een PUT-verzoek om de score bij te werken
    const response = await fetch(`https://lab6-ej2l.onrender.com/api/v1/scores/${selectedScore._id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ score: newScore.value, team: selectedTeam.value }),
    });

    const data = await response.json();

    // Reset het nieuwe scores invoerveld
    newScore.value = '';
  } catch (error) {
    console.error('Fout bij het bijwerken van de score:', error);
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
