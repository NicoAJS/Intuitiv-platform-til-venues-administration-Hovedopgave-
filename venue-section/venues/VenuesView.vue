<script setup lang="ts">
import TextInput from "@/components/text-input/TextInput.vue";
import { DataTableColumnBodyScope } from "@/domain/model/primevue/DataTable.ts";
import { Venue } from "@/domain/venue/model/Venue.ts";
import { useDeleteVenue } from "@/domain/venue/useDeleteVenue.ts";
import { useVenueStore } from "@/domain/venue/useVenueStore.ts";
import CreateVenueDialog from "@/views/admin/venue-section/venues/dialogs/CreateVenueDialog.vue";
import EditVenueDialog from "@/views/admin/venue-section/venues/dialogs/EditVenueDialog.vue";
import { DataTableFilterMetaData } from "primevue/datatable";
import { PopoverMethods } from "primevue/popover";
import { useConfirm } from "primevue/useconfirm";
import { computed, ref } from "vue";
import { useRouter } from "vue-router";
import VenueProfile from "@/components/venue-profile/venueProfile.vue";

type ColumnScope = DataTableColumnBodyScope<Venue>;
interface Filters {
  name: DataTableFilterMetaData;
}

const confirm = useConfirm();
const router = useRouter();
const venueStore = useVenueStore();
const deleteVenue = useDeleteVenue();

const venues = computed(() => (venueStore.venues.isSuccess ? venueStore.venues.data : []));
const totalRecords = computed(() => (venueStore.venues.isSuccess ? venueStore.venues.data.length : 0));

const selectedVenueId = ref<string | null>(null);
const selectedVenue = computed(() => venues.value.find((venue) => venue.id === selectedVenueId.value));

const showCreateDialog = ref(false);
const showEditDialog = ref(false);

const filterPopover = ref<PopoverMethods>();
const filters = ref<Filters>({
  name: { value: "", matchMode: "contains" },
});
const hasQuery = computed(() => filters.value.name.value.length > 0);

function onEdit(venue: Venue) {
  router.push({ name: "venue", params: { id: venue.id } });
}

function onDelete(venue: Venue) {
  confirm.require({
    message: "Are you sure you want to delete this venue?",
    header: "Confirmation",
    icon: "pi pi-exclamation-triangle",
    rejectLabel: "Cancel",
    rejectClass: "p-button-secondary p-button-outlined",
    acceptLabel: "Delete",
    acceptClass: "p-button-danger",
    accept: async () => deleteVenue.execute(venue.id),
  });
}

function clearFilters() {
  filters.value.name.value = "";
}
</script>

<template>
  <CreateVenueDialog v-model:visible="showCreateDialog" @create-successful="() => (showCreateDialog = false)" />
  <EditVenueDialog
    v-if="selectedVenue != null"
    v-model:visible="showEditDialog"
    :venue="selectedVenue"
    @edited-successful="() => (showEditDialog = false)"
  />

  <Card class="card">
    <template #title>Venues</template>
    <template #content>
      <Popover ref="filterPopover">
        <div class="filters">
          <TextInput id="nameFilter" v-model="filters.name.value" label="Name" />
        </div>
      </Popover>
    </template>
  </Card>

  <venue-profile class="venue-profile-with-spacing" />
</template>

<style scoped>
.card {
  width: 100%;
}

.filters {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
}

.popover {
  width: 100%;
}

.venue-profile-with-spacing {
  margin-bottom: 2rem; /* Adds space below venue profile */
}
</style>
