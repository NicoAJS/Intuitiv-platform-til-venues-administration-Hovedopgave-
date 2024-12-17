<template>
  <div class="venue-tags">
    <h3>Additional Information</h3>
    <div v-for="tag in predefinedTags" :key="tag" class="tag-section">
      <p>{{ tag }}</p>
      <div style="margin-top: 0.5rem">
        <input v-model="newTagValues[tag]" placeholder="Add a new tag" class="p-inputtext p-component" />
        <button @click="addTag(tag)" class="p-button p-component p-button-outlined p-button-secondary">Add Tag</button>
      </div>

      <!-- Use PrimeVue Chip component for displaying added tags -->
      <div v-for="addedTag in tags[tag]" :key="addedTag.value" class="tag-display">
        <Chip :label="addedTag.value" :style="{ backgroundColor: tagColors[tag], color: '#fff' }" />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import Chip from "primevue/chip"; // Import the Chip component

// Define the list of predefined tags
const predefinedTags = ["Architect", "Interior Designer", "Agency"];

// Reactive object to store added tags for each predefined category
const tags = ref<Record<string, { value: string }[]>>({});

// Reactive object to handle new tag inputs for each category
const newTagValues = ref<Record<string, string>>(
  predefinedTags.reduce(
    (acc, tag) => {
      acc[tag] = ""; // Initialize each tag input field with an empty string
      return acc;
    },
    {} as Record<string, string>,
  ),
);

const tagColors = {
  Architect: "rgba(30, 144, 255, 0.6)", // Blue color for Architect
  "Interior Designer": "rgba(50, 205, 50, 0.6)", // Green color for Interior Designer
  Agency: "rgba(255, 99, 71, 0.6)", // Red color for Agency
};

// Function to add a new tag to the corresponding category
function addTag(predefinedTag: string) {
  const newTagValue = newTagValues.value[predefinedTag].trim();
  if (newTagValue) {
    if (!tags.value[predefinedTag]) {
      tags.value[predefinedTag] = []; // Initialize the array if it doesn't exist
    }
    tags.value[predefinedTag].push({ value: newTagValue }); // Add the new tag
    newTagValues.value[predefinedTag] = ""; // Clear the input field
  }
}
</script>

<style scoped>
.venue-tags {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  justify-content: center;
  align-items: center;
  margin-top: 1rem;
  max-width: 800px;
}

.tag-section {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.venue-tags p {
  font-size: 0.9rem;
  font-weight: bold;
}

.tag-display {
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
}
</style>
