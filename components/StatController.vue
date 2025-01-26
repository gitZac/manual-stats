<template>
  <div
    @keyup.esc="flipCard"
    :class="`card has-text-white has-background-${getRangeColor}`"
  >
    <div class="container">
      <h2 class="is-size-3">Player {{ playerId }} : {{ getStyle }}</h2>

      <button class="button is-link" @click="increaseHand">
        + Money in pot
      </button>
      <button class="button is-danger" @click="increasePfr">
        + Preflop Raise
      </button>
      <div class="card-content">
        <div class="columns">
          <div class="column">
            <p class="is-size-5">
              vpip: <span class="has-text-weight-bold">{{ calcVpip }}%</span>
            </p>
            <p class="has-text-white is-size-5">
              PFR: <span class="has-text-weight-bold">{{ calcPfr }}%</span>
            </p>
          </div>
          <div class="column">
            <p>Hands played: {{ playedHands }} / {{ handsSeen }}</p>
            <p>Hands raised: {{ preFlopRaises }} / {{ playedHands }}</p>
          </div>
        </div>
      </div>

      <div class="columns">
        <div class="column">
          <button @click="flipCard" class="button is-info">Flip</button>
        </div>
        <div class="column">
          <button @click="clearStats" class="button is-danger">
            Clear Data
          </button>
        </div>
      </div>
    </div>

    <div :class="`modal ${isFlipped ? 'is-active' : ''}`">
      <div class="modal-background"></div>
      <div class="modal-content">
        <div class="columns">
          <div class="column">
            Raising Range
            <img :src="currentRange.raise" alt="" />
          </div>
          <div class="column">
            Calling Range
            <img :src="currentRange.call" alt="" />
          </div>
        </div>
      </div>
      <button
        @click="flipCard"
        class="modal-close is-large"
        aria-label="close"
      ></button>
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
const emit = defineEmits(["checkInteractable"]);
const opponentCalled = ref(false);
const opooonentRaised = ref(false);
const isFlipped = ref(false);

const rangeData = {
  nit: {
    raise:
      "https://www.splitsuit.com/nitropack_static/yYipRtHrrGgKGqoGltBMbGTmtbORZOjD/assets/images/optimized/rev-ba6dbfa/www.splitsuit.com/wp-content/uploads/2020/02/9-Percent-Range.jpg",
    call: "https://www.splitsuit.com/nitropack_static/yYipRtHrrGgKGqoGltBMbGTmtbORZOjD/assets/images/optimized/rev-ba6dbfa/www.splitsuit.com/wp-content/uploads/2020/02/8-Calling-Range.jpg",
  },
  rock: {
    raise:
      "https://www.splitsuit.com/nitropack_static/yYipRtHrrGgKGqoGltBMbGTmtbORZOjD/assets/images/optimized/rev-ba6dbfa/www.splitsuit.com/wp-content/uploads/2020/02/15-Percent-Range.jpg",
    call: "https://www.splitsuit.com/nitropack_static/yYipRtHrrGgKGqoGltBMbGTmtbORZOjD/assets/images/optimized/rev-ba6dbfa/www.splitsuit.com/wp-content/uploads/2020/02/16-Calling-Range.jpg",
  },
  lag: {
    raise:
      "https://www.splitsuit.com/nitropack_static/yYipRtHrrGgKGqoGltBMbGTmtbORZOjD/assets/images/optimized/rev-ba6dbfa/www.splitsuit.com/wp-content/uploads/2020/02/50-Percent-Range.jpg",
    call: "https://www.splitsuit.com/nitropack_static/yYipRtHrrGgKGqoGltBMbGTmtbORZOjD/assets/images/optimized/rev-ba6dbfa/www.splitsuit.com/wp-content/uploads/2020/02/30-Calling-Range.jpg",
  },
  tag: {
    raise:
      "https://www.splitsuit.com/nitropack_static/yYipRtHrrGgKGqoGltBMbGTmtbORZOjD/assets/images/optimized/rev-ba6dbfa/www.splitsuit.com/wp-content/uploads/2020/02/20-Percent-Range.jpg",
    call: "https://www.splitsuit.com/nitropack_static/yYipRtHrrGgKGqoGltBMbGTmtbORZOjD/assets/images/optimized/rev-ba6dbfa/www.splitsuit.com/wp-content/uploads/2020/02/16-Calling-Range.jpg",
  },
  passive: {
    raise:
      "https://www.splitsuit.com/nitropack_static/yYipRtHrrGgKGqoGltBMbGTmtbORZOjD/assets/images/optimized/rev-ba6dbfa/www.splitsuit.com/wp-content/uploads/2020/02/20-Percent-Range.jpg",
    call: "https://www.splitsuit.com/nitropack_static/yYipRtHrrGgKGqoGltBMbGTmtbORZOjD/assets/images/optimized/rev-ba6dbfa/www.splitsuit.com/wp-content/uploads/2020/02/50-Percent-Range.jpg",
  },
};

