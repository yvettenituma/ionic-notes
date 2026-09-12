<!-- src/components/ConfirmDialog.vue -->
<script setup lang="ts">
import { IonAlert } from '@ionic/vue';

const isOpen = defineModel<boolean>({ default: false });
const props = defineProps<{
  header?: string;
  message?: string;
}>();

const emit = defineEmits<{ (e: 'confirm'): void; (e: 'cancel'): void }>();

const buttons = [
  {
    text: 'Cancel',
    role: 'cancel',
    handler: () => emit('cancel'),
  },
  {
    text: 'Delete',
    role: 'destructive',
    handler: () => emit('confirm'),
  },
];
</script>

<template>
  <ion-alert
    :is-open="isOpen"
    css-class="ledger-alert"
    :header="header ?? 'Delete this note?'"
    :message="message ?? 'This action cannot be undone.'"
    :buttons="buttons"
    @didDismiss="isOpen = false"
  />
</template>

<!-- Unscoped styles required to penetrate Ionic shadow DOM components -->
<style>
.ledger-alert .alert-wrapper {
  background: var(--color-card) !important;
  border: 1.5px solid var(--color-card-border);
  border-radius: var(--radius-card);
  box-shadow: 0 4px 16px rgba(38, 36, 32, 0.2);
}

.ledger-alert .alert-head h2 {
  font-family: var(--font-display) !important;
  font-weight: 700 !important;
  color: var(--color-ink) !important;
  font-size: 1.15rem !important;
}

.ledger-alert .alert-message {
  font-family: var(--font-body) !important;
  color: var(--color-ink-soft) !important;
  font-size: 0.9rem !important;
}

.ledger-alert .alert-button-group {
  border-top: 1px dashed var(--color-card-border) !important;
  padding: 4px !important;
}

.ledger-alert button.alert-button {
  font-family: var(--font-display) !important;
  font-weight: 600 !important;
  font-size: 0.85rem !important;
  color: var(--color-ink-soft) !important;
  border-radius: var(--radius-card) !important;
}

.ledger-alert button.alert-button:focus-visible {
  outline: 2px solid var(--color-brass) !important;
}

/* Primary destructive action styled with theme favorite/danger red */
.ledger-alert button.alert-button.alert-button-role-destructive {
  color: var(--color-favorite) !important;
  font-weight: 700 !important;
}
</style>