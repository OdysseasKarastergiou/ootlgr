<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import SiteLogo from './assets/SiteLogo.webp'
import footerLogo from './assets/footerLogo.png'
import AccessibilityToolbar from './components/AccessibilityToolbar.vue'

const active = ref(0)
const slidesCount = 4
const businessEmail = 'info@ootl.gr' // change this to your business email
const isMobile = ref(false)

function checkMobile() {
  isMobile.value = window.innerWidth <= 640
}

function goto(i) {
  active.value = Math.max(0, Math.min(slidesCount - 1, i))
}

function next() {
  goto(active.value + 1)
}
function prev() {
  goto(active.value - 1)
}

// wheel navigation with small debounce
let wheelTimeout = null
function onWheel(e) {
  if (isMobile.value) return
  if (wheelTimeout) return
  if (e.deltaY > 10) next()
  else if (e.deltaY < -10) prev()
  wheelTimeout = setTimeout(() => (wheelTimeout = null), 300)
}

// touch support
let touchStartY = 0
function onTouchStart(e) {
  touchStartY = e.touches[0].clientY
}
function onTouchEnd(e) {
  if (isMobile.value) return
  const diff = e.changedTouches[0].clientY - touchStartY
  if (diff < -50) next()
  else if (diff > 50) prev()
}

function onKey(e) {
  if (e.key === 'ArrowDown' || e.key === 'PageDown') next()
  if (e.key === 'ArrowUp' || e.key === 'PageUp') prev()
}

onMounted(() => {
  window.addEventListener('wheel', onWheel, { passive: true })
  window.addEventListener('touchstart', onTouchStart, { passive: true })
  window.addEventListener('touchend', onTouchEnd, { passive: true })
  window.addEventListener('keydown', onKey)
  window.addEventListener('resize', checkMobile)
  checkMobile()
})
onBeforeUnmount(() => {
  window.removeEventListener('wheel', onWheel)
  window.removeEventListener('touchstart', onTouchStart)
  window.removeEventListener('touchend', onTouchEnd)
  window.removeEventListener('keydown', onKey)
  window.removeEventListener('resize', checkMobile)
})

// simple mailto opener for contact button
function openMailto() {
  const subject = 'Contact via website'
  const body = ''
  const mailto = `mailto:${businessEmail}?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(body)}`
  window.location.href = mailto
}
</script>

