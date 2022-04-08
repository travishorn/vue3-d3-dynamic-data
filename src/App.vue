<script setup>
import { computed, reactive } from "vue";
import ChartScatter from "./components/ChartScatter.vue";

// Raw data. Must be reactive since we use methods to modify it.
const whales = reactive([
  { age: 13, weight: 114 },
  { age: 25, weight:  48 },
  { age: 33, weight: 101 },
  { age: 43, weight:  75 },
  { age: 52, weight: 139 },
]);

// Transform raw data into usable version for scatterplot chart component. All
// it really does is tell the component that X is age and Y is weight. Computed
// so that it's always in-line with the raw data.
const points = computed(() => whales.map((whale) => {
  return {
    x: whale.age,
    y: whale.weight,
  };
}));

// Next 3 blocks are methods that modify the raw data. Adding, modifying, and
// removing data points.
const addWhale = () => {
  whales.push({
    age: Math.random() * 80,
    weight: Math.random() * 200,
  });
};

const modifyWhale = () => {
  if (whales.length > 0) {
    whales[Math.floor(Math.random() * whales.length)] = {
      age: Math.random() * 80,
      weight: Math.random() * 200,
    };
  }
};

const removeWhale = () => {
  if (whales.length > 0) {
    whales.splice(Math.floor(Math.random() * whales.length), 1);
  }
};
</script>

<template>
  <div class="container">
    <h1>Blue Whales</h1>

    <!-- Call the chart component with transformed data and axis labels -->
    <ChartScatter
      :points="points"
      label-x="Age (years)"
      label-y="Weight (tons)"
    />

    <!-- A button for each data modification method -->
    <button @click="addWhale">Add whale</button>
    <button @click="modifyWhale">Modify whale</button>
    <button @click="removeWhale">Remove whale</button>
  </div>
</template>

<style scoped>
  .container {
    max-width: 600px;
    margin: 2rem auto;
    text-align: center;
    font-family: Arial, Helvetica, sans-serif;
  }

  button {
    margin: 0.5rem;
  }
</style>
