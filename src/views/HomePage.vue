<!-- src/views/HomePage.vue -->
<script setup lang="ts">
import { IonPage, IonContent, IonIcon, IonFab, IonFabButton, IonModal } from '@ionic/vue';
import { addOutline } from 'ionicons/icons';
import { computed, onMounted, onUnmounted, ref } from 'vue';
import Sidebar from '@/components/Sidebar.vue';
import NoteStats from '@/components/NoteStats.vue';
import SearchBar from '@/components/SearchBar.vue';
import FilterBar from '@/components/FilterBar.vue';
import RecentNotes from '@/components/RecentNotes.vue';
import NoteList from '@/components/NoteList.vue';
import NoteEditor from '@/components/NoteEditor.vue';
import NotePreview from '@/components/NotePreview.vue';
import ConfirmDialog from '@/components/ConfirmDialog.vue';
import type { Note } from '@/components/NoteCard.vue';

// --- Reactive State & Data ---
const notes = ref<Note[]>([]);
const view = ref<'all' | 'favorites' | 'archive'>('all');
const category = ref<'all' | 'school' | 'personal'>('all');
const query = ref('');

const visibleNotes = computed(() =>
  notes.value
    .filter((n) => (view.value === 'archive' ? n.archived : !n.archived))
    .filter((n) => (view.value === 'favorites' ? n.favorite : true))
    .filter((n) => (category.value === 'all' ? true : n.category === category.value))
    .filter((n) => n.title.toLowerCase().includes(query.value.toLowerCase()))
);

const recent = computed(() =>
  [...notes.value]
    .filter((n) => !n.archived)
    .sort((a, b) => +new Date(b.updatedAt) - +new Date(a.updatedAt))
    .slice(0, 6)
);

// --- Responsive Preview Pane ---
const isWideScreen = ref(false);
let mql: MediaQueryList | undefined;

const handleMqlChange = (e: MediaQueryListEvent) => (isWideScreen.value = e.matches);

onMounted(() => {
  mql = window.matchMedia('(min-width: 880px)');
  isWideScreen.value = mql.matches;
  mql.addEventListener('change', handleMqlChange);
});

onUnmounted(() => mql?.removeEventListener('change', handleMqlChange));

// --- Note Selection ---
const selectedNoteId = ref<string | null>(null);
const selectedNote = computed(() => notes.value.find((n) => n.id === selectedNoteId.value) ?? null);
const isPreviewModalOpen = ref(false);

const selectNote = (id: string) => {
  selectedNoteId.value = id;
  if (!isWideScreen.value) isPreviewModalOpen.value = true;
};

const closePreviewModal = () => {
  isPreviewModalOpen.value = false;
};

// --- Actions ---
const toggleFavorite = (id: string) => {
  const n = notes.value.find((n) => n.id === id);
  if (n) n.favorite = !n.favorite;
};

const archiveNote = (id: string) => {
  const n = notes.value.find((n) => n.id === id);
  if (n) n.archived = !n.archived;
};

// --- Editor Modal ---
const isEditorOpen = ref(false);
const editingNote = ref<Note | null>(null);

const openNewNote = () => {
  editingNote.value = null;
  isEditorOpen.value = true;
};

const openEditNote = (id: string) => {
  editingNote.value = notes.value.find((n) => n.id === id) ?? null;
  isEditorOpen.value = true;
};

const handleSaveNote = (payload: { id?: string; title: string; content: string; category: 'school' | 'personal' }) => {
  const now = new Date().toISOString();

  if (payload.id) {
    const n = notes.value.find((n) => n.id === payload.id);
    if (n) {
      n.title = payload.title;
      n.content = payload.content;
      n.category = payload.category;
      n.updatedAt = now;
    }
  } else {
    const created: Note = {
      id: crypto.randomUUID(),
      title: payload.title,
      content: payload.content,
      category: payload.category,
      favorite: false,
      archived: false,
      updatedAt: now,
    };
    notes.value.unshift(created);
    selectedNoteId.value = created.id;
  }
};

// --- Confirm Delete Modal ---
const isConfirmOpen = ref(false);
const pendingDeleteId = ref<string | null>(null);

const requestDelete = (id: string) => {
  pendingDeleteId.value = id;
  isConfirmOpen.value = true;
};

const confirmDelete = () => {
  if (pendingDeleteId.value) {
    notes.value = notes.value.filter((n) => n.id !== pendingDeleteId.value);
    if (selectedNoteId.value === pendingDeleteId.value) {
      selectedNoteId.value = null;
      isPreviewModalOpen.value = false;
    }
  }
  pendingDeleteId.value = null;
};

const cancelDelete = () => {
  pendingDeleteId.value = null;
};
</script>

