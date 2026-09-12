<script setup lang="ts">
import { IonIcon } from '@ionic/vue';
import { star } from 'ionicons/icons';
import type { Note } from './NoteCard.vue';

const props = defineProps<{ notes: Note[]; selectedId?: string | null }>();
const emit = defineEmits<{ (e: 'select', id: string): void }>();
</script>

<template>
  <div class="recent-strip">
    <button
      v-for="note in notes"
      :key="note.id"
      class="mini-card paper-surface"
      :class="{ selected: note.id === selectedId }"
      @click="emit('select', note.id)"
    >
      <span class="mini-tab" :class="note.category" />
      <ion-icon v-if="note.favorite" :icon="star" class="mini-favorite" />
      <div class="mini-title">{{ note.title }}</div>
    </button>
  </div>
</template>

<style scoped>
.recent-strip {
  display: flex;
  gap: 10px;
  overflow-x: auto;
  padding: 2px 2px 10px;
  scroll-snap-type: x proximity;
}
.mini-card {
  flex: 0 0 auto;
  width: 108px;
  min-height: 56px;
  padding: 12px 10px 10px;
  text-align: left;
  cursor: pointer;
  scroll-snap-align: start;
  position: relative;
  display: flex;
  align-items: center;
}
.mini-card:hover,
.mini-card:focus-visible {
  border-color: var(--color-brass);
  outline: none;
}
.mini-card.selected {
  border-color: var(--color-brass);
  box-shadow: inset 3px 0 0 var(--color-brass);
}
.mini-tab {
  position: absolute;
  top: 0;
  left: 12px;
  width: 18px;
  height: 5px;
}
.mini-tab.school { background: var(--color-tag-school); }
.mini-tab.personal { background: var(--color-tag-personal); }
.mini-favorite {
  position: absolute;
  top: 8px;
  right: 8px;
  font-size: 0.75rem;
  color: var(--color-favorite);
}
.mini-title {
  font-family: var(--font-display);
  font-size: 0.82rem;
  font-weight: 600;
  color: var(--color-ink);
  line-height: 1.25;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
</style>