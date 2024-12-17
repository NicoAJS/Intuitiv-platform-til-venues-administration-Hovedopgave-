<script setup lang="ts">
import ActionColumn from "@/components/data-table/action-column/ActionColumn.vue";
import { useGlobalStore } from "@/domain/useGlobalStore.ts";
import { Venue } from "@/domain/venue/model/Venue.ts";
import { useDeleteVenue } from "@/domain/venue/useDeleteVenue.ts";
import { useVenueStore } from "@/domain/venue/useVenueStore.ts";
import CreateVenueDialog from "@/views/admin/venue-section/venues/dialogs/CreateVenueDialog.vue";
import EditVenueDialog from "@/views/admin/venue-section/venues/dialogs/EditVenueDialog.vue";
import { DataTablePageEvent } from "primevue/datatable";
import { useConfirm } from "primevue/useconfirm";
import { computed, onBeforeMount, ref } from "vue";

const confirm = useConfirm();
const venueStore = useVenueStore();
const globalStore = useGlobalStore();
const deleteVenue = useDeleteVenue();

const venues = computed(() => (venueStore.venues.isSuccess ? venueStore.venues.data : []));
const totalRecords = computed(() => (venueStore.venues.isSuccess ? (venueStore.venues.meta.total ?? 0) : 0));

const selectedVenueId = ref<string | null>(null);
const selectedVenue = computed(() => venues.value.find((venue) => venue.id === selectedVenueId.value));

const showCreateDialog = ref(false);
const showEditDialog = ref(false);

onBeforeMount(async () => {
  globalStore.showProgressBar();
  await venueStore.loadVenuePage("useCache");
  globalStore.hideProgressBar();
});

function onEditVenue(venue: Venue) {
  selectedVenueId.value = venue.id;
  showEditDialog.value = true;
}

function onDeleteVenue(venue: Venue) {
  selectedVenueId.value = venue.id;

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
      <DataTable
        data-key="id"
        size="small"
        striped-rows
        lazy
        paginator
        paginator-position="top"
        :first="venueStore.venuePage * venueStore.venuePerPage"
        :rows="venueStore.venuePerPage"
        :value="venues"
        :total-records="totalRecords"
        :loading="venueStore.venues.status !== 'done'"
        @page="(event: DataTablePageEvent) => venueStore.setVenuePage(event.page)"
      >
        <template #paginatorstart>
          <Button v-tooltip.bottom="'Refresh'" icon="pi pi-refresh" text @click="venueStore.loadVenuePage()" />
        </template>
        <template #paginatorend>
          <Button v-tooltip.bottom="'New Venue'" icon="pi pi-plus" text @click="() => (showCreateDialog = true)" />
        </template>
        <Column field="name" header="Name" />
        <Column field="code" header="Code" />
        <Column field="shopifyId" header="Shopify ID" />

        <ActionColumn
          v-if="true"
          :actions="[
            { icon: 'pi-eye', onClick: onEditVenue },
            { icon: 'pi-trash', severity: 'danger', onClick: onDeleteVenue },
          ]"
        />
      </DataTable>
    </template>
  </Card>
</template>

<style scoped>
.card {
  width: 100%;
}
</style>
