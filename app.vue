<template>
  <div @keyup.space="increaseHandTotal" class="container">
    <p class="is-size-2">Total hands: {{ totalHands }}</p>
    <button
      :disabled="!buttonIsInteractable"
      class="hand-total button is-primary"
      @click="increaseHandTotal"
    >
      + Hand
    </button>
    <div class="columns is-multiline">
      <div v-for="(player, index) in totalPlayers" class="column is-one-third">
        <StatController
          :playerId="index + 1"
          :totalHands="totalHands"
          :handsSeen="handsSeenByPlayer"
          @checkInteractable="checkInteractable"
        />
      </div>
    </div>

    <button class="hand-total button is-primary" @click="increaseHandTotal">
      Force inc
    </button>
  </div>
</template>

<script setup>
const totalHands = ref(0);
const totalPlayers = ref(9);
const buttonIsInteractable = ref(true);

onMounted(() => {
  window.addEventListener("keyup", (e) => {
    if (e.code === "Space") {
      increaseHandTotal();
    }
  });
});

onUnmounted(() => {
  window.removeEventListener("keyup");
});

const increaseHandTotal = () => {
  totalHands.value += 1;
  buttonIsInteractable.value = false;
};

const checkInteractable = () => {
  buttonIsInteractable.value = true;
};
</script>

<style scoped>
.hand-total {
  margin: 1rem 0;
}
</style>