<template>
  <ion-page>
    <div class="app-shell">
      <Sidebar v-model="view" />
      <div class="content-row">
        <ion-content class="list-pane">
          <div class="paper-texture" aria-hidden="true" />
          <div class="inner">
            <h1 class="page-title">Notes</h1>
            <NoteStats
              :total="notes.length"
              :favorites="notes.filter((n) => n.favorite).length"
            />
            <SearchBar v-model="query" />
            <FilterBar v-model="category" />

            <!-- Recent section: ONLY displayed on the 'all' view -->
            <template v-if="view === 'all' && recent.length">
              <div class="ledger-header">
                <span class="marker" />
                <h2 class="section-title">Recent Activity</h2>
              </div>
              <RecentNotes :notes="recent" :selected-id="selectedNoteId" @select="selectNote" />
            </template>

            <!-- Section Division Header for Active Tab -->
            <div class="ledger-header">
              <span class="marker" />
              <h2 class="section-title">{{ view }} Notes</h2>
            </div>
            
            <NoteList
              :notes="visibleNotes"
              :selected-id="selectedNoteId"
              :empty-message="
                view === 'archive' 
                  ? 'Nothing archived yet.' 
                  : view === 'favorites' 
                  ? 'No favorites yet — tap the star to add one.' 
                  : 'Nothing here yet. Tap + to write your first note.'
              "
              @select="selectNote"
              @toggle-favorite="toggleFavorite"
              @archive="archiveNote"
              @delete="requestDelete"
              @edit="openEditNote"
            />
          </div>

          <ion-fab vertical="bottom" horizontal="end" slot="fixed">
            <ion-fab-button aria-label="New note" @click="openNewNote">
              <ion-icon :icon="addOutline" />
            </ion-fab-button>
          </ion-fab>
        </ion-content>

        <!-- Wide-screen inline preview pane -->
        <div v-if="isWideScreen" class="preview-column">
          <NotePreview
            :note="selectedNote"
            @toggle-favorite="toggleFavorite"
            @archive="archiveNote"
            @edit="openEditNote"
            @delete="requestDelete"
          />
        </div>
      </div>

      <!-- Mobile/narrow screen preview modal -->
      <ion-modal v-if="!isWideScreen" :is-open="isPreviewModalOpen" @didDismiss="closePreviewModal">
        <NotePreview
          :note="selectedNote"
          show-close
          @toggle-favorite="toggleFavorite"
          @archive="archiveNote"
          @edit="(id) => { closePreviewModal(); openEditNote(id); }"
          @delete="(id) => { closePreviewModal(); requestDelete(id); }"
          @close="closePreviewModal"
        />
      </ion-modal>

      <NoteEditor
        v-model="isEditorOpen"
        :note="editingNote"
        @save="handleSaveNote"
      />

      <ConfirmDialog
        v-model="isConfirmOpen"
        header="Delete this note?"
        message="This action cannot be undone."
        @confirm="confirmDelete"
        @cancel="cancelDelete"
      />
    </div>
  </ion-page>
</template>

<style scoped>
.app-shell {
  display: flex;
  height: 100%;
}

.content-row {
  flex: 1;
  display: flex;
  min-width: 0;
}

.list-pane {
  --background: var(--color-canvas);
  position: relative;
  flex: 1;
  min-width: 0;
}

.preview-column {
  width: 380px;
  flex-shrink: 0;
  height: 100%;
}

.paper-texture {
  position: absolute;
  inset: 0;
  pointer-events: none;
  z-index: 0;
  background-image:
    linear-gradient(to right, transparent 55px, rgba(156, 59, 51, 0.18) 55px, rgba(156, 59, 51, 0.18) 56px, transparent 56px),
    repeating-linear-gradient(
      to bottom,
      transparent,
      transparent 27px,
      rgba(38, 36, 32, 0.05) 27px,
      rgba(38, 36, 32, 0.05) 28px
    );
}

.inner {
  position: relative;
  z-index: 1;
  padding: 14px 18px 90px 26px;
  max-width: 640px;
}

.page-title {
  font-size: 1.6rem;
  color: var(--color-ink);
  margin: 0 0 8px;
}

.ledger-header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin: 18px 0 10px;
  padding-bottom: 4px;
  border-bottom: 1.5px dashed var(--color-card-border);
}

.marker {
  width: 8px;
  height: 8px;
  background: var(--color-brass);
  border-radius: 1px;
}

.section-title {
  font-family: var(--font-display);
  font-size: 0.85rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  color: var(--color-ink-soft);
  margin: 0;
}

ion-fab-button {
  --background: var(--color-brass);
  --background-activated: var(--color-brass-dark);
}
</style>