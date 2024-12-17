<script setup lang="ts">
import { VenueSpace } from "@/domain/venue/model/Venue.ts";
import { VenueSpaceRequest } from "@/domain/venue/model/VenueSpaceRequest.ts";
import { usePatchVenueSpace } from "@/domain/venue/usePatchVenueSpace.ts";
import VariantSpaceForm from "@/views/admin/venue-section/venues/venue/space-tab/VenueSpaceForm.vue";
import { computed } from "vue";

interface Props {
  venueId: string;
  space: VenueSpace;
}

const visible = defineModel<boolean>();
const props = defineProps<Props>();
const emit = defineEmits<{ editedSuccessful: [] }>();

const patchSpace = usePatchVenueSpace();
const isLoading = computed(() => patchSpace.data.value.status === "fetching");

async function onSubmit(request: VenueSpaceRequest) {
  await patchSpace.execute(request, props.space, props.venueId);
  if (patchSpace.data.value.isSuccess) {
    emit("editedSuccessful");
  }
}
</script>

<template>
  <Dialog v-model:visible="visible" modal header="Space Details" dismissable-mask>
    <VariantSpaceForm
      class="content"
      :space="space"
      :buttons="{ submit: { label: 'Save', icon: 'pi-save', isLoading: isLoading } }"
      @submit="onSubmit"
    />
  </Dialog>
</template>

<style scoped>
.content {
  width: 40vw;
  min-width: 20rem;
}
</style>
