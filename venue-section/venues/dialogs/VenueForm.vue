<script setup lang="ts">
import ActionBar from "@/components/action-bar/ActionBar.vue";
import TextInput from "@/components/text-input/TextInput.vue";
import { DATE_FORMAT } from "@/domain/model/DateFormat.ts";
import { useToastStore } from "@/domain/toast/useToastStore.ts";
import { Venue } from "@/domain/venue/model/Venue.ts";
import { VenueRequest } from "@/domain/venue/model/VenueRequest.ts";
import { RequiredNonNull } from "@/util/RequiredNonNull.ts";
import { computed, onBeforeMount, ref } from "vue";

type WorkingData = RequiredNonNull<VenueRequest>;

interface Props {
  venue?: Venue;
  buttons: {
    submit: { label: string; icon: string; isLoading: boolean };
  };
}

const props = defineProps<Props>();
const emit = defineEmits<{ submit: [VenueRequest] }>();
const { toastError } = useToastStore();
const workingCopy = ref<WorkingData>(getOriginalValues());

const originalValues = computed(getOriginalValues);

function getOriginalValues(): WorkingData {
  return {
    name: props.venue?.name ?? "",
    locationId: props.venue?.locationId ?? "",
  };
}

const isDirty = computed(() => {
  const original = originalValues.value;
  return (
    workingCopy.value.name.trim() !== original.name || workingCopy.value.locationId?.trim() !== original.locationId
  );
});

onBeforeMount(() => {
  reset();
});

function reset() {
  workingCopy.value = structuredClone(originalValues.value);
}

function onSaved() {
  const workingData = workingCopy.value;
  const name = workingData.name.trim();
  if (!name) {
    toastError("Error", "Please fill out all required fields");
    return;
  }

  const request: VenueRequest = {
    name,
    locationId: workingData.locationId?.trim() || null,
  };

  emit("submit", request);
}
</script>

<template>
  <div class="content">
    <div class="form">
      <TextInput id="VenueName" v-model="workingCopy.name" label="Name" auto-focus required />
      <TextInput id="location" v-model="workingCopy.locationId" label="Shopify Location ID" />
      <div v-if="venue" class="data-section">
        <div>Redemption code:</div>
        <div class="data">{{ venue.code }}</div>
        <div>Created on:</div>
        <div class="data">{{ DATE_FORMAT.format(venue.createdAt) }}</div>
        <div>Last updated:</div>
        <div class="data">{{ DATE_FORMAT.format(venue.updatedAt) }}</div>
      </div>
    </div>
    <ActionBar
      :left-buttons="[
        { icon: 'pi-undo', tooltip: 'Reset', onClick: () => (workingCopy = getOriginalValues()), disabled: !isDirty },
      ]"
      :right-buttons="[{ icon: 'pi-save', tooltip: 'Save', onClick: onSaved, isLoading: buttons.submit.isLoading }]"
    />
  </div>
</template>

<style scoped>
.content {
  display: flex;
  flex-direction: column;
  gap: 1rem;

  .form {
    display: flex;
    flex-direction: column;
    gap: 1.5rem;
  }
}

.data-section {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 0.5rem;
}

.data {
  text-align: end;
}
</style>
