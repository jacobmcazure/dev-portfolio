<script>
  import { onMount } from 'svelte'
  import Nav from './lib/Nav.svelte'
  import ProjectCard from './lib/ProjectCard.svelte'
  import FallingParticles from './lib/FallingParticles.svelte'

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
      title: 'Appointment Scheduler',
      description: 'Scheduling application for a consulting company. Manages contacts, customers, and appointments. Connects to a backend database.',
      tags: ['Java', 'MySQL', 'IntelliJ', 'OOP', 'MVC', 'JavaFX'],
      link: 'https://github.com/jacobmcazure/Appointment-Scheduler'
    },
    {
      title: 'Diabetes Health Indicator',
      description: 'Machine Learning model that classifies patients by predicting whether a patient is non-diabetic or is prediabetic/has diabetes.',
      tags: ['Python', 'Jupyter Notebook', 'Machine Learning', 'NumPy', 'Pandas', 'Matplotlib', 'Scikit-learn', 'Anaconda'],
      link: 'https://github.com/jacobmcazure/Diabetes-Health-Indicators'
    },
    {
      title: 'Package Delivery Truck Router',
      description: 'Calculates routes for delivery trucks. Uses a variant of Dijkstra\'s algorithm done by hand.',
      tags: ['Python', 'PyCharm', 'Flask'],
      link: 'https://github.com/jacobmcazure/Package-Delivery'
    },
    {
      title: 'Inventory Management System',
      description: 'Application that manages inventory for a small business.',
      tags: ['Java', 'OOP', 'IntelliJ', 'MVC'],
      link: 'https://github.com/jacobmcazure/Inventory-Manager'
    }
  ]
  
  /***  Intersection Observer for fade-in on scroll 
  * when an element enters the viewport, add an "in-view" class that adds css transition effects, then unobserve it so it only triggers once.
  * threshold: trigger when that amount of the element becomes visible.
  * adds "in-view" class and never removes it so the transition stays with the element after the first time.
  * we just unobserve it so it does not trigger after every time it enters the viewport (since the class is already applied).
  */
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
  <FallingParticles />
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
      <p>A 26 yr old full‑stack developer from the United States. I mainly work on ASP.NET with javascript and C#, however in my free time,
        I have recently been enjoying Python and C++. I have been set a goal to contribute to more open source projects this year to
        improve myself as an engineer and give back to the community that runs the software I love using every day.
      </p>
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

  <section id="books" class="section">
    <div class="container">
      <h2>Booklist</h2>
      <p>A collection of computer science books that I am making my way through in my free time!</p>
      <ul>
        <li><a href="https://www.goodreads.com/book/show/23463279-designing-data-intensive-applications" target="_blank" rel="noopener">Designing Data-Intensive Applications</a> by Martin Kleppmann (Finished)</li>
        <li><a href="https://www.goodreads.com/book/show/17374825-operating-systems" target="_blank" rel="noopener">Operating Systems</a> by Remzi & Andrea Arpaci-Dusseau (67% done)</li>
        <li><a href="https://www.goodreads.com/book/show/9407722-dependency-injection-in-net" target="_blank" rel="noopener">Dependency Injection in .NET</a> by Mark Seemann (10% done)</li>
      </ul>
      </div>
  </section>

  <footer class="site-footer">
    <div class="container">© {new Date().getFullYear()} Jacob McEwen</div>
  </footer>
</main>

<!-- Popup -->
{#if showOutside}
  <div class="overlay" role="dialog" aria-modal="true">
    <div class="modal" transition:scale={{ duration: 220, start: 0.96 }}>
      <h3>My Life Outside of code</h3>
      <p>I have been developing myself as a person for the past few years, and along the way, 
        have built a structured routine and found many things I enjoy in my free time.
      </p>
      <p>
        These include boxing, reading, language learning, meditation, and playing guitar.
      </p>
      <p>
        I wish that anyone reading this may one day feel a similar feeling of
        accomplishment, confidence, and joy in their own life, whatever that may look like for them.
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
    transition: opacity 0.5s ease-in-out; 
  }
</style>