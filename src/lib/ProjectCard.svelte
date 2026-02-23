<script>
  export let title = ''
  export let description = ''
  export let tags = []
  export let link = '#'
</script>

<article class="card project-card">
  <div class="card-body">
    <h4>{title}</h4>
    <p class="muted">{description}</p>
    <div class="tags">
      {#each tags as t}
        <span class="tag">{t}</span>
      {/each}
    </div>
  </div>

  <div class="card-actions">
    <a class="text-link" href={link} target="_blank" rel="noopener">View</a>
  </div>
</article>
