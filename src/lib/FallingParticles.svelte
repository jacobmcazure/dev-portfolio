<script>
  import { onMount, onDestroy } from 'svelte';

  let canvas;
  let particles = [];
  let animFrame = 0;
  let isDark = true; //in localstorage

  const PARTICLE_COUNT = 60;

  // --- Detect dark mode ---
  // Checks for a "dark" class on <html> (Tailwind convention)
  // and also listens for system preference changes.
  function checkDarkMode() {
    //return document.documentElement.classList.contains('dark');
    return localStorage.getItem('theme') === 'dark';
  }

  // --- Particle factories ---
  function createPetal(w, h, startAtTop = false) {
    const size = 6 + Math.random() * 10;
    return {
      type: 'petal',
      x: Math.random() * w,
      y: startAtTop ? -size - Math.random() * h * 0.3 : Math.random() * h,
      size,
      speedY: 0.3 + Math.random() * 0.7,
      speedX: -0.2 + Math.random() * 0.4,
      rotation: Math.random() * Math.PI * 2,
      rotationSpeed: (Math.random() - 0.5) * 0.02,
      opacity: 0.25 + Math.random() * 0.45,
      petalWidth: size * (0.6 + Math.random() * 0.4),
      petalHeight: size * (0.8 + Math.random() * 0.5),
      swayAmplitude: 0.3 + Math.random() * 0.8,
      swayOffset: Math.random() * Math.PI * 2,
    };
  }

  function createSnowflake(w, h, startAtTop = false) {
    const size = 2 + Math.random() * 4;
    return {
      type: 'snow',
      x: Math.random() * w,
      y: startAtTop ? -size - Math.random() * h * 0.3 : Math.random() * h,
      size,
      speedY: 0.2 + Math.random() * 0.5,
      speedX: 0,
      rotation: 0,
      rotationSpeed: 0,
      opacity: 0.15 + Math.random() * 0.4,
      drift: (Math.random() - 0.5) * 0.4,
      swayAmplitude: 0.2 + Math.random() * 0.5,
      swayOffset: Math.random() * Math.PI * 2,
    };
  }

  // --- Drawing ---
  function drawPetal(ctx, p, time) {
    ctx.save();
    ctx.translate(p.x, p.y);
    ctx.rotate(p.rotation);
    ctx.globalAlpha = p.opacity;

    const w = p.petalWidth;
    const h = p.petalHeight;

    // Petal shape via bezier curves
    ctx.beginPath();
    ctx.moveTo(0, -h / 2);
    ctx.bezierCurveTo(w / 2, -h / 3, w / 2, h / 3, 0, h / 2);
    ctx.bezierCurveTo(-w / 2, h / 3, -w / 2, -h / 3, 0, -h / 2);
    ctx.closePath();

    const sway = Math.sin(time * 0.001 + p.swayOffset) * 0.15;
    const r = 240 + Math.floor(sway * 15);
    const g = 180 + Math.floor(sway * 20);
    const b = 190 + Math.floor(sway * 10);
    ctx.fillStyle = `rgb(${r}, ${g}, ${b})`;
    ctx.fill();

    // Subtle vein
    ctx.beginPath();
    ctx.moveTo(0, -h / 2 + 2);
    ctx.lineTo(0, h / 2 - 2);
    ctx.strokeStyle = `rgba(210, 150, 160, ${p.opacity * 0.4})`;
    ctx.lineWidth = 0.5;
    ctx.stroke();

    ctx.restore();
  }

  function drawSnowflake(ctx, p) {
    ctx.save();
    ctx.translate(p.x, p.y);
    ctx.globalAlpha = p.opacity;

    // Radial glow
    const gradient = ctx.createRadialGradient(0, 0, 0, 0, 0, p.size);
    gradient.addColorStop(0, 'rgba(220, 230, 245, 1)');
    gradient.addColorStop(0.5, 'rgba(200, 215, 235, 0.6)');
    gradient.addColorStop(1, 'rgba(180, 200, 225, 0)');

    ctx.beginPath();
    ctx.arc(0, 0, p.size, 0, Math.PI * 2);
    ctx.fillStyle = gradient;
    ctx.fill();

    // Crystal arms on larger flakes
    if (p.size > 3) {
      ctx.strokeStyle = `rgba(230, 240, 255, ${p.opacity * 0.5})`;
      ctx.lineWidth = 0.4;
      const arm = p.size * 0.5;
      for (let i = 0; i < 3; i++) {
        const angle = (i * Math.PI) / 3;
        ctx.beginPath();
        ctx.moveTo(-Math.cos(angle) * arm, -Math.sin(angle) * arm);
        ctx.lineTo(Math.cos(angle) * arm, Math.sin(angle) * arm);
        ctx.stroke();
      }
    }

    ctx.restore();
  }

  // --- Initialization ---
  function initParticles(w, h, dark, scatter = false) {
    const arr = [];
    for (let i = 0; i < PARTICLE_COUNT; i++) {
      arr.push(dark ? createSnowflake(w, h, scatter) : createPetal(w, h, scatter));
    }
    return arr;
  }

  // --- Lifecycle ---
  let observer;

  onMount(() => {
    const ctx = canvas.getContext('2d');
    if (!ctx) return;

    function resize() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
      isDark = checkDarkMode();
      particles = initParticles(canvas.width, canvas.height, isDark, false);
    }

    resize();
    window.addEventListener('resize', resize);

    // Watch for class changes on <html> to detect theme toggle
    observer = new MutationObserver(() => {
      const wasDark = isDark;
      isDark = checkDarkMode();
      if (wasDark !== isDark) {
        particles = initParticles(canvas.width, canvas.height, isDark, false);
      }
    });
    observer.observe(document.documentElement, {
      attributes: true,
      attributeFilter: ['class'],
    });

    function animate(time) {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      for (let i = 0; i < particles.length; i++) {
        const p = particles[i];

        if (isDark) {
          const sway = Math.sin(time * 0.0008 + p.swayOffset) * (p.swayAmplitude || 0.3);
          p.x += (p.drift || 0) + sway * 0.3;
          p.y += p.speedY;
          drawSnowflake(ctx, p);

          if (p.y > canvas.height + p.size * 2) {
            particles[i] = createSnowflake(canvas.width, canvas.height, true);
          }
          if (p.x < -20) p.x = canvas.width + 10;
          if (p.x > canvas.width + 20) p.x = -10;
        } else {
          const sway = Math.sin(time * 0.001 + p.swayOffset) * (p.swayAmplitude || 0.5);
          p.x += p.speedX + sway * 0.4;
          p.y += p.speedY;
          p.rotation += p.rotationSpeed;
          drawPetal(ctx, p, time);

          if (p.y > canvas.height + p.size * 2) {
            particles[i] = createPetal(canvas.width, canvas.height, true);
          }
          if (p.x < -30) p.x = canvas.width + 15;
          if (p.x > canvas.width + 30) p.x = -15;
        }
      }

      animFrame = requestAnimationFrame(animate);
    }

    animFrame = requestAnimationFrame(animate);

    return () => {
      window.removeEventListener('resize', resize);
      cancelAnimationFrame(animFrame);
      if (observer) observer.disconnect();
    };
  });

  onDestroy(() => {
    cancelAnimationFrame(animFrame);
    if (observer) observer.disconnect();
  });
</script>

<canvas
  bind:this={canvas}
  class="falling-particles"
  aria-hidden="true"
></canvas>

<style>
  .falling-particles {
    position: fixed;
    inset: 0;
    z-index: 0;
    pointer-events: none;
    width: 100%;
    height: 100%;
  }
</style>
