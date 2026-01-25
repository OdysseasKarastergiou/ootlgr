<script setup>
import { ref, onMounted, watch } from 'vue'

const isOpen = ref(false)
const root = document.documentElement
const STORAGE_KEY = 'a11y-settings'

const settings = ref({
  fontSize: 0,
  classes: [],
})

/* Load saved state */
onMounted(() => {
  const saved = localStorage.getItem(STORAGE_KEY)
  if (saved) settings.value = JSON.parse(saved)
  applySettings()
})

/* Persist + apply */
watch(
  settings,
  () => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(settings.value))
    applySettings()
  },
  { deep: true },
)

function applySettings() {
  root.style.fontSize = settings.value.fontSize ? `${16 + settings.value.fontSize}px` : ''
  root.className = settings.value.classes.join(' ')
}

function toggleClass(className) {
  const i = settings.value.classes.indexOf(className)
  i === -1 ? settings.value.classes.push(className) : settings.value.classes.splice(i, 1)
}

function isActive(className) {
  return settings.value.classes.includes(className)
}

function action(type) {
  switch (type) {
    case 'plus':
      settings.value.fontSize += 2
      break
    case 'minus':
      settings.value.fontSize -= 2
      break
    case 'grayscale':
      toggleClass('a11y-grayscale')
      break
    case 'contrast':
      toggleClass('a11y-high-contrast')
      break
    case 'negative':
      toggleClass('a11y-negative-contrast')
      break
    case 'light':
      toggleClass('a11y-light-bg')
      break
    case 'underline':
      toggleClass('a11y-underline-links')
      break
    case 'font':
      toggleClass('a11y-readable-font')
      break
    case 'reset':
      settings.value = { fontSize: 0, classes: [] }
      break
  }
}
</script>

<template>
  <nav class="a11y-toolbar" aria-label="Accessibility tools">
    <!-- Toggle button -->
    <button
      class="a11y-toggle"
      :aria-expanded="isOpen"
      aria-controls="a11y-panel"
      @click="isOpen = !isOpen"
    >
      <i class="fa-solid fa-universal-access" aria-hidden="true"></i>
      <span class="sr-only">Open accessibility tools</span>
    </button>

    <Transition name="a11y-slide">
      <div v-if="isOpen" id="a11y-panel" class="a11y-panel">
        <!-- Font size -->
        <button @click="action('plus')">
          <i class="fa-solid fa-text-height"></i>
          <span>Increase Text</span>
        </button>

        <button @click="action('minus')">
          <i class="fa-solid fa-text-height fa-rotate-180"></i>
          <span>Decrease Text</span>
        </button>

        <!-- Toggle buttons -->
        <button
          :class="{ active: isActive('a11y-grayscale') }"
          :aria-pressed="isActive('a11y-grayscale')"
          @click="action('grayscale')"
        >
          <i class="fa-solid fa-adjust"></i>
          <span>Grayscale</span>
        </button>

        <button
          :class="{ active: isActive('a11y-high-contrast') }"
          :aria-pressed="isActive('a11y-high-contrast')"
          @click="action('contrast')"
        >
          <i class="fa-solid fa-circle-half-stroke"></i>
          <span>High Contrast</span>
        </button>

        <button
          :class="{ active: isActive('a11y-negative-contrast') }"
          :aria-pressed="isActive('a11y-negative-contrast')"
          @click="action('negative')"
        >
          <i class="fa-solid fa-arrows-rotate"></i>
          <span>Negative Contrast</span>
        </button>

        <button
          :class="{ active: isActive('a11y-light-bg') }"
          :aria-pressed="isActive('a11y-light-bg')"
          @click="action('light')"
        >
          <i class="fa-solid fa-sun"></i>
          <span>Light Background</span>
        </button>

        <button
          :class="{ active: isActive('a11y-underline-links') }"
          :aria-pressed="isActive('a11y-underline-links')"
          @click="action('underline')"
        >
          <i class="fa-solid fa-link"></i>
          <span>Underline Links</span>
        </button>

        <button
          :class="{ active: isActive('a11y-readable-font') }"
          :aria-pressed="isActive('a11y-readable-font')"
          @click="action('font')"
        >
          <i class="fa-solid fa-font"></i>
          <span>Readable Font</span>
        </button>

        <!-- Reset -->
        <button @click="action('reset')">
          <i class="fa-solid fa-rotate-left"></i>
          <span>Reset</span>
        </button>
      </div>
    </Transition>
  </nav>
</template>
