<template>
  <div class="darkmode-wrapper">
    <input
      id="checkbox"
      class="switch-checkbox"
      type="checkbox"
      @change="toggleTheme"
    />
    <label for="checkbox" class="switch-label">
      <span>🌙</span>
      <span>☀️</span>
      <div
        class="switch-toggle"
        :class="{ 'switch-toggle-checked': userTheme === 'dark-theme' }"
      ></div>
    </label>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userTheme: 'light-theme',
    }
  },
  mounted() {
    const initUserTheme = this.getTheme() || this.getMediaPreference()
    this.setTheme(initUserTheme)
  },

  methods: {
    toggleTheme() {
      const activeTheme = localStorage.getItem('user-theme')
      if (activeTheme === 'light-theme') {
        this.setTheme('dark-theme')
      } else {
        this.setTheme('light-theme')
      }
    },

    getTheme() {
      return localStorage.getItem('user-theme')
    },

    setTheme(theme) {
      localStorage.setItem('user-theme', theme)
      this.userTheme = theme
      document.documentElement.className = theme
    },

    getMediaPreference() {
      const hasDarkPreference = window.matchMedia(
        '(prefers-color-scheme: dark)'
      ).matches
      if (hasDarkPreference) {
        return 'dark-theme'
      } else {
        return 'light-theme'
      }
    },
  },
}
</script>

<style scoped>
.switch-checkbox {
  display: none;
}

.switch-label {
  align-items: center;
  background: var(--text-primary-color);
  border: calc(var(--element-size) * 0.025) solid var(--accent-color);
  border-radius: var(--element-size);
  cursor: pointer;
  display: flex;
  font-size: calc(var(--element-size) * 0.3);
  height: calc(var(--element-size) * 0.55);
  position: relative;
  padding: calc(var(--element-size) * 0.1);
  transition: background 0.5s ease;
  justify-content: space-between;
  width: var(--element-size);
  z-index: 1;
}

.switch-toggle {
  position: absolute;
  background-color: var(--background-color-primary);
  border-radius: 50%;
  top: calc(var(--element-size) * 0.07);
  left: calc(var(--element-size) * 0.2);
  height: calc(var(--element-size) * 0.4);
  width: calc(var(--element-size) * 0.4);
  margin-left: -5px;

  transform: translateX(0);
  transition: transform 0.3s ease, background-color 0.5s ease;
}

.switch-toggle-checked {
  transform: translateX(calc(var(--element-size) * 0.4)) !important;
}
</style>
