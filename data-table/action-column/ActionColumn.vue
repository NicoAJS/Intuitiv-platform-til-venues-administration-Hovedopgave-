<script setup lang="ts">
import { DataTableColumnBodyScope } from "@/domain/model/primevue/DataTable.ts";
import { computed } from "vue";

// eslint-disable-next-line @typescript-eslint/no-explicit-any
type OnClick = (row: any, event: MouseEvent) => void;

export interface ColumnAction {
  icon: string;
  severity?: "secondary" | "success" | "info" | "warn" | "help" | "danger" | "contrast";
  disabled?: boolean;
  isLoading?: boolean;
  onClick: OnClick;
}

type ColumnScope = DataTableColumnBodyScope<ColumnAction[]>;

const props = defineProps<{ actions: ColumnAction[] }>();

const columnWidth = computed<number>(() => props.actions.length * 2);
</script>

<template>
  <Column header="Actions" :style="{ width: `${columnWidth}rem` }">
    <template #body="{ data }: ColumnScope">
      <div class="actions-column">
        <Button
          v-for="action in actions"
          :key="action.icon"
          :icon="`pi ${action.icon}`"
          :severity="action.severity"
          :disabled="action.disabled"
          :loading="action.isLoading"
          outlined
          @click="action.onClick(data, $event)"
        />
      </div>
    </template>
  </Column>
</template>

<style scoped>
.actions-column {
  display: flex;
  flex-direction: row;
  gap: 0.5rem;

  & button {
    padding: 0;
    font-size: 1rem;
    line-height: 1.5rem;
    flex-basis: 1.5rem;
  }
}
</style>
