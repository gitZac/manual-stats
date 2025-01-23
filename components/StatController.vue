<template>
  <div :class="`card has-text-white has-background-${getRangeColor}`">
    <div class="container">
      <h2 class="is-size-3">Player {{ playerId }} : {{ getStyle }}</h2>

      <button class="button is-link" @click="increaseHand">
        + Money in pot
      </button>
      <button class="button is-danger" @click="increasePfr">
        + Preflop Raise
      </button>
      <p class="is-size-5">
        vpip: <span class="has-text-weight-bold">{{ calcVpip }}%</span>
      </p>

      <p class="has-text-white is-size-5">
        PFR: <span class="has-text-weight-bold">{{ calcPfr }}%</span>
      </p>
      <p>Hands opponent has played: {{ playedHands }} / {{ handsSeen }}</p>
      <p>Hands opponent has raised: {{ preFlopRaises }} / {{ playedHands }}</p>
      <button @click="clearStats" class="button is-danger">Clear Data</button>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  playerId: {
    type: Number,
    default: 0,
  },
  totalHands: {
    type: Number,
    default: 0,
  },
});
const vPip = ref(0);
const pfr = ref(0);
const playedHands = ref(0);
const preFlopRaises = ref(0);
const handsSeen = ref(0);

const clearStats = () => {
  handsSeen.value = 0;
  playedHands.value = 0;
  vPip.value = 0;
  pfr.value = 0;
  preFlopRaises.value = 0;
};

watch(
  () => props.totalHands,
  (newValue, oldValue) => {
    // This watcher is triggered whenever 'myProp' changes
    handsSeen.value += 1;
  }
);

const increaseHand = () => {
  if (playedHands.value < handsSeen.value) {
    playedHands.value += 1;
  }
};

const increasePfr = () => {
  if (preFlopRaises.value < playedHands.value) preFlopRaises.value += 1;
};

const calcVpip = computed(() => {
  vPip.value = (playedHands.value / handsSeen.value) * 100;

  if (vPip.value == Infinity) {
    return 0;
  } else {
    return vPip.value;
  }
});

const calcPfr = computed(() => {
  pfr.value = (preFlopRaises.value / playedHands.value) * 100;

  if (pfr.value === NaN) {
    return 0;
  } else {
    return pfr.value;
  }
});

const getStyle = computed(() => {
  if (isPassive()) return "Passive";
  if (isRock()) return "ROCK";
  if (isNit()) return "NIT";
  if (isVeryPassive()) return "Very Passive";
  if (isLooseAggressive()) return "Loose Aggressive";
});

const isLooseAggressive = () => {
  return vPip.value >= 60 && pfr.value >= 50;
};

const isVeryPassive = () => {
  return vPip.value >= 60 && pfr.value <= 10;
};

const isPassive = () => {
  return vPip.value >= 40 && pfr.value <= 15;
};
const isRock = () => {
  return vPip.value < 25 && pfr.value <= 15;
};

const isNit = () => {
  return vPip.value <= 15 && pfr.value <= 15 && pfr.value >= 6;
};

const isTightAggressive = () => {
  return vPip.value < 25 && pfr.value >= 50;
};

const getRangeColor = computed(() => {
  let colorClass = "primary-dark";
  if (isPassive()) colorClass = "primary";
  if (isVeryPassive()) colorClass = "success";
  //   if (isNit()) colorClass = "link";
  if (isRock()) colorClass = "text-dark";
  if (isLooseAggressive()) colorClass = "danger-dark";
  if (isTightAggressive()) colorClass = "warning";

  return colorClass;
});
</script>

<style scoped>
.card {
  padding: 0 2rem;
}

.button {
  margin: 0 0.5rem;
}
</style>
