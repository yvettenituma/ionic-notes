<script setup lang="ts">
import { IonModal, IonHeader, IonToolbar, IonTitle, IonButtons, IonButton, IonContent } from '@ionic/vue';
import { ref, watch } from 'vue';
import type { Note } from './NoteCard.vue';

const isOpen = defineModel<boolean>({ default: false });
const props = defineProps<{ note?: Note | null }>();
const emit = defineEmits<{
  (e: 'save', payload: { id?: string; title: string; content: string; category: 'school' | 'personal' }): void;
}>();

const title = ref('');
const content = ref('');
const category = ref<'school' | 'personal'>('school');

// Reset the form each time the modal opens, seeded from the note being edited (if any)
watch(isOpen, (open) => {
  if (!open) return;
  title.value = props.note?.title ?? '';
  content.value = props.note?.content ?? '';
  category.value = props.note?.category ?? 'school';
});

const close = () => {
  isOpen.value = false;
};

const handleSave = () => {
  if (!title.value.trim()) return;
  emit('save', {
    id: props.note?.id,
    title: title.value.trim(),
    content: content.value.trim(),
    category: category.value,
  });
  close();
};
</script>

<template>
  <ion-modal :is-open="isOpen" @didDismiss="close">
    <ion-header class="editor-header">
      <ion-toolbar>
        <ion-title>{{ note ? 'Edit note' : 'New note' }}</ion-title>
        <ion-buttons slot="start">
          <ion-button @click="close">Cancel</ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content class="editor-content">
      <input
        v-model="title"
        class="title-input"
        placeholder="Title"
        maxlength="80"
        autofocus
      />

      <div class="category-picker">
        <button
          type="button"
          class="category-btn school"
          :class="{ active: category === 'school' }"
          @click="category = 'school'"
        >
          School
        </button>
        <button
          type="button"
          class="category-btn personal"
          :class="{ active: category === 'personal' }"
          @click="category = 'personal'"
        >
          Personal
        </button>
      </div>

      <textarea
        v-model="content"
        class="content-input"
        placeholder="Write your note…"
        rows="10"
      />
    </ion-content>

    <div class="editor-footer">
      <button class="save-btn" :disabled="!title.trim()" @click="handleSave">
        Save note
      </button>
    </div>
  </ion-modal>
</template>

<style scoped>
.editor-header {
  --background: var(--color-card);
}
:deep(ion-toolbar) {
  --background: var(--color-card);
  --color: var(--color-ink);
  --border-color: var(--color-card-border);
}
:deep(ion-title) {
  font-family: var(--font-display);
  font-weight: 700;
}
:deep(ion-button) {
  --color: var(--color-ink-soft);
  font-family: var(--font-body);
  text-transform: none;
}
.editor-content {
  --background: var(--color-canvas);
  --padding-start: 18px;
  --padding-end: 18px;
  --padding-top: 16px;
}
.title-input {
  width: 100%;
  border: none;
  border-bottom: 1px solid var(--color-card-border);
  background: transparent;
  font-family: var(--font-display);
  font-size: 1.3rem;
  font-weight: 600;
  color: var(--color-ink);
  padding: 6px 0 10px;
  outline: none;
}
.title-input::placeholder {
  color: var(--color-ink-soft);
}
.category-picker {
  display: flex;
  gap: 8px;
  margin: 14px 0;
}
.category-btn {
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
.category-btn.school.active {
  border-color: var(--color-tag-school);
  border-style: solid;
  color: var(--color-tag-school);
  background: color-mix(in srgb, var(--color-tag-school) 12%, var(--color-card));
}
.category-btn.personal.active {
  border-color: var(--color-tag-personal);
  border-style: solid;
  color: var(--color-tag-personal);
  background: color-mix(in srgb, var(--color-tag-personal) 12%, var(--color-card));
}
.content-input {
  width: 100%;
  border: 1px solid var(--color-card-border);
  border-radius: var(--radius-card);
  background: var(--color-card);
  font-family: var(--font-body);
  font-size: 0.95rem;
  color: var(--color-ink);
  padding: 12px;
  resize: vertical;
  outline: none;
}
.content-input:focus {
  border-color: var(--color-brass);
}
.content-input::placeholder {
  color: var(--color-ink-soft);
}
.editor-footer {
  padding: 12px 18px calc(12px + env(safe-area-inset-bottom));
  background: var(--color-canvas);
  border-top: 1px solid var(--color-card-border);
}
.save-btn {
  width: 100%;
  padding: 12px;
  border: none;
  border-radius: var(--radius-card);
  background: var(--color-brass);
  color: #fff;
  font-family: var(--font-display);
  font-weight: 600;
  font-size: 0.95rem;
  cursor: pointer;
}
.save-btn:disabled {
  background: var(--color-card-border);
  color: var(--color-ink-soft);
  cursor: not-allowed;
}
</style>