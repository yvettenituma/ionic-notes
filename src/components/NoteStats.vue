<script setup lang="ts">
import { IonIcon } from '@ionic/vue';
import { documentTextOutline, starOutline } from 'ionicons/icons';

defineProps<{ total: number; favorites: number }>();
</script>

<template>
  <div class="ledger-strip">
    <div class="stat">
      <ion-icon :icon="documentTextOutline" class="stat-icon" />
      <div class="stat-body">
        <div class="stat-number">{{ total }}</div>
        <div class="stat-label meta-text">TOTAL NOTES</div>
      </div>
    </div>
    <div class="divider" />
    <div class="stat">
      <ion-icon :icon="starOutline" class="stat-icon favorite" />
      <div class="stat-body">
        <div class="stat-number favorite">{{ favorites }}</div>
        <div class="stat-label meta-text">FAVORITES</div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.ledger-strip {
  display: flex;
  align-items: center;
  padding: 10px 10px;
  background: var(--color-card);
  border: 1px solid var(--color-card-border);
  border-left: 3px solid var(--color-brass);
  border-radius: var(--radius-card);
  margin-bottom: 10px;
}
.stat {
  flex: 1;
  display: flex;
  align-items: center;
  gap: 10px;
}
.stat-icon {
  font-size: 1.1rem;
  color: var(--color-brass);
  flex-shrink: 0;
}
.stat-icon.favorite {
  color: var(--color-favorite);
}
.stat-number {
  font-family: var(--font-display);
  font-size: 1.6rem;
  font-weight: 700;
  line-height: 1;
  color: var(--color-ink);
}
.stat-number.favorite {
  color: var(--color-favorite);
}
.stat-label {
  margin-top: 2px;
}
.divider {
  width: 1px;
  height: 30px;
  background: var(--color-card-border);
  margin: 0 14px;
}
</style>