<script setup lang="ts">
import { VenueRequest } from "@/domain/venue/model/VenueRequest.ts";
import { usePatchVenue } from "@/domain/venue/usePatchVenue.ts";
import { useVenueStore } from "@/domain/venue/useVenueStore.ts";
import VenueForm from "@/views/admin/venue-section/venues/dialogs/VenueForm.vue";
import VenueSpaceTable from "@/views/admin/venue-section/venues/venue/space-tab/VenueSpaceTable.vue";
import { computed, onMounted, ref } from "vue";
import { useRouter } from "vue-router";

export interface VenueViewProps {
  id: string;
}

const props = defineProps<VenueViewProps>();

const router = useRouter();
const venueStore = useVenueStore();

const venue = computed(() => venueStore.getVenue(props.id));

const currentTab = ref<string>("0");

const patchVenue = usePatchVenue();
const isLoading = computed(() => patchVenue.data.value.status === "fetching");

onMounted(async () => {
  await venueStore.loadVenues("useCache");
});

async function onSaveInfo(request: VenueRequest) {
  if (venue.value) {
    await patchVenue.execute(request, venue.value);
  }
}
</script>

<template>
  <Card class="card">
    <template #header>
      <Button icon="pi pi-arrow-left" text severity="secondary" @click="() => router.back()" />
    </template>
    <template #content>
      <Tabs v-if="venue" v-model:value="currentTab">
        <TabList>
          <Tab value="0">Information</Tab>
          <Tab value="1">Spaces</Tab>
        </TabList>
        <TabPanels>
          <TabPanel value="0">
            <VenueForm
              :venue="venue"
              :buttons="{ submit: { label: 'Save', icon: 'pi-save', isLoading: isLoading } }"
              @submit="onSaveInfo"
            />
          </TabPanel>
          <TabPanel value="1">
            <VenueSpaceTable :venue="venue" />
          </TabPanel>
        </TabPanels>
      </Tabs>
    </template>
  </Card>
</template>

<style scoped>
.card {
  width: 100%;
}
</style>
