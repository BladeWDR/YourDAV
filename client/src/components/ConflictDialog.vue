<script setup lang="ts">
defineProps<{
  kind: 'update' | 'delete'
  noun: 'event' | 'contact'
  // The server's current version, if it could still be found after the
  // reload triggered by the 412 (null if it was deleted elsewhere).
  serverVersion: { title: string; detail: string } | null
  attemptedSummary: string
}>()
const emit = defineEmits<{ discard: []; reapply: [] }>()
</script>

<template>
  <div class="overlay" @click.self="emit('discard')">
    <div class="dialog">
      <h2>This {{ noun }} changed elsewhere</h2>
      <p class="dialog__hint">
        Someone (maybe you, in another tab) changed this {{ noun }} before your
        {{ kind === 'delete' ? 'delete' : 'save' }} went through.
      </p>

      <div v-if="serverVersion" class="dialog__version">
        <span class="dialog__version-label">Current version on the server</span>
        <p class="dialog__version-title">{{ serverVersion.title }}</p>
        <p v-if="serverVersion.detail" class="dialog__version-when">{{ serverVersion.detail }}</p>
      </div>
      <p v-else class="dialog__hint">It looks like this {{ noun }} was deleted elsewhere.</p>

      <div class="dialog__version" v-if="kind === 'update'">
        <span class="dialog__version-label">Your attempted change</span>
        <p class="dialog__version-title">{{ attemptedSummary }}</p>
      </div>

      <div class="dialog__actions">
        <button type="button" class="btn btn-ghost" @click="emit('discard')">
          {{ serverVersion ? 'Discard my changes' : 'OK' }}
        </button>
        <button v-if="serverVersion" type="button" class="btn btn-primary" @click="emit('reapply')">
          {{ kind === 'delete' ? 'Delete anyway' : 'Reapply my changes' }}
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.overlay {
  position: fixed;
  inset: 0;
  background: rgba(16, 20, 26, 0.45);
  backdrop-filter: blur(2px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 50;
  animation: fade-in 0.12s ease;
}
.dialog {
  background: var(--color-surface);
  padding: 1.5rem;
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-lg);
  width: 360px;
  animation: pop-in 0.14s ease;
}
.dialog h2 {
  font-size: 1.05rem;
  margin: 0;
}
.dialog__hint {
  margin: 0.5rem 0 0.75rem;
  color: var(--color-text-muted);
  font-size: 0.85rem;
}
.dialog__version {
  margin: 0 0 0.75rem;
  padding: 0.5rem 0.65rem;
  border: 1px solid var(--color-border);
  border-radius: var(--radius-sm);
  background: var(--color-surface-hover);
}
.dialog__version-label {
  display: block;
  font-size: 0.7rem;
  text-transform: uppercase;
  letter-spacing: 0.04em;
  color: var(--color-text-faint);
  margin-bottom: 0.2rem;
}
.dialog__version-title {
  margin: 0;
  font-size: 0.9rem;
  color: var(--color-text);
}
.dialog__version-when {
  margin: 0.15rem 0 0;
  font-size: 0.8rem;
  color: var(--color-text-muted);
}
.dialog__actions {
  display: flex;
  justify-content: flex-end;
  gap: 0.5rem;
  margin-top: 0.5rem;
}
@keyframes fade-in {
  from {
    opacity: 0;
  }
}
@keyframes pop-in {
  from {
    opacity: 0;
    transform: scale(0.97) translateY(4px);
  }
}
</style>
