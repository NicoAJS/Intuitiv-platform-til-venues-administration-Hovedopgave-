<script setup lang="ts">
import { Venue } from "@/domain/venue/model/Venue.ts";
import { VenueRequest } from "@/domain/venue/model/VenueRequest.ts";
import { usePatchVenue } from "@/domain/venue/usePatchVenue.ts";
import VenueForm from "@/views/admin/venue-section/venues/dialogs/VenueForm.vue";
import { computed } from "vue";

const visible = defineModel<boolean>();
const props = defineProps<{ venue: Venue }>();
const emit = defineEmits<{ editedSuccessful: [] }>();

const patchVenue = usePatchVenue();
const isLoading = computed(() => patchVenue.data.value.status === "fetching");

async function onSubmit(request: VenueRequest) {
  await patchVenue.execute(request, props.venue);
  if (patchVenue.data.value.isSuccess) {
    emit("editedSuccessful");
  }
}
</script>

<template>
  <Dialog v-model:visible="visible" modal :header="`Venue Details: ${venue.name}`" dismissable-mask>
    <VenueForm
      class="content"
      :venue="venue"
      :buttons="{ submit: { label: 'Save', icon: 'pi-save', isLoading: isLoading } }"
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
