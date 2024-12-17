<script setup lang="ts">
import TabView from "primevue/tabview";
import TabPanel from "primevue/tabpanel";
import VenueInfo from "./venue-info.vue";
import VenueLocation from "./venue-location.vue";
import VenueSpaces from "@/components/venue-profile/venue-spaces.vue";
import VenueMedia from "./venue-media.vue";
import VenueTags from "@/components/venue-profile/venue-tags.vue";
</script>

<template>
  <div class="venue-tabs">
    <TabView>
      <!-- Tab 1: Venue Information -->
      <TabPanel header="Venue Information">
        <VenueInfo />
        <hr />
        <VenueLocation />
        <!-- Added VenueLocation -->
        <div class="save-button">
          <button @click="saveVenueInfo">Save</button>
        </div>
      </TabPanel>

      <!-- Tab 2: Media -->
      <TabPanel header="Media">
        <VenueMedia />
        <hr />
        <VenueTags />
        <div class="save-button">
          <button @click="saveMedia">Save</button>
        </div>
      </TabPanel>

      <!-- Tab 3: Spaces -->
      <TabPanel header="Spaces">
        <VenueSpaces />
        <div class="save-button">
          <button @click="saveSpaces">Save</button>
        </div>
      </TabPanel>
    </TabView>
  </div>
</template>

<style scoped>
.venue-tabs {
  width: 100%;
  margin: 0 auto;
  padding: 1rem;
}
hr {
  margin: 2rem 0;
  border: none;
  border-top: 1px solid #ccc;
}
.save-button {
  margin-top: 1rem;
  text-align: right;
}
button {
  padding: 0.5rem 1rem;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
button:hover {
  background-color: #0056b3;
}
</style>
