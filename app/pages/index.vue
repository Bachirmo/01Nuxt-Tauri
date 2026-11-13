<script setup lang="ts">
import { invoke } from '@tauri-apps/api/core'

const storageKey = 'nuxt-tauri-learning-note'
const note = ref('')
const savedMessage = ref('Your note is saved locally in this browser.')
const nativeMessage = ref('The native bridge is ready when this page runs in Tauri.')

const characterCount = computed(() => note.value.length)
const wordCount = computed(() => {
  const trimmedNote = note.value.trim()
  return trimmedNote ? trimmedNote.split(/\s+/).length : 0
})

onMounted(() => {
  note.value = localStorage.getItem(storageKey) ?? ''
})

watch(note, (value) => {
  if (!import.meta.client) return

  localStorage.setItem(storageKey, value)
  savedMessage.value = value ? 'Saved automatically.' : 'Start typing to create a note.'
})

function clearNote() {
  note.value = ''
  savedMessage.value = 'Note cleared.'
}

async function greetFromRust() {
  try {
    nativeMessage.value = await invoke<string>('greet', { name: 'Nuxt' })
  } catch {
    nativeMessage.value = 'This button needs the Tauri desktop shell to call Rust.'
  }
}
</script>

<template>
  <main class="page-shell">
    <section class="learning-card" aria-labelledby="page-title">
      <div class="eyebrow">Nuxt 4 + Tauri learning sandbox</div>
      <h1 id="page-title">Learning Notes</h1>
      <p class="intro">
        This tiny app demonstrates Nuxt reactivity in the browser. Tauri will later display this same page in a native desktop window.
      </p>

      <label class="label" for="note">Write a note</label>
      <textarea
        id="note"
        v-model="note"
        placeholder="Try typing here..."
        rows="8"
      />

      <div class="note-footer">
        <span>{{ savedMessage }}</span>
        <button class="secondary-button" type="button" @click="clearNote">Clear note</button>
      </div>

      <div class="bridge-demo">
        <div>
          <strong>Frontend → Rust</strong>
          <p>{{ nativeMessage }}</p>
        </div>
        <button class="primary-button" type="button" @click="greetFromRust">Call native command</button>
      </div>

      <dl class="stats" aria-label="Note statistics">
        <div>
          <dt>{{ characterCount }}</dt>
          <dd>characters</dd>
        </div>
        <div>
          <dt>{{ wordCount }}</dt>
          <dd>words</dd>
        </div>
      </dl>
    </section>
  </main>
</template>
