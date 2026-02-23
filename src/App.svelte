<script>
  import { onMount } from 'svelte'
  import Nav from './lib/Nav.svelte'
  import ProjectCard from './lib/ProjectCard.svelte'
  // FontAwesome imports
  import { faGithub, faLinkedin } from '@fortawesome/free-brands-svg-icons'
  import { faEnvelope } from '@fortawesome/free-solid-svg-icons'
  import { FontAwesomeIcon } from '@fortawesome/svelte-fontawesome'
  import { scale } from 'svelte/transition'

  //#region State and const variables
    const email = 'jacobmcewen7@gmail.com'
    let showOutside = $state(false)
    let copied = $state(false)
  //#endregion

  const projects = [
    {
      title: 'Project 1',
      description: 'description 1.',
      tags: ['Svelte', 'Vite', 'CSS'],
      link: '#'
    },
    {
      title: 'Project 2',
      description: 'a short description of the project.',
      tags: ['HTML', 'more tags', 'Accessibility'],
      link: '#'
    },
    {
      title: 'Project 3',
      description: 'desc',
      tags: ['Node', 'APIs', 'some tag goes here'],
      link: '#'
    },
    {
      title: 'Project 4',
      description: 'description 4',
      tags: ['Open Source', 'Tests', 'CI'],
      link: '#'
    }
  ]

  onMount(() => {
    const observer = new IntersectionObserver((entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          entry.target.classList.add('in-view')
          observer.unobserve(entry.target)
        }
      })
    }, { threshold: 0.12 })

    document.querySelectorAll('.section, .card').forEach((el) => observer.observe(el))
  })

  function openOutside() {
    showOutside = true
  }
  function closeOutside() {
    showOutside = false
  }
  async function copyEmail() {
    await navigator.clipboard.writeText(email)
    copied = true
    setTimeout(() => copied = false, 1200)
  }
</script>

<Nav />

<main id="app">
  <section id="home" class="hero section">
    <div class="hero-inner container">
      <div>
        <h1 class="hero-name">Jacob McEwen</h1>
        <p class="subtitle">Full-Stack Software Engineer.</p>

        <p class="hero-actions">
          <a class="cta" href="#projects">See my work</a>
          <button class="ghost" onclick={openOutside}>Outside of code</button>
        </p>

        <p class="sm-icons">
          <a href="https://github.com/jacobmcazure" target="_blank" rel="noopener" aria-label="GitHub">
            <FontAwesomeIcon icon={faGithub} />
          </a>
          <a href="https://www.linkedin.com/in/jacob-mcewen/" target="_blank" rel="noopener" aria-label="LinkedIn">
            <FontAwesomeIcon icon={faLinkedin} />
          </a>
          <button class="icon-btn" onclick={copyEmail} aria-label="Copy email">
            <FontAwesomeIcon icon={faEnvelope} />
            {#if copied}
              <span class="copied">Copied!</span>
            {/if}
          </button>
        </p>
      </div>
    </div>
  </section>

  <section id="about" class="section">
    <div class="container">
      <h2>About</h2>
      <p>26 yr old full‑stack developer from the United States.</p>
    </div>
  </section>

  <section id="projects" class="section">
    <div class="container">
      <h2>Selected Projects</h2>
      <div class="card-grid">
        {#each projects as p (p.title)}
          <ProjectCard title={p.title} description={p.description} tags={p.tags} link={p.link} />
        {/each}
      </div>
    </div>
  </section>

  <section id="resume" class="section">
    <div class="container">
      <h2>Resume</h2>
      <p>Download a PDF or email me for a tailored copy — <a href="mailto:jacobmcewen7@gmail.com">jacobmcewen7@gmail.com</a>.</p>
    </div>
  </section>

  <section id="contact" class="section">
    <div class="container">
      <h2>Contact</h2>
      <p>If you'd like to work together, say hello: <a href="mailto:jacobmcewen7@gmail.com">jacobmcewen7@gmail.com</a></p>
    </div>
  </section>

  <footer class="site-footer">
    <div class="container">© {new Date().getFullYear()} Jacob McEwen · Built with Svelte + Vite</div>
  </footer>
</main>

{#if showOutside}
  <div class="overlay" role="dialog" aria-modal="true">
    <div class="modal" transition:scale={{ duration: 220, start: 0.96 }}>
      <h3>My Life Outside of code</h3>
      <p>I have been developing myself as a person for the past few years, and along the way, 
        have built a structured routine and found many things I enjoy in my free time.
      </p>
      <p>
        These include boxing, reading, language learning, meditation, and playing guitar. I am very proud of my journey to this point in my life while
        simultaneously realizing there is so much more still out there.
      </p>
      <p>
        I wish that anyone reading this may one day feel a similar feeling of
        accomplishment, confidence, and joy in their own life, whatever that may look like for them.
      </p>
      <p>
        You are capable of more thank you think.
      </p>
      <p>
        <i>"If you think something is impossible, you'll only make it impossible." - Bruce Lee</i>
      </p>
      <p><button class="cta" onclick={closeOutside}>Close</button></p>
    </div>
  </div>
{/if}

<style>
  /* layout + variables are in src/app.css; keep component-scoped adjustments minimal */
  .overlay {
    position: fixed;
    inset: 0;
    display: grid;
    place-items: center;
    background: rgba(2,6,23,0.6);
    z-index: 60;
  }
  .modal {
    background: var(--card-bg);
    color: var(--text);
    padding: 1.25rem;
    border-radius: 12px;
    max-width: 640px;
    width: calc(100% - 48px);
    box-shadow: var(--shadow);
    border: 1px solid rgba(255,255,255,0.03);
  }

  .sm-icons {
    margin-top: 18px;
    display: flex;
    gap: 16px;
    align-items: center;
  }
  .icon-btn {
    background: transparent;
    border: none;
    color: var(--muted);
    font-size: 1.2rem;
    cursor: pointer;
    position: relative;
    padding: 0;
  }
  .icon-btn:hover {
    color: var(--accent);
  }
  .copied {
    position: absolute;
    left: 2.2em;
    top: 0;
    font-size: 0.9em;
    color: var(--accent);
    background: var(--glass);
    border-radius: 6px;
    padding: 2px 8px;
    pointer-events: none;
    opacity: 0.92;
  }
</style>