<template>
  <div class="container">
    <p class="is-size-2">Total hands: {{ totalHands }}</p>
    <button class="hand-total button is-primary" @click="increaseHandTotal">
      + Hand
    </button>
    <div class="columns is-multiline">
      <div v-for="(player, index) in totalPlayers" class="column is-one-third">
        <StatController
          :playerId="index + 1"
          :totalHands="totalHands"
          :handsSeen="handsSeenByPlayer"
          @clearStats="clearStats"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
const totalHands = ref(0);
const totalPlayers = ref(9);

const increaseHandTotal = () => {
  totalHands.value += 1;
};
</script>

<style scoped>
.hand-total {
  margin: 1rem 0;
}
</style>