const currentRange = ref({});

const clearStats = () => {
  handsSeen.value = 0;
  playedHands.value = 0;
  vPip.value = 0;
  pfr.value = 0;
  preFlopRaises.value = 0;
};

const flipCard = () => {
  isFlipped.value = !isFlipped.value;
};

watch(
  () => props.totalHands,
  (newValue, oldValue) => {
    handsSeen.value += 1;
    opooonentRaised.value = false;
    opponentCalled.value = false;
  }
);

const checkInteractable = () => {
  emit("checkInteractable");
};

const increaseHand = () => {
  if (playedHands.value < handsSeen.value) {
    playedHands.value += 1;
    checkInteractable();
    opponentCalled.value = true;
  }
};

const increasePfr = () => {
  if (preFlopRaises.value < playedHands.value) {
    opponentCalled.value = false;
    opooonentRaised.value = true;
    preFlopRaises.value += 1;
    checkInteractable();
  }
};

const calcVpip = computed(() => {
  vPip.value = (playedHands.value / handsSeen.value) * 100;

  if (Number.isNaN(vPip.value)) {
    return 0;
  } else {
    return Math.round(vPip.value);
  }
});

const calcPfr = computed(() => {
  pfr.value = (preFlopRaises.value / playedHands.value) * 100;

  if (Number.isNaN(pfr.value)) {
    return 0;
  } else {
    return Math.round(pfr.value);
  }
});

const getStyle = computed(() => {
  if (isPassive()) {
    currentRange.value = rangeData["passive"];

    return "Passive";
  }
  if (isRock()) {
    currentRange.value = rangeData["rock"];

    return "ROCK";
  }
  if (isNit()) {
    currentRange.value = rangeData["nit"];
    return "NIT";
  }
  if (isVeryPassive()) {
    currentRange.value = rangeData["passive"];

    return "Very Passive";
  }
  if (isLooseAggressive()) {
    currentRange.value = rangeData["lag"];

    return "Loose Agg";
  }
  if (isTightAggressive()) {
    currentRange.value = rangeData["tag"];

    return "Tight Agg";
  }
});

const isVeryPassive = () => {
  return vPip.value >= 50 && pfr.value <= 30;
};

const isPassive = () => {
  return vPip.value >= 35 && vPip.value < 50 && pfr.value <= 30;
};

const isLooseAggressive = () => {
  return vPip.value >= 25 && pfr.value > 30;
};

const isRock = () => {
  return vPip.value >= 15 && vPip.value <= 30 && pfr.value <= 30;
};

const isNit = () => {
  return vPip.value < 15 && pfr.value <= 15;
};

const isTightAggressive = () => {
  return vPip.value <= 25 && pfr.value >= 50;
};

const getGraphData = computed(() => {});

const getRangeColor = computed(() => {
  let colorClass = "primary-dark";
  if (isPassive()) colorClass = "primary";
  if (isVeryPassive()) colorClass = "success";
  if (isLooseAggressive()) colorClass = "danger-dark";
  if (isTightAggressive()) colorClass = "warning";
  if (isRock()) colorClass = "text-dark";
  if (isNit()) colorClass = "text";

  return colorClass;
});
</script>

<style scoped>
.card {
  padding: 0 2rem;
}

.modal-content {
  width: 100% !important;
}

.button {
  margin: 0 0.5rem;
}

.card-content,
.modal img {
  padding: 2rem 0;
}

img {
  max-width: 500px;
}
</style>