<template>
  <div class="app-root">
    <AccessibilityToolbar />
    <div
      class="slides"
      :class="{ mobile: isMobile }"
      :style="isMobile ? null : { transform: `translateY(${-active * 100}vh)` }"
    >
      <!-- Slide 1: Hero with background image and title -->
      <section class="slide slide-hero">
        <div class="hero-content">
          <img :src="SiteLogo" alt="Site logo" class="site-logo" />
          <hr class="thin-sep hero-sep" />
        </div>
      </section>

      <!-- Slide 2: Placeholder center text -->
      <section class="slide slide-center">
        <div class="center-box">
          <p class="placeholder-text">
            Σας παρουσιάζουμε το <b>Out Of The Loop</b>, όπου επαναπροσδιορίζουμε τον τομέα της
            κατασκευής, μετατρέποντας υφασμάτινα απορρίμματα σε αειφόρα τούβλα. Η καινοτόμα
            προσέγγισή μας μειώνει τα απόβλητα και δημιουργεί ανθεκτικά, χρηστικά υλικά διακόσμησης.
          </p>
          <hr class="thin-sep" />
          <p class="lead-text">
            Η αποστολή μας είναι να παρέχουμε αποτελεσματικές, πολλαπλών χρήσεων και προσαρμόσιμες
            λύσεις που υπερβαίνουν τις οικολογικά καταστροφικές πρακτικες.
          </p>
          <hr class="thin-sep center-mobile-sep" />
        </div>
      </section>

      <!-- Slide 3: Contact CTA -->
      <section class="slide slide-cta">
        <div class="form-box">
          <p class="contact-instructions">
            Ελάτε να χτίσουμε ένα πιο πράσινο μέλλον, ένα τούβλο κάθε φορά
          </p>
          <div class="contact-cta">
            <button class="contact-btn" @click="openMailto">
              Contact us
              <svg viewBox="0 0 24 24" aria-hidden="true" focusable="false">
                <path d="M5 12h12M13 6l6 6-6 6" />
              </svg>
            </button>
          </div>
        </div>
      </section>

      <!-- Slide 4: Contact info (moved to fourth) -->
      <section class="slide slide-contact">
        <div class="contact-box">
          <h2>Contact</h2>
          <ul class="contact-list">
            <li>
              <span class="icon"><i class="fa-solid fa-map-pin" aria-hidden="true"></i></span>
              <span class="value">ΓΡΑΒΙΑΣ 38, ΘΕΣΣΑΛΟΝΙΚΗ, 54645</span>
            </li>
            <li>
              <span class="icon"><i class="fa-solid fa-phone" aria-hidden="true"></i></span>
              <span class="value">+30 2315153146</span>
            </li>
            <li>
              <span class="icon"><i class="fa-solid fa-envelope" aria-hidden="true"></i></span>
              <span class="value">{{ businessEmail }}</span>
            </li>
            <li>
              <span class="icon"
                ><i class="fa-solid fa-scale-balanced" aria-hidden="true"></i
              ></span>
              <span class="value"
                >Α.Φ.Μ.: 139980514 - Δ.Ο.Υ.: ΚΑΛΑΜΑΡΙΑΣ ΑΡ. Γ.Ε.ΜΗ.: 174026105000</span
              >
            </li>
          </ul>

          <div class="contact-footer">
            <img :src="footerLogo" alt="logo" class="site-logo-small" />
            <div class="copyright">© 2026. All rights reserved.</div>
          </div>
        </div>
      </section>
    </div>

    <!-- Right-side bullets -->
    <nav class="dots" aria-hidden="false">
      <ul>
        <li v-for="i in 4" :key="i">
          <button
            :class="{ active: active === i - 1 }"
            @click="goto(i - 1)"
            :aria-label="`Go to slide ${i}`"
          ></button>
        </li>
      </ul>
    </nav>
  </div>
</template>

<style scoped>
* {
  box-sizing: border-box;
}
.app-root {
  height: 100vh;
  overflow: hidden;
  position: relative;
  font-family:
    system-ui,
    -apple-system,
    Segoe UI,
    Roboto,
    'Helvetica Neue',
    Arial;
}
.slides {
  height: 400vh;
  transition: transform 0.7s cubic-bezier(0.22, 0.9, 0.32, 1);
}
.slide {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* Slide 1 hero */
.slide-hero {
  background: linear-gradient(rgba(0, 0, 0, 0.05), rgba(0, 0, 0, 0.03));
  color: #111;
}
.hero-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 18px;
  text-align: center;
}
.site-logo {
  max-width: 70%;
  height: auto;
  width: auto;
  display: block;
}
.site-title {
  font-size: clamp(20px, 4vw, 48px);
  letter-spacing: 4px;
  margin: 0;
}

/* Slide 2 */
.slide-center {
  background: #f7f7f7;
  color: #111;
}
.center-box {
  max-width: 80%;
  padding: 24px;
  text-align: center;
}
.placeholder-text {
  font-size: 1.25rem;
}
.thin-sep {
  width: 60%;
  max-width: 720px;
  height: 1px;
  background: black;
  border: none;
  margin: 100px auto;
}
.center-mobile-sep {
  display: none;
}
.hero-sep {
  width: 60%;
  max-width: 250px;
  margin: 40px auto;
  display: none;
}

/* hide the separator inside slide-center on desktop; show on mobile */
.lead-text {
  font-size: 1.2em;
  font-weight: 600;
  margin: 0;
  text-align: center;
}

/* Slide 3 contact */
.slide-contact {
  background: #fff;
  color: #111;
}
.contact-box {
  max-width: 720px;
  padding: 32px;
}
.contact-list {
  list-style: none;
  padding: 0;
  margin: 12px 0;
  display: grid;
  gap: 14px;
}
.contact-list li {
  display: flex;
  gap: 12px;
  align-items: center;
}
.icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background: #734e36;
  color: #fff;
  font-size: 1rem;
  flex: 0 0 36px;
}
.label {
  font-weight: 600;
  margin-right: 6px;
  width: 140px;
  flex: 0 0 140px;
}
.value {
  flex: 1;
}

