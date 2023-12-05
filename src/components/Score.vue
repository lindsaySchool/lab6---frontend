<script setup>
import { ref, onMounted } from 'vue';


const scores = ref([]);
const teams = ref([]);
const selectedTeam = ref(null);
const newScore = ref('');


// Maak een nieuwe Primus-verbinding
let primus = new Primus("https://lab6-ej2l.onrender.com/api/v1/scores");

primus.on('open', function () {
  console.log("Connection is open");
});

primus.on('data', (json) => {
  console.log("Onmessage:" + json);
  if(json.action == "update"){
    scores.value = json.data.scores;
    console.log(`Scorelijst bijgewerkt`, json.data.scores);
  }else if(json.action == "init"){
    scores.value = json.data.scores;
    console.log(`Scorelijst bijgewerkt`, json.data.scores);
  }
});

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

    selectedScore.score = newScore.value;
    console.log(selectedScore);

    // Stuur een PUT-verzoek om de score bij te werken
    const response = await fetch(`https://lab6-ej2l.onrender.com/api/v1/scores/${selectedScore._id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ score: newScore.value, team: selectedTeam.value }),
    });

    const data = await response.json();
    if(primus.readyState == Primus.OPEN){
     primus.write(JSON.stringify({ action: 'update', data: {scores: scores.value}}));
    }
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
  <div class="scorelist">
    <h2 class="scorelist__title">Score list</h2>
    <h2 class="scorelist__update-title">Update the scores</h2>
    <div class="scorelist__update-form">
      <div class="scorelist__form-group">
        <label for="team-select" class="scorelist__form-label">Select team</label>
        <!--Add een dropdown box where I can select a team-->
        <select id="team-select" v-model="selectedTeam">
          <label for="team-select" class="scorelist__form-label">Select team</label>
          <option v-for="team in teams" :key="team.team" :value="team.team">
            {{ team.team }}
          </option>
        </select>
      </div>
      <div class="scorelist__form-group">
        <label for="score-input" class="scorelist__form-label">Add score</label>
        <input id="score-input" type="text" v-model="newScore" placeholder="00"/>
      </div>
      <div class="scorelist_form-group">
        <button @click="updateScore">Update</button>
      </div>
    </div>
    <h2 class="scorelist__scores-title">SCORES:</h2>
    <div>
      <ul class="scorelist__scores">
        <li v-for="score in scores" :key="score._id" class="scorelist__scores-item">
          {{ score.team }}: {{ score.score }}
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.scorelist {
  max-width: 600px;
  margin: 0 auto;
}

.scorelist__title {
  text-align: center;
  color: #fff;
  font-size: 2em;
  margin-bottom: 1em;
}

.scorelist__update-title,
.scorelist__scores-title {
  color: #fff;
  font-size: 1.5em;
  margin-bottom: 0.5em;
}

.scorelist__update-form {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  margin-bottom: 2em;
  background-color: #242424;
  padding: 1em;
  border-radius: 5px;
}

.scorelist__update-form select,
.scorelist__update-form input,
.scorelist__update-form button {
  margin-right: 1em;
  padding: 0.5em;
  border: none;
  border-radius: 5px;
}
.scorelist__update-form select,
.scorelist__update-form input {
  flex: 1;
}

.scorelist__update-form button {
  cursor: pointer;
  background-color: #007BFF;
  color: white;
  padding: 0.5em 2em;
}

.scorelist__update-form button:hover {
  background-color: #0056b3;
}
.scorelist__form-group {
  flex: 1;
  display: flex;
  flex-direction: column;
  margin-right: 1em;
}

.scorelist__form-label {
  margin-bottom: 0.5em;
  color: #fff;
}
.scorelist__scores {
  list-style: none;
  padding: 0;
}

.scorelist__scores-item {
  padding: 1em;
  border-bottom: 1px solid #333;
  background-color: #242424;
  margin-bottom: 1em;
  border-radius: 5px;
}
</style>
