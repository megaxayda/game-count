<template>
  <div>
    <!-- Header with Add Player and Reset All buttons -->
    <v-row class="mb-6">
      <v-col cols="12">
        <v-card class="pa-4">
          <v-card-text>
            <v-row align="center" justify="center">
              <v-col cols="12" md="6">
                <v-text-field
                  v-model="newPlayerName"
                  label="Enter player name"
                  prepend-icon="mdi-account-plus"
                  @keyup.enter="addPlayer"
                  outlined
                  dense
                  clearable
                ></v-text-field>
              </v-col>
              <v-col cols="12" md="3">
                <v-btn
                  @click="addPlayer"
                  color="success"
                  large
                  block
                  :disabled="!newPlayerName.trim()"
                >
                  <v-icon left>mdi-plus</v-icon>
                  Add Player
                </v-btn>
              </v-col>
              <v-col cols="12" md="3">
                <v-btn
                  @click="resetAllCounters"
                  color="error"
                  large
                  block
                  :disabled="players.length === 0"
                >
                  <v-icon left>mdi-refresh</v-icon>
                  Reset All
                </v-btn>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>

    <!-- Players Grid -->
    <v-row v-if="players.length > 0">
      <v-col
        v-for="player in players"
        :key="player.id"
        cols="12"
        sm="6"
        md="4"
        lg="3"
      >
        <PlayerCard
          :player="player"
          @increment="incrementCounter"
          @decrement="decrementCounter"
          @delete="deletePlayer"
        />
      </v-col>
    </v-row>

    <!-- Empty State -->
    <v-row v-else>
      <v-col cols="12">
        <v-card class="pa-8 text-center">
          <v-icon size="96" color="grey lighten-1"
            >mdi-account-group-outline</v-icon
          >
          <v-card-title class="justify-center text-h5 grey--text">
            No players added yet
          </v-card-title>
          <v-card-text class="text-body-1 grey--text">
            Add your first player using the form above to get started!
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>

    <!-- Statistics -->
    <v-row v-if="players.length > 0" class="mt-6">
      <v-col cols="12">
        <v-card class="pa-4">
          <v-card-title>
            <v-icon left>mdi-chart-bar</v-icon>
            Statistics
          </v-card-title>
          <v-card-text>
            <v-row>
              <v-col cols="6" sm="3">
                <VStatistic
                  title="Total Players"
                  :value="players.length"
                  color="primary"
                />
              </v-col>
              <v-col cols="6" sm="3">
                <VStatistic
                  title="Total Score"
                  :value="totalScore"
                  color="success"
                />
              </v-col>
              <v-col cols="6" sm="3">
                <VStatistic
                  title="Highest Score"
                  :value="highestScore"
                  color="warning"
                />
              </v-col>
              <v-col cols="6" sm="3">
                <VStatistic
                  title="Average Score"
                  :value="averageScore"
                  color="info"
                />
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>

    <!-- Snackbar for notifications -->
    <v-snackbar
      v-model="snackbar.show"
      :color="snackbar.color"
      timeout="3000"
      top
    >
      {{ snackbar.message }}
      <template v-slot:action="{ attrs }">
        <v-btn text v-bind="attrs" @click="snackbar.show = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from "vue";
import PlayerCard from "./PlayerCard.vue";
import VStatistic from "./VStatistic.vue";

// Reactive data
const newPlayerName = ref("");
const players = ref([]);
const snackbar = ref({
  show: false,
  message: "",
  color: "success",
});

// Load players from localStorage on component mount
onMounted(() => {
  const savedPlayers = localStorage.getItem("players");
  if (savedPlayers) {
    players.value = JSON.parse(savedPlayers);
  }
});

// Watch for changes in players and save to localStorage
watch(
  players,
  (newPlayers) => {
    localStorage.setItem("players", JSON.stringify(newPlayers));
  },
  { deep: true }
);

// Generate unique ID for players
const generateId = () => {
  return Date.now() + Math.random().toString(36).substring(2);
};

// Add new player
const addPlayer = () => {
  if (newPlayerName.value.trim()) {
    const newPlayer = {
      id: generateId(),
      name: newPlayerName.value.trim(),
      counter: 0,
    };
    players.value.push(newPlayer);
    showSnackbar(`Player "${newPlayer.name}" added successfully!`, "success");
    newPlayerName.value = "";
  }
};

// Delete player
const deletePlayer = (playerId) => {
  const playerIndex = players.value.findIndex((p) => p.id === playerId);
  if (playerIndex !== -1) {
    const playerName = players.value[playerIndex].name;
    players.value.splice(playerIndex, 1);
    showSnackbar(`Player "${playerName}" removed`, "info");
  }
};

// Increment counter
const incrementCounter = (playerId) => {
  const player = players.value.find((p) => p.id === playerId);
  if (player) {
    player.counter++;
  }
};

// Decrement counter
const decrementCounter = (playerId) => {
  const player = players.value.find((p) => p.id === playerId);
  if (player && player.counter > 0) {
    player.counter--;
  }
};

// Reset all counters
const resetAllCounters = () => {
  players.value.forEach((player) => {
    player.counter = 0;
  });
  showSnackbar("All counters reset to zero!", "warning");
};

// Show snackbar notification
const showSnackbar = (message, color = "success") => {
  snackbar.value.message = message;
  snackbar.value.color = color;
  snackbar.value.show = true;
};

// Computed statistics
const totalScore = computed(() => {
  return players.value.reduce((sum, player) => sum + player.counter, 0);
});

const highestScore = computed(() => {
  if (players.value.length === 0) return 0;
  return Math.max(...players.value.map((player) => player.counter));
});

const averageScore = computed(() => {
  if (players.value.length === 0) return 0;
  return Math.round((totalScore.value / players.value.length) * 10) / 10;
});
</script>

<style scoped>
.v-statistic {
  text-align: center;
}
</style>
