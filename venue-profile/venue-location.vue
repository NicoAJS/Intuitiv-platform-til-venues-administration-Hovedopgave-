<template>
  <div class="venue-location">
    <h3>Location</h3>

    <!-- Country and City Dropdowns -->
    <div class="dropdowns">
      <!-- Country Dropdown -->
      <div class="dropdown-item">
        <p>Country</p>
        <SelectInput
          v-model="selectedCountry"
          :options="countries"
          label="Country"
          :loading="loadingCountries"
          :tooltip="loadingCountries ? 'Loading countries...' : null"
          @update:selected="fetchCities"
        />
        <p v-if="loadingCountries">Loading countries...</p>
      </div>

      <!-- City Dropdown -->
      <div class="dropdown-item">
        <p>City</p>
        <SelectInput
          v-model="selectedCity"
          :options="cities"
          label="City"
          :loading="loadingCities"
          :tooltip="loadingCities ? 'Loading cities...' : null"
          :disabled="loadingCities || !selectedCountry"
        />
        <p v-if="loadingCities">Loading cities...</p>
      </div>
    </div>

    <!-- Latitude and Longitude Inputs -->
    <p>Latitude</p>
    <NumberInput v-model="latitude" id="Latitude" />

    <p>Longitude</p>
    <NumberInput v-model="longitude" id="Longitude" />
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref, watch } from "vue";
import SelectInput from "@/components/select-input/Select-Input.vue";
import NumberInput from "@/components/number-input/NumberInput.vue";

interface Country {
  name: string;
  code: string;
}

interface City {
  name: string;
  code: string;
}

const selectedCountry = ref<string | null>(null);
const selectedCity = ref<string | null>(null);
const latitude = ref<number | null>(null);
const longitude = ref<number | null>(null);

const countries = ref<Country[]>([]);
const cities = ref<City[]>([]);
const loadingCountries = ref<boolean>(false);
const loadingCities = ref<boolean>(false);

// Fetch countries
const fetchCountries = async () => {
  loadingCountries.value = true;
  try {
    const response = await fetch("https://api.example.com/countries");
    const data = await response.json();
    countries.value = data;
  } catch (error) {
    console.error("Error fetching countries:", error);
  } finally {
    loadingCountries.value = false;
  }
};

// Fetch cities based on selected country
const fetchCities = async (countryCode: string) => {
  loadingCities.value = true;
  try {
    const response = await fetch(`https://api.example.com/cities?country=${countryCode}`);
    const data = await response.json();
    cities.value = data;
  } catch (error) {
    console.error("Error fetching cities:", error);
  } finally {
    loadingCities.value = false;
  }
};

onMounted(() => {
  fetchCountries();
});

watch(selectedCountry, (newCountry) => {
  if (newCountry) {
    fetchCities(newCountry);
    selectedCity.value = null;
  }
});
</script>

<style scoped>
.venue-location {
  max-width: 800px;
  margin: 0 auto;
  padding: 1rem;

  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.venue-location h3 {
  margin-bottom: 1rem;
  font-size: 1.5rem;
  font-weight: bold;
}

.dropdowns {
  display: flex;
  gap: 1.5rem;
}

.dropdown-item {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.dropdown-item p {
  margin: 0.5rem 0;
  font-weight: bold;
}

.number-input p {
  margin-top: 1rem;
}
</style>
