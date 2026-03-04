<script>
  import { onMount } from 'svelte'
  let theme = 'dark'

  onMount(() => {
    const stored = localStorage.getItem('theme')
    if (stored) {
      theme = stored
      document.documentElement.classList.toggle('light', theme === 'light')
    }
  })

  function toggleTheme() {
    theme = theme === 'dark' ? 'light' : 'dark'
    document.documentElement.classList.toggle('light', theme === 'light')
    localStorage.setItem('theme', theme)
  }
</script>

<header class="navbar">
  <div class="nav-container">
    <a class="brand" href="#home">Jacob McEwen</a>

    <nav class="nav-options" aria-label="Primary">
      <a href="#home">Home</a>
      <a href="#about">About</a>
      <a href="#projects">Projects</a>
      <a href="/public/Resume-Jacob-McEwen.pdf" target="_blank" rel="noopener noreferrer">Resume</a>

      <button class="theme-toggle" aria-pressed={theme === 'light'} title="Toggle theme" on:click={toggleTheme}>
        {#if theme === 'light'}
          <span aria-hidden="true">🌞</span>
        {:else}
          <span aria-hidden="true">🌙</span>
        {/if}
      </button>
    </nav>
  </div>
</header>
