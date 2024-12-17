<script setup lang="ts" generic="T extends Record<string, unknown>">
import FloatLabel from "primevue/floatlabel";
import InputGroup from "primevue/inputgroup";
import InputGroupAddon from "primevue/inputgroupaddon";
import { computed } from "vue";

interface Options {
  items: T[];
  label: Extract<keyof T, string>;
  value: Extract<keyof T, string>;
}

interface ButtonProp {
  label: string;
  icon: string;
  onClick: () => void;
}

interface Props {
  id: string;
  type?: "single" | "multi";
  label: string;
  placeholder?: string;
  options: Options;
  info?: string;
  required?: boolean;
  disabled?: boolean;
  autoFocus?: boolean;
  buttons?: ButtonProp[];
  maxSelectedLabels?: number;
}

const value = defineModel<string | undefined>("value", { default: undefined });
const values = defineModel<string[]>("values", { default: [] });
const props = withDefaults(defineProps<Props>(), {
  type: "single",
  placeholder: undefined,
  info: undefined,
  required: false,
  disabled: false,
  autoFocus: false,
  buttons: undefined,
  maxSelectedLabels: 3,
});

const emit = defineEmits<{ hide: [] }>();

const tooltipText = computed(() => {
  const lines: string[] = [];
  if (props.required) {
    lines.push("Required");
  }
  if (props.info) {
    lines.push(`${props.info}`);
  }

  if (lines.length > 0) {
    if (lines.length > 1) {
      for (let i = 0; i < lines.length; i++) {
        lines[i] = `* ${lines[i]}`;
      }
    }

    return lines.join("\n");
  }

  return undefined;
});
</script>

<template>
  <div :id="id" class="select-input">
    <InputGroup>
      <FloatLabel variant="in">
        <Select
          v-if="type === 'single'"
          :id="`input-${props.id}`"
          v-model="value"
          :options="props.options.items"
          :option-label="props.options.label"
          :option-value="props.options.value"
          :placeholder="props.placeholder"
          filter
          :autofocus="props.autoFocus"
          :disabled="props.disabled"
          @hide="emit('hide')"
        />
        <MultiSelect
          v-else-if="type === 'multi'"
          :id="`input-${props.id}`"
          v-model="values"
          :options="props.options.items"
          :option-label="props.options.label"
          :option-value="props.options.value"
          :placeholder="props.placeholder"
          filter
          :max-selected-labels="props.maxSelectedLabels"
          :show-toggle-all="false"
          :autofocus="props.autoFocus"
          :disabled="props.disabled"
          @hide="emit('hide')"
        />
        <label :for="`input-${props.id}`">{{ props.label }}</label>
      </FloatLabel>
      <InputGroupAddon v-if="tooltipText">
        <div v-tooltip.bottom="tooltipText" class="pi pi-info-circle" />
      </InputGroupAddon>
      <InputGroupAddon v-if="props.buttons">
        <Button
          v-for="button in props.buttons"
          :key="button.label"
          v-tooltip="button.label"
          class="button"
          :icon="'pi ' + button.icon"
          size="small"
          text
          @click="button.onClick"
        />
      </InputGroupAddon>
    </InputGroup>
  </div>
</template>

<style scoped>
.select-input {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 0.5rem;
}

.button {
  padding: 0;
}
</style>
