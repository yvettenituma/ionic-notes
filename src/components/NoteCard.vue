<!-- src/components/NoteCard.vue -->
<script setup lang="ts">
import { IonIcon } from '@ionic/vue';
import { archiveOutline, trashOutline, createOutline, star, starOutline } from 'ionicons/icons';

export interface Note {
  id: string;
  title: string;
  content: string;
  category: 'school' | 'personal';
  favorite: boolean;
  archived: boolean;
  updatedAt: string; // ISO date string
}

const props = defineProps<{ note: Note; selected?: boolean }>();

const emit = defineEmits<{
  (e: 'select', id: string): void;
  (e: 'toggle-favorite', id: string): void;
  (e: 'archive', id: string): void;
  (e: 'edit', id: string): void;
  (e: 'delete', id: string): void;
}>();

// Formats ISO string into short date + 12-hour time (e.g., "Sep 12, 2:30 PM")
const formattedDate = (iso: string) => {
  if (!iso) return '';
  const date = new Date(iso);
  return date.toLocaleDateString(undefined, {
    month: 'short',
    day: 'numeric',
    hour: 'numeric',
    minute: '2-digit',
    hour12: true,
  });
};
</script>

<template>
  <article
    class="note-card paper-surface"
    :class="{ favorited: note.favorite, selected: props.selected }"
    role="button"
    tabindex="0"
    @click="emit('select', note.id)"
    @keydown.enter="emit('select', note.id)"
  >
    <button
      class="dog-ear"
      :class="{ folded: note.favorite }"
      :aria-pressed="note.favorite"
      aria-label="Toggle favorite"
      @click.stop="emit('toggle-favorite', note.id)"
    />
    <span class="stamp" :class="note.category">{{ note.category }}</span>
    <h3 class="note-title">{{ note.title }}</h3>
    <p class="note-content">{{ note.content }}</p>
    <div class="note-footer">
      <span class="meta-text">{{ formattedDate(note.updatedAt) }}</span>
      <div class="note-actions">
        <button
          class="favorite-btn"
          :class="{ active: note.favorite }"
          :aria-pressed="note.favorite"
          :aria-label="note.favorite ? 'Remove from favorites' : 'Add to favorites'"
          @click.stop="emit('toggle-favorite', note.id)"
        >
          <ion-icon :icon="note.favorite ? star : starOutline" />
        </button>
        <button aria-label="Edit note" @click.stop="emit('edit', note.id)"><ion-icon :icon="createOutline" /></button>
        <button aria-label="Archive note" @click.stop="emit('archive', note.id)"><ion-icon :icon="archiveOutline" /></button>
        <button class="danger" aria-label="Delete note" @click.stop="emit('delete', note.id)"><ion-icon :icon="trashOutline" /></button>
      </div>
    </div>
  </article>
</template>

<style scoped>
.note-card {
  position: relative;
  padding: 16px 16px 12px;
  overflow: hidden;
  cursor: pointer;
  transition: box-shadow 0.15s ease, transform 0.15s ease, border-color 0.15s ease;
}

.note-card:hover {
  box-shadow: 0 2px 8px rgba(38, 36, 32, 0.1);
  transform: translateY(-1px);
}

.note-card:active {
  box-shadow: 0 3px 10px rgba(38, 36, 32, 0.15);
  transform: translateY(0);
}

.note-card:focus-visible {
  outline: 2px solid var(--color-brass);
  outline-offset: 2px;
}

.note-card.selected {
  border-color: var(--color-brass);
  box-shadow: inset 3px 0 0 var(--color-brass);
}

.dog-ear {
  position: absolute;
  top: 0;
  left: 0;
  width: 26px;
  height: 26px;
  border: none;
  padding: 0;
  cursor: pointer;
  background: transparent;
  clip-path: polygon(0 0, 100% 0, 0 100%);
  z-index: 1;
}

.dog-ear::before {
  content: '';
  position: absolute;
  inset: 0;
  background: var(--color-card-border);
  clip-path: polygon(0 0, 100% 0, 0 100%);
  transition: transform 0.18s ease, background 0.18s ease;
  transform-origin: top left;
}

.dog-ear.folded::before {
  background: var(--color-favorite);
  transform: scale(1.15);
}

.stamp {
  position: absolute;
  top: 10px;
  right: -6px;
  font-family: var(--font-mono);
  font-size: 0.62rem;
  text-transform: uppercase;
  letter-spacing: 0.06em;
  padding: 2px 10px;
  border: 1px dashed currentColor;
  transform: rotate(3deg);
}

.stamp.school { color: var(--color-tag-school); }
.stamp.personal { color: var(--color-tag-personal); }

.note-title {
  font-size: 1rem;
  margin: 22px 0 6px;
  padding-bottom: 6px;
  border-bottom: 1px solid var(--color-card-border);
}

.note-content {
  font-size: 0.85rem;
  color: var(--color-ink-soft);
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
  margin-bottom: 10px;
}

.note-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.note-actions {
  display: flex;
  gap: 4px;
}

.note-actions button {
  background: none;
  border: none;
  color: var(--color-ink-soft);
  padding: 4px;
  cursor: pointer;
  font-size: 1rem;
  border-radius: 3px;
}

.note-actions button:hover { color: var(--color-brass-dark); }
.note-actions button.danger:hover { color: var(--color-favorite); }

.favorite-btn.active {
  color: var(--color-favorite);
}

.note-actions button:focus-visible,
.dog-ear:focus-visible {
  outline: 2px solid var(--color-brass);
  outline-offset: 2px;
}
</style>