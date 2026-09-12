<script setup lang="ts">
import { IonIcon } from '@ionic/vue';
import { searchOutline } from 'ionicons/icons';

const model = defineModel<string>({ default: '' });
</script>

<template>
  <label class="search-field">
    <ion-icon :icon="searchOutline" />
    <input
      v-model="model"
      type="text"
      placeholder="Search notes…"
    />
  </label>
</template>

<style scoped>
.search-field {
  display: flex;
  align-items: center;
  gap: 8px;
  background: var(--color-card);
  border: 1px solid var(--color-card-border);
  border-radius: var(--radius-card);
  padding: 10px 12px;
}
.search-field ion-icon {
  color: var(--color-ink-soft);
  font-size: 1rem;
}
.search-field input {
  border: none;
  outline: none;
  background: transparent;
  font-family: var(--font-body);
  font-size: 0.95rem;
  color: var(--color-ink);
  width: 100%;
}
.search-field input::placeholder {
  color: var(--color-ink-soft);
}
</style>