<script setup lang="ts">
import { IonIcon } from '@ionic/vue';
import { documentTextOutline, starOutline, archiveOutline } from 'ionicons/icons';

type ViewKey = 'all' | 'favorites' | 'archive';

const props = defineProps<{ modelValue: ViewKey }>();
const emit = defineEmits<{ (e: 'update:modelValue', v: ViewKey): void }>();

const tabs: { key: ViewKey; label: string; icon: string; flag: string }[] = [
  { key: 'all', label: 'All', icon: documentTextOutline, flag: 'var(--color-brass)' },
  { key: 'favorites', label: 'Favorites', icon: starOutline, flag: 'var(--color-favorite)' },
  { key: 'archive', label: 'Archive', icon: archiveOutline, flag: 'var(--color-archive)' },
];
</script>

<template>
  <nav class="rail">
    <div class="rail-title">NOTES</div>
    <button
      v-for="tab in tabs"
      :key="tab.key"
      class="rail-tab"
      :class="{ active: modelValue === tab.key }"
      @click="emit('update:modelValue', tab.key)"
    >
      <span class="flag" :style="{ background: tab.flag }" />
      <ion-icon :icon="tab.icon" />
      <span class="rail-label">{{ tab.label }}</span>
    </button>
  </nav>
</template>

<style scoped>
.rail {
  width: var(--rail-width);
  background: linear-gradient(180deg, #294048 0%, var(--color-rail) 40%, #1B2930 100%);
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 16px;
  flex-shrink: 0;
  position: relative;
  box-shadow: inset -1px 0 0 rgba(0, 0, 0, 0.25);
}
/* brass rivets, top and bottom, like a real binder spine */
.rail::before,
.rail::after {
  content: '';
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background: var(--color-brass);
  opacity: 0.7;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.4);
}
.rail::before { top: 8px; }
.rail::after { bottom: 8px; }
.rail-title {
  color: #F7F5EE;
  font-family: var(--font-display);
  font-weight: 700;
  writing-mode: vertical-rl;
  letter-spacing: 0.15em;
  font-size: 0.85rem;
  margin-bottom: 20px;
  opacity: 0.85;
}
.rail-tab {
  position: relative;
  width: 100%;
  background: transparent;
  border: none;
  color: #C9C4B4;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
  padding: 10px 4px;
  cursor: pointer;
}
.rail-tab.active {
  background: var(--color-rail-active);
  color: #FFFFFF;
}
.rail-tab:focus-visible {
  outline: 2px solid var(--color-brass);
  outline-offset: -2px;
}
.flag {
  position: absolute;
  left: 0;
  top: 8px;
  bottom: 8px;
  width: 3px;
  opacity: 0;
}
.rail-tab.active .flag {
  opacity: 1;
}
.rail-tab ion-icon {
  font-size: 1.25rem;
}
.rail-label {
  font-family: var(--font-mono);
  font-size: 0.6rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}
</style>