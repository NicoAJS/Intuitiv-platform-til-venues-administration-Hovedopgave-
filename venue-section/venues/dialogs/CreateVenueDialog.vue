<script setup lang="ts">
import { VenueRequest } from "@/domain/venue/model/VenueRequest.ts";
import { useCreateVenue } from "@/domain/venue/useCreateVenue.ts";
import VenueForm from "@/views/admin/venue-section/venues/dialogs/VenueForm.vue";
import { computed } from "vue";

const visible = defineModel<boolean>();
const emit = defineEmits<{ createSuccessful: [] }>();

const createVenue = useCreateVenue();
const isLoading = computed(() => createVenue.data.value.status === "fetching");

async function onSubmit(request: VenueRequest) {
  await createVenue.execute(request);
  if (createVenue.data.value.status === "done") {
    emit("createSuccessful");
  }
}
</script>

<template>
  <Dialog v-model:visible="visible" modal header="Create Venue" dismissable-mask>
    <VenueForm
      class="content"
      :buttons="{ submit: { label: 'Create', icon: 'pi-save', isLoading: isLoading } }"
      @submit="(request) => onSubmit(request)"
    />
  </Dialog>
</template>

<style scoped>
.content {
  width: 40vw;
  min-width: 20rem;
}
</style>
