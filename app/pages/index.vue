<script setup lang="ts">
const storageKey = 'nuxt-tauri-learning-note'
const note = ref('')
const savedMessage = ref('Your note is saved locally in this browser.')

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
