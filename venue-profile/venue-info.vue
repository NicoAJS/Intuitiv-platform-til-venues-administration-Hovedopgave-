<script setup lang="ts">
import FloatLabel from "primevue/floatlabel";
import InputGroup from "primevue/inputgroup";
import InputGroupAddon from "primevue/inputgroupaddon";
import { computed, onMounted, ref } from "vue";
import SelectInput from "@/components/select-input/Select-Input.vue";
import TextInput from "@/components/text-input/TextInput.vue";

interface Options {
  items: { name: string; id: string }[];
  label: string;
  value: string;
}

const selectedCategory = ref<string | undefined>();
const categories = ref<{ name: string; id: string }[]>([]);
const loadingCategories = ref<boolean>(false);
const errorFetchingCategories = ref<boolean>(false);

const fetchCategories = async () => {
  loadingCategories.value = true;
  errorFetchingCategories.value = false;
  try {
    const response = await fetch("https://api.example.com/categories");
    const data = await response.json();
    categories.value = data.map((item: { name: string; id: string }) => ({
      name: item.name,
      id: item.id,
    }));
  } catch (error) {
    console.error("Error fetching categories:", error);
    errorFetchingCategories.value = true;
  } finally {
    loadingCategories.value = false;
  }
};

onMounted(() => {
  fetchCategories();
});

const options = computed<Options>(() => ({
  items: categories.value,
  label: "name",
  value: "id",
}));

const tooltipText = computed(() => {
  const lines: string[] = [];
  if (loadingCategories.value) lines.push("Loading categories...");
  if (errorFetchingCategories.value) lines.push("Failed to load categories.");
  return lines.join("\n") || undefined;
});

const emit = defineEmits<{ hide: [] }>();
</script>

<template>
  <div class="details-component">
    <h1>Venue Profile</h1>
    <h3>Details</h3>
    <TextInput id="venueName" label="Venue Name" />
    <br />
    <div class="input-section">
      <InputGroup>
        <FloatLabel variant="in">
          <SelectInput id="selectCategory" label="Category" :options="options" v-model="selectedCategory" />
        </FloatLabel>
        <InputGroupAddon v-if="tooltipText">
          <div v-tooltip.bottom="tooltipText" class="pi pi-info-circle" />
        </InputGroupAddon>
        <button v-if="errorFetchingCategories.value" class="retry-button" @click="fetchCategories">Retry</button>
      </InputGroup>
    </div>

    <p v-if="errorFetchingCategories.value" class="error-text">Could not fetch categories. Please try again.</p>

    <div class="input-section">
      <InputText v-model="description" placeholder="Enter description" style="width: 100%" />
    </div>
  </div>
</template>

<style scoped>
.details-component {
  max-width: 800px;
  margin: 0 auto;
  padding: 1rem;
}

.details-component h1 {
  margin-bottom: 1rem;
}

.details-component h3 {
  margin-top: 0.5rem;
}

.details-component label {
  display: block;
  margin: 0.5rem 0 0.25rem;
  font-weight: bold;
}

.details-component p {
  margin: 0.5rem 0;
}

.retry-button {
  margin-top: 0.5rem;
  padding: 0.25rem 0.5rem;
  background-color: #10b981;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1rem;
}

.retry-button:hover {
  background-color: #059669;
}

.error-text {
  color: red;
  font-size: 0.9rem;
  margin-top: 0.5rem;
}

.pi-info-circle {
  font-size: 1.2rem;
  cursor: pointer;
}

.input-section {
  margin-bottom: 1rem; /* Adds space between sections */
}

/* Scoped style for SelectInput by ID */
#selectCategory {
  width: 100%; /* Ensures full width */
}
</style>
