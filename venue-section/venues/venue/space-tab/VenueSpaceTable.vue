<script setup lang="ts">
import Table from "@/components/table/Table.vue";
import { DataTableColumnBodyScope } from "@/domain/model/primevue/DataTable.ts";
import { Venue, VenueSpace } from "@/domain/venue/model/Venue.ts";
import { useDeleteVenueSpace } from "@/domain/venue/useDeleteVenueSpace.ts";
import CreateVenueSpaceDialog from "@/views/admin/venue-section/venues/venue/space-tab/CreateVenueSpaceDialog.vue";
import EditVenueSpaceDialog from "@/views/admin/venue-section/venues/venue/space-tab/EditVenueSpaceDialog.vue";
import { useConfirm } from "primevue/useconfirm";
import { ref } from "vue";

type ColumnScope = DataTableColumnBodyScope<VenueSpace>;

const props = defineProps<{ venue: Venue }>();

const confirm = useConfirm();

const selectedSpace = ref<VenueSpace | null>(null);
const showEditDialog = ref<boolean>(false);
const showCreateDialog = ref<boolean>(false);

const deleteSpace = useDeleteVenueSpace();

function onEdit(space: VenueSpace) {
  selectedSpace.value = space;
  showEditDialog.value = true;
}

function onDelete(space: VenueSpace) {
  confirm.require({
    message: "Are you sure you want to delete this space?",
    header: "Confirmation",
    icon: "pi pi-exclamation-triangle",
    rejectLabel: "Cancel",
    rejectClass: "p-button-secondary p-button-outlined",
    acceptLabel: "Delete",
    acceptClass: "p-button-danger",
    accept: async () => deleteSpace.execute(props.venue.id, space.id),
  });
}
</script>

<template>
  <CreateVenueSpaceDialog
    v-model:visible="showCreateDialog"
    :venue-id="venue.id"
    @create-successful="() => (showCreateDialog = false)"
  />
  <EditVenueSpaceDialog
    v-if="selectedSpace != null"
    v-model:visible="showEditDialog"
    :space="selectedSpace"
    :venue-id="venue.id"
    @edited-successful="() => (showEditDialog = false)"
  />

  <Table
    data-key="id"
    :paginator="{ lazy: false, rows: 10 }"
    :data="{ items: venue.spaces, totalRecords: venue.spaces.length }"
    :buttons="{
      right: [
        {
          icon: 'pi-plus',
          tooltip: 'New Space',
          onClick: () => (showCreateDialog = true),
          disabled: venue.spaces.length > 0,
        },
      ],
    }"
    :context-menu="[
      { label: 'Edit', icon: 'pi pi-pencil', command: onEdit },
      { label: 'Delete', icon: 'pi pi-trash', command: onDelete },
    ]"
    @row-click="onEdit"
  >
    <Column field="name" header="Name" />
    <Column key="count" header="Products">
      <template #body="{ data }: ColumnScope">
        {{ data.placements.flatMap((p) => p.variants).length }}
      </template>
    </Column>
  </Table>
</template>

<style scoped />
