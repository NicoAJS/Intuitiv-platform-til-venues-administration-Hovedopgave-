<script setup lang="ts">
import FloatLabel from "primevue/floatlabel";
import InputGroup from "primevue/inputgroup";
import InputGroupAddon from "primevue/inputgroupaddon";
import { computed } from "vue";

const model = defineModel<number>();
const props = withDefaults(
  defineProps<{
    allowEmpty?: boolean;
    min?: number;
    max?: number;
    minFractionDigits?: number;
    maxFractionDigits?: number;
    useGrouping?: boolean;
    prefix?: string;
    suffix?: string;
    id: string;
    label?: string;
    info?: string;
    required?: boolean;
    disabled?: boolean;
    autoFocus?: boolean;
  }>(),
  {
    min: undefined,
    max: undefined,
    minFractionDigits: undefined,
    maxFractionDigits: undefined,
    prefix: undefined,
    suffix: undefined,
    label: undefined,
    info: undefined,
    required: false,
    disabled: false,
    autoFocus: false,
  },
);

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
  <div :id="props.id" class="number-input">
    <InputGroup>
      <slot name="prefixAddons" />
      <FloatLabel variant="in">
        <InputNumber
          :id="`input-${props.id}`"
          v-model="model"
          autocomplete="off"
          :autofocus="props.autoFocus"
          :disabled="props.disabled"
          :allow-empty="props.allowEmpty"
          :min="props.min"
          :max="props.max"
          :min-fraction-digits="props.minFractionDigits"
          :max-fraction-digits="props.maxFractionDigits"
          :use-grouping="props.useGrouping"
          :prefix="props.prefix"
          :suffix="props.suffix"
        />
        <label v-if="label != null" :for="`input-${props.id}`">{{ props.label }}</label>
      </FloatLabel>
      <slot name="suffixAddons" />
      <InputGroupAddon v-if="tooltipText">
        <div v-tooltip.bottom="tooltipText" class="pi pi-info-circle" />
      </InputGroupAddon>
    </InputGroup>
  </div>
</template>

<style scoped>
.number-input {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 0.5rem;
}
</style>
