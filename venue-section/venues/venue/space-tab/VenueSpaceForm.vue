<script setup lang="ts">
import ActionBar from "@/components/action-bar/ActionBar.vue";
import TextInput from "@/components/text-input/TextInput.vue";
import { useToastStore } from "@/domain/toast/useToastStore.ts";
import { VenueSpace } from "@/domain/venue/model/Venue.ts";
import { VenueSpaceRequest } from "@/domain/venue/model/VenueSpaceRequest.ts";
import { RequiredNonNull } from "@/util/RequiredNonNull.ts";
import { computed, onBeforeMount, ref } from "vue";

type WorkingData = RequiredNonNull<VenueSpaceRequest>;

interface Props {
  space?: VenueSpace;
  buttons: {
    submit: { label: string; icon: string; isLoading: boolean };
  };
}

const props = defineProps<Props>();
const emit = defineEmits<{ submit: [VenueSpaceRequest] }>();
const { toastError } = useToastStore();
const workingCopy = ref<WorkingData>(getOriginalValues());

const originalValues = computed(getOriginalValues);

function getOriginalValues(): WorkingData {
  return {
    name: props.space?.name ?? "",
    metaobjectId: props.space?.metaobjectId ?? "",
  };
}

const isDirty = computed(() => {
  const original = originalValues.value;
  return workingCopy.value.name.trim() !== original.name;
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
  const metaobjectId = workingData.metaobjectId.trim();
  if (!name || !metaobjectId) {
    toastError("Error", "Please fill out all required fields");
    return;
  }

  const request: VenueSpaceRequest = { name, metaobjectId };

  emit("submit", request);
}
</script>

<template>
  <div class="content">
    <div class="form">
      <TextInput id="spaceName" v-model="workingCopy.name" label="Name" auto-focus required />
      <TextInput
        id="metaobjectId"
        v-model="workingCopy.metaobjectId"
        label="Shopify Metaobject ID"
        :disabled="props.space != null"
        required
      />
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
</style>