.contact-footer {
  margin-top: 22px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
}
.site-logo-small {
  max-width: 300px;
  height: auto;
  opacity: 0.95;
  margin-top: 3em;
}
.copyright {
  font-size: 1em;
  color: black;
  font-weight: 600;
}

/* Slide 4 form */
.slide-form {
  background: linear-gradient(180deg, #eef2ff, #f8fafc);
}
.form-box {
  width: 100%;
  max-width: 680px;
  padding: 24px;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.contact-form {
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.contact-form input,
.contact-form textarea {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 6px;
}
.contact-form button {
  background: #111;
  color: #fff;
  border: none;
  padding: 10px 14px;
  border-radius: 6px;
  cursor: pointer;
}
.contact-form button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}
.contact-instructions {
  font-size: 1.1em;
  font-weight: 500;
  margin: 8px 0 14px;
  text-align: center;
  margin-bottom: 1.5em;
}
.contact-cta {
  display: flex;
  justify-content: center;
}
.contact-btn {
  background: transparent;
  color: #734e36;
  border: 2px solid #734e36;
  padding: 10px 18px;
  border-radius: 50px;
  cursor: pointer;
  font-weight: 600;
  display: inline-flex;
  align-items: center;
  gap: 8px;
  transition:
    background 0.18s ease,
    color 0.18s ease,
    transform 0.12s ease;
}
.contact-btn svg {
  width: 16px;
  height: 16px;
}
.contact-btn svg path {
  stroke: currentColor;
  stroke-width: 2;
  fill: none;
  stroke-linecap: round;
  stroke-linejoin: round;
}
.contact-btn:hover {
  background: #734e36;
  color: #fff;
  transform: translateY(-2px);
}
/* form feedback removed for mailto behavior */

/* Dots nav */
.dots {
  position: fixed;
  right: 18px;
  top: 50%;
  transform: translateY(-50%);
  z-index: 20;
}
.dots ul {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 0;
  margin: 0;
  list-style: none;
}
.dots button {
  width: 12px;
  height: 12px;
  border-radius: 999px;
  border: 2px solid rgba(0, 0, 0, 0.25);
  background: transparent;
  cursor: pointer;
  padding: 0;
  transition:
    transform 0.18s ease,
    border-color 0.18s ease,
    background-color 0.18s ease;
}
.dots button:hover {
  transform: scale(1.1);
}
.dots button.active {
  background: #734e36;
  border-color: #734e36;
  transform: scale(1.25);
}

@media (max-width: 640px) {
  .dots {
    right: 12px;
  }
  .dots {
    display: none;
  }
  .site-title {
    font-size: 28px;
  }
  .label {
    width: 110px;
    flex: 0 0 110px;
  }
  .site-logo {
    max-width: 550px;
  }
  .site-logo-small {
    max-width: 200px;
  }
  .dots button {
    width: 8px;
    height: 8px;
  }
  .placeholder-text {
    font-size: 1.1em;
  }
  .lead-text {
    font-size: 1.1em;
  }
  .thin-sep {
    width: 90%;
    margin: 5em auto;
  }
  .slide-center .thin-sep {
    display: block;
  }
  .center-mobile-sep {
    display: block;
  }
  .hero-content {
    gap: 10px;
  }
  .center-box {
    padding: 16px;
  }
  .lead-text {
    margin-top: 12px;
  }
  .hero-sep {
    display: block;
  }
  /* enable normal scrolling on mobile */
  .app-root {
    height: auto;
    overflow: auto;
  }
  .slides {
    height: auto;
    transition: none;
  }
  .slide {
    height: auto;
    min-height: 20vh;
  }
}

/* CTA slide styling */
.slide-cta {
  background: #f7f7f7;
  color: #111;
}
.slide-cta .form-box {
  max-width: 680px;
  padding: 32px;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}
</style>
