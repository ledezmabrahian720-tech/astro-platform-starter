<!doctype html>

<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Galaxia: Mi mujer Paola</title>
  <style>
    :root{--bg:#03030b;--text:#e8eef8}
    html,body{height:100%;margin:0;background:radial-gradient(ellipse at 20% 10%, #081127 0%, var(--bg) 60%);font-family:Inter, system-ui, sans-serif;color:var(--text)}
    .wrap{display:flex;flex-direction:column;align-items:center;justify-content:center;height:100%;gap:16px;padding:20px}
    h1{margin:0;font-size:24px;text-align:center}
    p{margin:0;opacity:.85}
    canvas{border-radius:12px;box-shadow:0 10px 30px rgba(0,0,0,.6);background:transparent}
    .controls{display:flex;gap:10px;align-items:center}
    .btn{background:#0b1220;color:var(--text);padding:8px 12px;border-radius:8px;border:1px solid rgba(255,255,255,.06);cursor:pointer}
    .legend{display:flex;gap:8px;flex-wrap:wrap;max-width:900px;justify-content:center}
    .planet-tag{background:rgba(255,255,255,.04);padding:6px 10px;border-radius:999px;border:1px solid rgba(255,255,255,.03)}
    .credit{position:fixed;left:12px;bottom:12px;font-size:12px;opacity:.7}
  </style>
</head>
<body>
  <div class="wrap">
    <h1>Galaxia — <strong>Mi mujer Paola</strong></h1>
    <p>Haz clic en un planeta para ver una nota bonita ✨</p><canvas id="galaxy" width="1000" height="640"></canvas>

<div class="controls">
  <button id="pause" class="btn">Pausar movimiento</button>
  <button id="reset" class="btn">Reiniciar</button>
</div>

<div class="legend" id="legend"></div>

  </div>  <div class="credit">Generado para Paola — puedes subir este archivo a GitHub como README o página web</div>  <script>
    // --- Datos de la "galaxia" ---
    const centerPlanet = { name: 'Mi mujer Paola', color1: '#ff9a9e', color2: '#fad0c4', size: 70, note: 'El corazón de esta galaxia. Siempre brillante.' };
    const planets = [
      { name: 'Ojos', size: 18, dist: 140, speed: 0.009, color1:'#7ee8fa', color2:'#acb6e5', note: 'Ventanas al mundo; reflejan ternura y fuerza.' },
      { name: 'Sonrisa', size: 22, dist: 200, speed: 0.007, color1:'#ffd89b', color2:'#19547b', note: 'Ilumina cualquier día, pura calidez.' },
      { name: 'Labios', size: 16, dist: 260, speed: 0.01, color1:'#ff758c', color2:'#ff7eb3', note: 'Dulces y sinceros; prometen ternura.' },
      { name: 'Cabello', size: 20, dist: 320, speed: 0.006, color1:'#a18cd1', color2:'#fbc2eb', note: 'Cascada que juega con el viento.' },
      { name: 'Manos', size: 15, dist: 380, speed: 0.005, color1:'#cfd9df', color2:'#e2ebf0', note: 'Caricias que curan y sostienen.' },
      { name: 'Risa', size: 14, dist: 440, speed: 0.008, color1:'#f6d365', color2:'#fda085', note: 'Sonido que contagia alegría.' }
    ];

    // --- Canvas setup ---
    const canvas = document.getElementById('galaxy');
    const ctx = canvas.getContext('2d');
    let W = canvas.width, H = canvas.height;
    const cx = W/2, cy = H/2;

    // stars
    const stars = [];
    for(let i=0;i<180;i++){ stars.push({x:Math.random()*W,y:Math.random()*H,r:Math.random()*1.4,alpha:0.2+Math.random()*0.9}); }

    // orbit states
    planets.forEach(p=>{ p.angle = Math.random()*Math.PI*2; p.selected=false; });

    let running = true;
    document.getElementById('pause').onclick = ()=>{ running = !running; document.getElementById('pause').textContent = running ? 'Pausar movimiento' : 'Reanudar'; };
    document.getElementById('reset').onclick = ()=>{ planets.forEach(p=>p.angle=Math.random()*Math.PI*2); };

    // legend
    const legend = document.getElementById('legend');
    function buildLegend(){
      legend.innerHTML = '';
      const centerTag = document.createElement('div'); centerTag.className='planet-tag'; centerTag.textContent = centerPlanet.name; legend.appendChild(centerTag);
      planets.forEach(p=>{ const tag=document.createElement('div'); tag.className='planet-tag'; tag.textContent = p.name; legend.appendChild(tag); });
    }
    buildLegend();

    // responsive
    function resize(){
      const ratio = Math.min(window.innerWidth-60,1200);
      canvas.width = ratio; canvas.height = Math.round(ratio*0.64);
      W = canvas.width; H = canvas.height;
    }
    window.addEventListener('resize', resize); resize();

    // draw helpers
    function drawStar(s){ ctx.beginPath(); ctx.globalAlpha = s.alpha; ctx.arc(s.x, s.y, s.r, 0, Math.PI*2); ctx.fillStyle = 'white'; ctx.fill(); ctx.globalAlpha = 1; }
    function radialCircle(x,y,r,c1,c2){ const g=ctx.createRadialGradient(x,y,r*0.1,x,y,r); g.addColorStop(0,c1); g.addColorStop(1,c2); ctx.fillStyle=g; ctx.beginPath(); ctx.arc(x,y,r,0,Math.PI*2); ctx.fill(); }

    // main draw loop
    function draw(ts){
      ctx.clearRect(0,0,W,H);

      // subtle background nebula
      const ng = ctx.createLinearGradient(0,0,W,H); ng.addColorStop(0,'rgba(255,160,200,0.02)'); ng.addColorStop(1,'rgba(120,160,255,0.02)'); ctx.fillStyle=ng; ctx.fillRect(0,0,W,H);

      // stars
      stars.forEach(s=>drawStar(s));

      // center planet glow
      const centerX = W/2, centerY = H/2;
      for(let i=0;i<3;i++){ radialCircle(centerX,centerY, centerPlanet.size + 30 + i*18, centerPlanet.color1, 'rgba(0,0,0,0)'); }
      radialCircle(centerX,centerY, centerPlanet.size, centerPlanet.color1, centerPlanet.color2);

      // label center
      ctx.font = 'bold 18px system-ui'; ctx.textAlign='center'; ctx.fillStyle='rgba(255,255,255,0.95)'; ctx.fillText(centerPlanet.name, centerX, centerY + centerPlanet.size + 26);

      // orbits and planets
      planets.forEach((p,i)=>{
        // orbit path
        ctx.beginPath(); ctx.setLineDash([4,6]); ctx.strokeStyle = 'rgba(255,255,255,0.06)'; ctx.lineWidth=1; ctx.arc(centerX,centerY,p.dist,0,Math.PI*2); ctx.stroke(); ctx.setLineDash([]);

        // update angle
        if(running) p.angle += p.speed * (1 + i*0.02);
        const x = centerX + Math.cos(p.angle) * p.dist;
        const y = centerY + Math.sin(p.angle) * p.dist;

        // planet body
        radialCircle(x,y,p.size,p.color1,p.color2);

        // label
        ctx.font='12px system-ui'; ctx.textAlign='center'; ctx.fillStyle='rgba(255,255,255,0.9)'; ctx.fillText(p.name, x, y + p.size + 14);

        // store screen pos for interaction
        p.screenX = x; p.screenY = y;
      });

      requestAnimationFrame(draw);
    }
    requestAnimationFrame(draw);

    // click interaction
    canvas.addEventListener('click', (e)=>{
      const rect = canvas.getBoundingClientRect(); const mx = e.clientX - rect.left; const my = e.clientY - rect.top;
      // check planets
      for(const p of planets){ const dx = mx - p.screenX, dy = my - p.screenY; if(Math.sqrt(dx*dx+dy*dy) <= p.size+6){ showNote(p); return; } }
      // center
      const dx = mx - canvas.width/2, dy = my - canvas.height/2; if(Math.sqrt(dx*dx+dy*dy) <= centerPlanet.size+10){ showNote(centerPlanet); }
    });

    function showNote(planet){
      // little floating tooltip
      const tooltip = document.createElement('div');
      tooltip.style.position='fixed'; tooltip.style.left='50%'; tooltip.style.top='16%'; tooltip.style.transform='translateX(-50%)';
      tooltip.style.background='linear-gradient(180deg, rgba(255,255,255,0.06), rgba(0,0,0,0.4))'; tooltip.style.backdropFilter='blur(6px)'; tooltip.style.padding='14px 18px'; tooltip.style.borderRadius='12px'; tooltip.style.border='1px solid rgba(255,255,255,0.06)'; tooltip.style.boxShadow='0 6px 24px rgba(0,0,0,0.6)'; tooltip.style.color='#fff'; tooltip.style.zIndex=9999;
      tooltip.innerHTML = `<strong>${planet.name}</strong><div style="margin-top:6px;font-size:13px;opacity:.95">${planet.note || 'Un lugar especial.'}</div>`;
      document.body.appendChild(tooltip);
      setTimeout(()=>{ tooltip.style.transition='opacity .4s'; tooltip.style.opacity=0; setTimeout(()=>tooltip.remove(),400); }, 3000);
    }

    // keyboard: press H to toggle hints
    window.addEventListener('keydown', (e)=>{ if(e.key.toLowerCase()==='h'){ alert('Haz clic en un planeta para leer una nota bonita. Usa los botones para pausar o reiniciar.'); } });

  </script></body>
</html>