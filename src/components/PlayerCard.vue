<template>
  <v-card
    class="player-card"
    elevation="4"
    hover
    :class="{ 'player-card--highlighted': player.counter > 0 }"
  >
    <v-card-title class="text-center">
      <div class="player-name">
        {{ player.name }}
      </div>
    </v-card-title>

    <v-card-text class="text-center">
      <!-- Counter Display -->
      <div class="counter-section">
        <div class="counter-label">Score</div>
        <div class="counter-value" :class="counterColorClass">
          {{ player.counter }}
        </div>
      </div>

      <!-- Counter Controls -->
      <v-row class="mt-2" justify="center">
        <v-col cols="4">
          <v-btn
            @click="$emit('decrement', player.id)"
            color="error"
            fab
            small
            :disabled="player.counter <= 0"
          >
            <v-icon>mdi-minus</v-icon>
          </v-btn>
        </v-col>
        <v-col cols="4" class="text-center">
          <v-btn
            @click="resetPlayerCounter"
            color="warning"
            outlined
            small
            :disabled="player.counter === 0"
          >
            <v-icon>mdi-refresh</v-icon>
          </v-btn>
        </v-col>
        <v-col cols="4">
          <v-btn
            @click="$emit('increment', player.id)"
            color="success"
            fab
            small
          >
            <v-icon>mdi-plus</v-icon>
          </v-btn>
        </v-col>
      </v-row>
    </v-card-text>

    <v-card-actions class="justify-center">
      <v-btn @click="confirmDelete" color="error" text small>
        <v-icon left small>mdi-delete</v-icon>
        Remove Player
      </v-btn>
    </v-card-actions>

    <!-- Delete Confirmation Dialog -->
    <v-dialog v-model="deleteDialog" max-width="400">
      <v-card>
        <v-card-title class="text-h6">
          <v-icon color="error" left>mdi-alert-circle</v-icon>
          Confirm Deletion
        </v-card-title>
        <v-card-text>
          Are you sure you want to remove <strong>{{ player.name }}</strong
          >?
          <br />
          <span class="text-caption text--secondary">
            This action cannot be undone.
          </span>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn @click="deleteDialog = false" text> Cancel </v-btn>
          <v-btn @click="deletePlayer" color="error" text> Delete </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-card>
</template>

<script setup>
import { ref, computed, defineProps, defineEmits } from "vue";

// Props
const props = defineProps({
  player: {
    type: Object,
    required: true,
  },
});

// Emits
const emit = defineEmits(["increment", "decrement", "delete"]);

// Reactive data
const deleteDialog = ref(false);

// Computed properties
const avatarColor = computed(() => {
  // Generate a color based on player name
  const colors = [
    "primary",
    "secondary",
    "accent",
    "success",
    "info",
    "warning",
  ];
  const hash = props.player.name.split("").reduce((a, b) => {
    a = (a << 5) - a + b.charCodeAt(0);
    return a & a;
  }, 0);
  return colors[Math.abs(hash) % colors.length];
});

const counterColorClass = computed(() => {
  if (props.player.counter === 0) return "counter-zero";
  if (props.player.counter < 5) return "counter-low";
  if (props.player.counter < 10) return "counter-medium";
  return "counter-high";
});

// Methods
const confirmDelete = () => {
  deleteDialog.value = true;
};

const deletePlayer = () => {
  emit("delete", props.player.id);
  deleteDialog.value = false;
};

const resetPlayerCounter = () => {
  // Emit decrement events until counter reaches 0
  const currentCounter = props.player.counter;
  for (let i = 0; i < currentCounter; i++) {
    emit("decrement", props.player.id);
  }
};
</script>

<style scoped>
.player-card {
  transition: all 0.3s ease;
  border-radius: 12px;
}

.player-card--highlighted {
  border: 2px solid #4caf50;
  box-shadow: 0 4px 20px rgba(76, 175, 80, 0.3);
}

.player-card:hover {
  transform: translateY(-2px);
}

.player-name {
  font-weight: 600;
  font-size: 1.1rem;
  word-break: break-word;
}

.counter-label {
  font-size: 0.875rem;
  color: #666;
  margin-bottom: 8px;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.counter-value {
  font-size: 3rem;
  font-weight: 700;
  line-height: 1;
  transition: color 0.3s ease;
}

.counter-zero {
  color: #9e9e9e;
}

.counter-low {
  color: #ff9800;
}

.counter-medium {
  color: #2196f3;
}

.counter-high {
  color: #4caf50;
}

.v-btn--fab.v-size--small {
  height: 40px;
  width: 40px;
}
</style>
