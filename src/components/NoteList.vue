<script setup lang="ts">
import { IonIcon } from '@ionic/vue';
import { fileTrayOutline } from 'ionicons/icons';
import NoteCard, { type Note } from './NoteCard.vue';

const props = defineProps<{ notes: Note[]; emptyMessage?: string; selectedId?: string | null }>();
const emit = defineEmits<{
  (e: 'select', id: string): void;
  (e: 'toggle-favorite', id: string): void;
  (e: 'archive', id: string): void;
  (e: 'edit', id: string): void;
  (e: 'delete', id: string): void;
}>();
</script>

<template>
  <div v-if="notes.length" class="notes-grid">
    <NoteCard
      v-for="note in notes"
      :key="note.id"
      :note="note"
      :selected="note.id === selectedId"
      @select="(id) => emit('select', id)"
      @toggle-favorite="(id) => emit('toggle-favorite', id)"
      @archive="(id) => emit('archive', id)"
      @edit="(id) => emit('edit', id)"
      @delete="(id) => emit('delete', id)"
    />
  </div>

  <div v-else class="empty-shelf">
    <div class="empty-tab" />
    <ion-icon :icon="fileTrayOutline" class="empty-icon" />
    <p class="empty-text">{{ emptyMessage ?? 'Nothing filed here yet.' }}</p>
  </div>
</template>

<style scoped>
.notes-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 10px;
}

/* Empty state reads as an empty binder pocket, not a generic placeholder */
.empty-shelf {
  border: 1.5px dashed var(--color-card-border);
  border-radius: var(--radius-card);
  padding: 26px 20px;
  text-align: center;
  position: relative;
  margin-top: 6px;
}
.empty-icon {
  font-size: 1.6rem;
  color: var(--color-card-border);
  margin-bottom: 6px;
}
.empty-tab {
  position: absolute;
  top: -1.5px;
  left: 24px;
  width: 32px;
  height: 10px;
  background: var(--color-canvas);
  border-left: 1.5px dashed var(--color-card-border);
  border-right: 1.5px dashed var(--color-card-border);
}
.empty-text {
  font-family: var(--font-body);
  color: var(--color-ink-soft);
  font-size: 0.85rem;
  margin: 0;
}

@media (max-width: 340px) {
  .notes-grid {
    grid-template-columns: 1fr;
  }
}
</style>