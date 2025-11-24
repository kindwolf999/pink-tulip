<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Feliz Mes, Mi Vida</title>
  <style>
    :root{
      --bg: linear-gradient(180deg,#0f1020,#2b1328 50%, #ffecf2 100%);
      --card-bg: rgba(255,255,255,0.06);
      --accent: #ff6b87;
      --muted: rgba(255,255,255,0.8);
    }
    *{box-sizing:border-box}
    body{
      margin:0;min-height:100vh;
      display:flex;align-items:center;justify-content:center;
      background: radial-gradient(800px 400px at 10% 20%, #2b1020 0%, transparent 8%),
                  radial-gradient(700px 300px at 90% 80%, #0f1220 0%, transparent 10%),
                  linear-gradient(180deg,#0b0810,#2b1120);
      font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      color: white;
      padding:24px;
    }
    .stage{
      width:100%;max-width:920px;border-radius:18px;padding:20px;background:linear-gradient(180deg, rgba(255,255,255,0.03), rgba(0,0,0,0.16));
      box-shadow: 0 20px 60px rgba(0,0,0,0.6);
      display:grid;grid-template-columns:1fr 320px;gap:18px;
    }

    .left{padding:18px}
    h1{font-size:34px;margin:6px 0 10px;letter-spacing:0.6px}
    .subtitle{color:var(--muted);margin-bottom:18px}

    .controls{display:flex;gap:12px;align-items:center;margin-bottom:18px}
    .btn{background:transparent;border:1px solid rgba(255,255,255,0.08);padding:10px 12px;border-radius:10px;color:var(--muted);cursor:pointer;font-weight:700}
    .btn.active{background:linear-gradient(90deg,var(--accent),#ff9bb0);color:#fff;border:0;box-shadow:0 8px 30px rgba(255,100,140,0.12)}

    .dedications{margin-top:8px}
    .dedication{font-size:20px;margin:10px 0;opacity:0;transform:translateY(8px);transition:all 600ms cubic-bezier(.2,.9,.25,1)}
    .dedication.show{opacity:1;transform:translateY(0)}

    .right{
      padding:18px;border-radius:12px;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(0,0,0,0.14));
      display:flex;flex-direction:column;gap:12px;align-items:center;justify-content:center;
    }
    .preview{
      width:100%;height:220px;border-radius:10px;background:linear-gradient(180deg,#fff1f6, #ffeef6);
      display:flex;flex-direction:column;align-items:center;justify-content:center;color:#521032;font-weight:700;padding:12px;text-align:center;
      box-shadow:0 10px 30px rgba(0,0,0,0.2);
    }
    .heart{font-size:48px;opacity:0;transform:scale(0.8);transition:all 400ms ease}
    .heart.pop{opacity:1;transform:scale(1.08)}
    .small{font-size:13px;color:rgba(0,0,0,0.6)}

    /* typewriter */
    .typewrap{display:inline-block;white-space:nowrap;overflow:hidden;border-right:3px solid rgba(0,0,0,0.12);padding-right:6px}
    .muted{color:rgba(255,255,255,0.85)}

    @media (max-width:880px){
      .stage{grid-template-columns:1fr;gap:14px}
    }
  </style>
</head>
<body>
  <div class="stage" role="main" aria-label="Romantic message">
    <div class="left">
      <h1 id="romantic-title">Feliz Mes, Mi Vida</h1>
      <div class="subtitle">Un pequeño detalle para ti — música, mensaje y más.</div>

      <div class="controls" aria-hidden="false">
        <button id="toggle-audio" class="btn" aria-pressed="false" title="Toggle audio">
          🔊 Música
        </button>

        <button id="toggle-fullscreen" class="btn" title="Toggle fullscreen">
          ⛶ Pantalla completa
        </button>

        <button id="reveal-btn" class="btn" title="Reveal message">
          ✨ Revelar
        </button>
      </div>

      <div style="margin-top:8px">
        <label class="muted" for="name">Nombre:</label>
        <input id="name" placeholder="Ej: Mi Amor" style="width:100%;padding:10px;border-radius:10px;border:1px solid rgba(255,255,255,0.06);margin-top:6px;background:transparent;color:white" />
      </div>

      <div style="margin-top:10px">
        <label class="muted" for="note">Nota:</label>
        <textarea id="note" placeholder="Escribe una nota corta..." rows="4" style="width:100%;padding:10px;border-radius:10px;border:1px solid rgba(255,255,255,0.06);margin-top:6px;background:transparent;color:white"></textarea>
      </div>

      <div style="margin-top:12px;color:var(--muted);font-size:13px">Tip: pulsa <strong>Revelar</strong> para animar el mensaje.</div>
    </div>

    <aside class="right" aria-label="Preview">
      <div class="preview" id="preview">
        <div class="heart" id="heart">❤</div>
        <div style="margin-top:6px;font-size:18px" id="preview-title">Feliz Mes, Mi Vida</div>
        <div style="margin-top:6px" id="preview-sub" class="small">Para ti, con todo mi corazón.</div>
      </div>

      <div style="width:100%;display:flex;gap:8px">
        <button id="download-btn" class="btn" title="Download image">📥 Descargar imagen</button>
        <button id="reset-btn" class="btn" title="Reset">↺ Reset</button>
      </div>

      <div style="font-size:12px;color:rgba(255,255,255,0.6);margin-top:8px">Replace the audio source below with your own file if needed.</div>
      <!-- Provide an audio element. Replace `src` with a hosted audio file or a local file path. -->
      <audio id="bg-audio" loop preload="auto" src=""></audio>
    </aside>
  </div>

  <script>
    // All JS runs after DOMContentLoaded to avoid undefined errors
    window.addEventListener('DOMContentLoaded', () => {
      const toggleAudio = document.getElementById('toggle-audio');
      const toggleFs = document.getElementById('toggle-fullscreen');
      const revealBtn = document.getElementById('reveal-btn');
      const nameInput = document.getElementById('name');
      const noteInput = document.getElementById('note');
      const previewTitle = document.getElementById('preview-title');
      const previewSub = document.getElementById('preview-sub');
      const heart = document.getElementById('heart');
      const bgAudio = document.getElementById('bg-audio');
      const downloadBtn = document.getElementById('download-btn');
      const resetBtn = document.getElementById('reset-btn');

      // If you want a default audio file, put its URL here.
      // Example: bgAudio.src = 'https://example.com/your-music.mp3'
      // For privacy & autoplay rules, browsers may block autoplay; user must interact to play.
      // Leave blank; user can paste a file URL or use a hosted file.
      // bgAudio.src = 'https://...'

      function updatePreview() {
        const name = (nameInput && nameInput.value.trim()) || 'Mi Amor';
        const note = (noteInput && noteInput.value.trim()) || 'Te amo con todo mi corazón.';
        previewTitle.textContent = name;
        previewSub.textContent = note;
      }

      // audio toggle
      let audioOn = false;
      toggleAudio.addEventListener('click', () => {
        if (!bgAudio.src) {
          // friendly hint
          alert('No audio file set. Add an audio URL to the <audio> element or allow the page to use a local file.');
          return;
        }
        if (!audioOn) {
          bgAudio.play().catch(err => {
            // autoplay may be blocked; let user know
            console.warn('Audio play failed:', err);
            alert('El navegador bloqueó la reproducción automática. Pulsa el botón de audio de nuevo para reproducir.');
          });
          toggleAudio.classList.add('active');
          toggleAudio.setAttribute('aria-pressed','true');
        } else {
          bgAudio.pause();
          toggleAudio.classList.remove('active');
          toggleAudio.setAttribute('aria-pressed','false');
        }
        audioOn = !audioOn;
      });

      // fullscreen toggle
      toggleFs.addEventListener('click', async () => {
        try {
          if (!document.fullscreenElement) {
            await document.documentElement.requestFullscreen();
            toggleFs.classList.add('active');
          } else {
            await document.exitFullscreen();
            toggleFs.classList.remove('active');
          }
        } catch (e) {
          console.warn('Fullscreen toggle failed', e);
        }
      });

      // Reveal effect
      revealBtn.addEventListener('click', () => {
        updatePreview();
        // show dedications with simple staged animation
        const ded = document.querySelectorAll('.dedication');
        // if there are any dedications on page, toggle them (here not used)
        // pop the heart
        heart.classList.remove('pop');
        // force reflow to retrigger animation
        void heart.offsetWidth;
        heart.classList.add('pop');

        // brief highlight on preview card
        const previewEl = document.getElementById('preview');
        previewEl.animate([{transform:'scale(1)'},{transform:'scale(1.02)'},{transform:'scale(1)'}], {duration:600, easing:'ease-out'});

        // also type the note text into the preview using a simple typed reveal
        const text = previewSub.textContent;
        previewSub.textContent = '';
        let i=0;
        const interval = setInterval(()=> {
          i++;
          previewSub.textContent = text.slice(0,i);
          if (i>=text.length) clearInterval(interval);
        }, 28);
      });

      // Download preview as image (uses html2canvas if available, fallback: alert)
      downloadBtn.addEventListener('click', async () => {
        // attempt to use html2canvas if present in page; otherwise, create an SVG snapshot
        if (window.html2canvas) {
          const canvas = await html2canvas(document.getElementById('preview'), {backgroundColor: null});
          const url = canvas.toDataURL('image/png');
          const a = document.createElement('a');
          a.href = url; a.download = 'mensaje.png'; a.click();
        } else {
          // simple SVG fallback snapshot (text-only, approximate)
          const title = previewTitle.textContent.replaceAll('&','&amp;');
          const sub = previewSub.textContent.replaceAll('&','&amp;');
          const svg = `<svg xmlns='http://www.w3.org/2000/svg' width='800' height='400'>
            <rect width='100%' height='100%' fill='#fff1f6'/>
            <text x='50%' y='35%' font-size='32' text-anchor='middle' fill='#521032' font-family='Arial'>${title}</text>
            <text x='50%' y='55%' font-size='18' text-anchor='middle' fill='#521032' font-family='Arial'>${sub}</text>
          </svg>`;
          const blob = new Blob([svg], {type:'image/svg+xml'});
          const url = URL.createObjectURL(blob);
          const a = document.createElement('a'); a.href = url; a.download = 'mensaje.svg'; a.click();
        }
      });

      // reset
      resetBtn.addEventListener('click', ()=> {
        nameInput.value = '';
        noteInput.value = '';
        previewTitle.textContent = 'Feliz Mes, Mi Vida';
        previewSub.textContent = 'Para ti, con todo mi corazón.';
        heart.classList.remove('pop');
        if (audioOn && bgAudio) { bgAudio.pause(); audioOn = false; toggleAudio.classList.remove('active'); toggleAudio.setAttribute('aria-pressed','false'); }
      });

      // keyboard shortcuts
      nameInput.addEventListener('keydown', (e)=> { if (e.key === 'Enter') { e.preventDefault(); revealBtn.click(); } });
      noteInput.addEventListener('keydown', (e)=> { if (e.key === 'Enter' && (e.ctrlKey || e.metaKey)) { e.preventDefault(); revealBtn.click(); } });

      // initial preview update
      updatePreview();

      // helpful debug log if elements missing
      const need = {toggleAudio, toggleFs, revealBtn, nameInput, noteInput, previewTitle, previewSub, heart};
      for (const [k,v] of Object.entries(need)) if (!v) console.warn(`Missing element: ${k}`);
    });
  </script>
</body>
</html>
