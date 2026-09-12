<script setup lang="ts">
type Category = 'all' | 'school' | 'personal';

const props = defineProps<{ modelValue: Category }>();
const emit = defineEmits<{ (e: 'update:modelValue', v: Category): void }>();

const options: { key: Category; label: string; color?: string }[] = [
  { key: 'all', label: 'All' },
  { key: 'school', label: 'School', color: 'var(--color-tag-school)' },
  { key: 'personal', label: 'Personal', color: 'var(--color-tag-personal)' },
];
</script>

<template>
  <div class="filter-bar">
    <button
      v-for="opt in options"
      :key="opt.key"
      class="stamp-tab"
      :class="{ active: modelValue === opt.key }"
      :style="opt.color ? { '--tab-color': opt.color } : {}"
      @click="emit('update:modelValue', opt.key)"
    >
      <span v-if="opt.color" class="dot" />
      {{ opt.label }}
    </button>
  </div>
</template>

<style scoped>
.filter-bar {
  display: flex;
  gap: 6px;
  margin: 8px 0 12px;
}
.stamp-tab {
  --tab-color: var(--color-brass);
  display: inline-flex;
  align-items: center;
  gap: 6px;
  font-family: var(--font-display);
  font-size: 0.8rem;
  font-weight: 600;
  padding: 6px 14px;
  background: var(--color-card);
  border: 1.5px dashed var(--color-card-border);
  border-radius: 2px;
  color: var(--color-ink-soft);
  cursor: pointer;
}
.dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: var(--tab-color);
}
.stamp-tab.active {
  background: color-mix(in srgb, var(--tab-color) 12%, var(--color-card));
  border-color: var(--tab-color);
  color: var(--tab-color);
  border-style: solid;
}
.stamp-tab:focus-visible {
  outline: 2px solid var(--color-brass);
  outline-offset: 1px;
}
</style>