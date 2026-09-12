<!-- src/components/NotePreview.vue -->
<script setup lang="ts">
import { IonIcon } from '@ionic/vue';
import { archiveOutline, trashOutline, createOutline, star, starOutline, fileTrayStackedOutline, closeOutline } from 'ionicons/icons';
import type { Note } from './NoteCard.vue';

const props = defineProps<{ note: Note | null; showClose?: boolean }>();
const emit = defineEmits<{
  (e: 'toggle-favorite', id: string): void;
  (e: 'archive', id: string): void;
  (e: 'edit', id: string): void;
  (e: 'delete', id: string): void;
  (e: 'close'): void;
}>();

// Formats ISO string into detailed date + 12-hour time (e.g., "September 12, 2026 at 2:30 PM")
const formattedDate = (iso: string) => {
  if (!iso) return '';
  const date = new Date(iso);
  const dateStr = date.toLocaleDateString(undefined, {
    month: 'long',
    day: 'numeric',
    year: 'numeric',
  });
  const timeStr = date.toLocaleTimeString(undefined, {
    hour: 'numeric',
    minute: '2-digit',
    hour12: true,
  });
  return `${dateStr} at ${timeStr}`;
};
</script>

<template>
  <div class="preview-pane">
    <button v-if="showClose" class="close-btn" aria-label="Close preview" @click="emit('close')">
      <ion-icon :icon="closeOutline" />
    </button>
    
    <template v-if="note">
      <div class="preview-scroll-body">
        <div class="preview-head">
          <span class="stamp" :class="note.category">{{ note.category }}</span>
          <h2 class="preview-title">{{ note.title }}</h2>
          <span class="meta-text">Updated {{ formattedDate(note.updatedAt) }}</span>
        </div>
        <div class="rule" />
        <p class="preview-content">{{ note.content || 'This note has no content yet.' }}</p>
      </div>

      <div class="preview-toolbar">
        <button
          class="favorite-btn"
          :class="{ active: note.favorite }"
          :aria-pressed="note.favorite"
          :aria-label="note.favorite ? 'Remove from favorites' : 'Add to favorites'"
          @click="emit('toggle-favorite', note.id)"
        >
          <ion-icon :icon="note.favorite ? star : starOutline" />
          {{ note.favorite ? 'Favorited' : 'Favorite' }}
        </button>
        <button aria-label="Edit note" @click="emit('edit', note.id)">
          <ion-icon :icon="createOutline" /> Edit
        </button>
        <button aria-label="Archive note" @click="emit('archive', note.id)">
          <ion-icon :icon="archiveOutline" /> {{ note.archived ? 'Unarchive' : 'Archive' }}
        </button>
        <button class="danger" aria-label="Delete note" @click="emit('delete', note.id)">
          <ion-icon :icon="trashOutline" />
        </button>
      </div>
    </template>

    <div v-else class="preview-empty">
      <ion-icon :icon="fileTrayStackedOutline" />
      <p>Select a note to read it here.</p>
    </div>
  </div>
</template>

<style scoped>
.preview-pane {
  height: 100%;
  display: flex;
  flex-direction: column;
  position: relative;
  background: var(--color-card);
  border-left: 1px solid var(--color-card-border);
  overflow: hidden;
}

.close-btn {
  position: absolute;
  top: 14px;
  right: 14px;
  background: none;
  border: none;
  color: var(--color-ink-soft);
  font-size: 1.2rem;
  cursor: pointer;
  z-index: 2;
}

.preview-scroll-body {
  flex: 1;
  overflow-y: auto;
  padding: 22px 24px 16px;
  -webkit-overflow-scrolling: touch;
}

.preview-head {
  position: relative;
  padding-top: 4px;
}

.stamp {
  font-family: var(--font-mono);
  font-size: 0.65rem;
  text-transform: uppercase;
  letter-spacing: 0.06em;
  padding: 2px 10px;
  border: 1px dashed currentColor;
  display: inline-block;
  transform: rotate(-2deg);
}

.stamp.school { color: var(--color-tag-school); }
.stamp.personal { color: var(--color-tag-personal); }

.preview-title {
  font-size: 1.5rem;
  margin: 12px 0 6px;
  line-height: 1.25;
}

.rule {
  height: 1px;
  background: var(--color-card-border);
  margin: 16px 0 18px;
}

.preview-content {
  font-family: var(--font-body);
  font-size: 0.95rem;
  line-height: 1.7;
  color: var(--color-ink);
  white-space: pre-wrap;
  word-break: break-word;
}

.preview-toolbar {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  padding: 14px 24px calc(14px + env(safe-area-inset-bottom));
  background: var(--color-card);
  border-top: 1px solid var(--color-card-border);
  flex-shrink: 0;
}

.preview-toolbar button {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  font-family: var(--font-body);
  font-size: 0.82rem;
  font-weight: 500;
  color: var(--color-ink-soft);
  background: var(--color-canvas);
  border: 1px solid var(--color-card-border);
  border-radius: 4px;
  padding: 7px 12px;
  cursor: pointer;
}

.preview-toolbar button:hover { border-color: var(--color-brass); color: var(--color-brass-dark); }
.preview-toolbar button.danger:hover { border-color: var(--color-favorite); color: var(--color-favorite); }

.favorite-btn.active {
  color: var(--color-favorite);
  border-color: var(--color-favorite);
}

.preview-empty {
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 8px;
  color: var(--color-ink-soft);
  text-align: center;
}

.preview-empty ion-icon {
  font-size: 2.2rem;
  color: var(--color-card-border);
}

.preview-empty p {
  font-family: var(--font-body);
  font-size: 0.9rem;
  margin: 0;
}
</style>