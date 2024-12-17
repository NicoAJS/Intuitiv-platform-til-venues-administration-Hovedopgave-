<script setup lang="ts">
import { VenueSpaceRequest } from "@/domain/venue/model/VenueSpaceRequest.ts";
import { useCreateVenueSpace } from "@/domain/venue/useCreateVenueSpace.ts";
import VariantSpaceForm from "@/views/admin/venue-section/venues/venue/space-tab/VenueSpaceForm.vue";
import { computed } from "vue";

interface Props {
  venueId: string;
}

const visible = defineModel<boolean>();
const props = defineProps<Props>();
const emit = defineEmits<{ createSuccessful: [] }>();

const createSpace = useCreateVenueSpace();
const isLoading = computed(() => createSpace.data.value.status === "fetching");

async function onSubmit(request: VenueSpaceRequest) {
  await createSpace.execute(props.venueId, request);
  if (createSpace.data.value.isSuccess) {
    emit("createSuccessful");
  }
}
</script>

<template>
  <Dialog v-model:visible="visible" modal header="Space Details" dismissable-mask>
    <VariantSpaceForm
      class="content"
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
