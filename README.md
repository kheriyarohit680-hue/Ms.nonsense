<!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no" />
    <title>Non-sense🤧</title>
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link href="https://fonts.googleapis.com/css2?family=Pinyon+Script&family=Playfair+Display:ital@1&family=Great+Vibes&display=swap" rel="stylesheet" />
    <style>
      /* ============================================================
         RESET & BASE
      ============================================================ */
      *, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }
      html, body { width: 100%; height: 100%; overflow: hidden; background: #000; }

      /* ============================================================
         CAT PAW CURSOR
      ============================================================ */
      body {
        cursor: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='36' height='36' viewBox='0 0 36 36'%3E%3Cellipse cx='18' cy='23' rx='9' ry='8' fill='%23f4a7b9'/%3E%3Cellipse cx='18' cy='23' rx='5.5' ry='5' fill='%23e8829a'/%3E%3Cellipse cx='9' cy='15' rx='4' ry='3.5' fill='%23f4a7b9'/%3E%3Cellipse cx='18' cy='12' rx='4' ry='3.5' fill='%23f4a7b9'/%3E%3Cellipse cx='27' cy='15' rx='4' ry='3.5' fill='%23f4a7b9'/%3E%3Cellipse cx='9' cy='15' rx='2.5' ry='2' fill='%23e8829a'/%3E%3Cellipse cx='18' cy='12' rx='2.5' ry='2' fill='%23e8829a'/%3E%3Cellipse cx='27' cy='15' rx='2.5' ry='2' fill='%23e8829a'/%3E%3C/svg%3E") 18 18, auto;
      }

      /* ============================================================
         FLOWER STAGE
      ============================================================ */
      #stage { position: fixed; inset: 0; pointer-events: none; z-index: 1; }

      /* ============================================================
         FLOWER — bloom in, then orbit in place via JS
      ============================================================ */
      .flower {
        position: absolute;
        transform: translate(-50%, -50%) scale(0) rotate(-20deg);
        opacity: 0;
        pointer-events: none;
        filter: drop-shadow(0 0 10px rgba(255,150,190,.7))
                drop-shadow(0 0 26px rgba(255,100,160,.4));
        animation: bloom .65s cubic-bezier(.34,1.56,.64,1) forwards;
      }
      @keyframes bloom {
        0%   { transform: translate(-50%,-50%) scale(0) rotate(-25deg); opacity: 0; }
        60%  { opacity: 1; }
        100% { transform: translate(-50%,-50%) scale(1) rotate(0deg); opacity: .92; }
      }
      @keyframes fallSpiral {
        0%   { opacity: .92; }
        100% {
          transform: translate(calc(-50% + var(--drift,0px)), calc(-50% + 140vh))
                     scale(.18) rotate(var(--spin,540deg));
          opacity: 0;
        }
      }
      .flower.falling {
        animation: fallSpiral 2.5s cubic-bezier(.38,0,.74,1) forwards !important;
        transition: none !important;
      }

      /* ============================================================
         BLACKOUT OVERLAY
      ============================================================ */
      #blackout {
        position: fixed; inset: 0; background: #000;
        z-index: 300; opacity: 0; pointer-events: none;
        transition: opacity 1.1s ease;
      }
      #blackout.on { opacity: 1; pointer-events: all; }

      /* ============================================================
         GLASS POPUP — shared base
      ============================================================ */
      .glass-popup {
        position: fixed; left: 50%; transform: translateX(-50%);
        z-index: 120; pointer-events: none;
        opacity: 0; transition: opacity 1s ease;
      }
      .glass-popup.visible { opacity: 1; }

      .glass-popup .box {
        font-family: 'Great Vibes', 'Pinyon Script', cursive;
        letter-spacing: .06em;
        line-height: 1.6;
        text-align: center;
        padding: 1.2rem 2.4rem;
        border-radius: 2rem;
        background: rgba(0,0,0,.4);
        backdrop-filter: blur(14px);
        -webkit-backdrop-filter: blur(14px);
        border: 1px solid rgba(255,170,210,.3);
        box-shadow: 0 0 36px rgba(255,120,180,.2),
                    inset 0 0 24px rgba(255,190,220,.07);
      }

      /* HINT popup */
      #hint-popup { top: 50%; transform: translate(-50%, -50%); }
      #hint-popup .box {
        font-size: clamp(1.4rem, 5vw, 2.2rem);
        color: #ffd0e8;
        text-shadow: 0 0 16px rgba(255,120,180,1),
                     0 0 40px rgba(255,90,160,.6),
                     0 0 80px rgba(255,60,140,.3);
      }

      /* MORNING popup */
      #morning-popup { top: 6%; }
      #morning-popup .box {
        font-size: clamp(1.8rem, 6vw, 3rem);
        color: #ffe0f0;
        text-shadow: 0 0 18px rgba(255,130,185,1),
                     0 0 50px rgba(255,90,160,.65),
                     0 0 100px rgba(255,60,140,.35);
      }

      /* ============================================================
         CAT ASSEMBLY — enters from top, exits back up
      ============================================================ */
      #cat-assembly {
        position: fixed;
        top: -700px;
        left: 50%;
        /* float animation overrides transform when .arrive is set */
        transform: translateX(-50%);
        z-index: 110;
        pointer-events: none;
        width: min(280px, 62vw);
        transition: top 2.2s cubic-bezier(.22,.8,.36,1);
      }
      #cat-assembly.arrive { top: 6vh; }
      #cat-assembly.leave  { top: -700px; transition: top 1.8s cubic-bezier(.6,0,.9,.4); }

      /* float bob */
      @keyframes catFloat {
        0%,100% { margin-top: 0; }
        50%      { margin-top: -14px; }
      }
      #cat-assembly.arrive { animation: catFloat 2.4s ease-in-out infinite; top: 6vh; }

      #cat-img {
        width: 100%;
        display: block;
        /* transparent PNG — no extra background treatment needed */
        filter: drop-shadow(0 12px 36px rgba(255,140,200,.45));
      }
    </style>
  </head>
  <body>

    <div id="stage"></div>
    <div id="blackout"></div>

    <!-- Idle / hint popup -->
    <div class="glass-popup" id="hint-popup">
      <div class="box" id="hint-text">Click anywhere madam 🌸</div>
    </div>

    <!-- Good morning popup -->
    <div class="glass-popup" id="morning-popup">
      <div class="box">Good morning Sunaina 💗</div>
    </div>

    <!-- Cat with balloons descending from top -->
    <div id="cat-assembly">
      <img id="cat-img" src=""/>
    </div>

    <script>
      /* ============================================================
         LILY SVG DATA URI
      ============================================================ */
      const LILY_SVG = `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 120 120">
        <defs>
          <radialGradient id="pg" cx="50%" cy="70%" r="70%">
            <stop offset="0%"   stop-color="#fff0f5"/>
            <stop offset="35%"  stop-color="#ffb3cf"/>
            <stop offset="75%"  stop-color="#e8538a"/>
            <stop offset="100%" stop-color="#c0265c"/>
          </radialGradient>
          <radialGradient id="sg" cx="50%" cy="50%" r="50%">
            <stop offset="0%"  stop-color="#fff5a0"/>
            <stop offset="100%" stop-color="#f0a020"/>
          </radialGradient>
        </defs>
        <g transform="translate(60,60)">
          <ellipse cx="0" cy="-28" rx="14" ry="30" fill="url(#pg)" opacity=".95"/>
          <ellipse cx="0" cy="-28" rx="14" ry="30" fill="url(#pg)" opacity=".95" transform="rotate(72)"/>
          <ellipse cx="0" cy="-28" rx="14" ry="30" fill="url(#pg)" opacity=".95" transform="rotate(144)"/>
          <ellipse cx="0" cy="-28" rx="14" ry="30" fill="url(#pg)" opacity=".95" transform="rotate(216)"/>
          <ellipse cx="0" cy="-28" rx="14" ry="30" fill="url(#pg)" opacity=".95" transform="rotate(288)"/>
          <ellipse cx="0" cy="-20" rx="8"  ry="20" fill="#ffe0ef" opacity=".5"/>
          <ellipse cx="0" cy="-20" rx="8"  ry="20" fill="#ffe0ef" opacity=".5" transform="rotate(72)"/>
          <ellipse cx="0" cy="-20" rx="8"  ry="20" fill="#ffe0ef" opacity=".5" transform="rotate(144)"/>
          <ellipse cx="0" cy="-20" rx="8"  ry="20" fill="#ffe0ef" opacity=".5" transform="rotate(216)"/>
          <ellipse cx="0" cy="-20" rx="8"  ry="20" fill="#ffe0ef" opacity=".5" transform="rotate(288)"/>
        </g>
        <circle cx="60" cy="60" r="9"   fill="url(#sg)" opacity=".95"/>
        <circle cx="60" cy="60" r="4.5" fill="#fff8c0"/>
        <g transform="translate(60,60)" opacity=".8">
          <circle cx="0" cy="-13" r="2.2" fill="#f0a020"/>
          <circle cx="0" cy="-13" r="2.2" fill="#f0a020" transform="rotate(72)"/>
          <circle cx="0" cy="-13" r="2.2" fill="#f0a020" transform="rotate(144)"/>
          <circle cx="0" cy="-13" r="2.2" fill="#f0a020" transform="rotate(216)"/>
          <circle cx="0" cy="-13" r="2.2" fill="#f0a020" transform="rotate(288)"/>
        </g>
      </svg>`;
      const LILY_URI = 'data:image/svg+xml;charset=utf-8,' + encodeURIComponent(LILY_SVG);

      /* ============================================================
         DOM
      ============================================================ */
      const stage        = document.getElementById('stage');
      const blackout     = document.getElementById('blackout');
      const hintPopup    = document.getElementById('hint-popup');
      const hintText     = document.getElementById('hint-text');
      const morningPopup = document.getElementById('morning-popup');
      const catAssembly  = document.getElementById('cat-assembly');

      /* ============================================================
         STATE
      ============================================================ */
      let clickCount = 0;
      let idleTimer  = null;
      let phase      = 'normal';  // 'normal' | 'sequence' | 'resetting'
      const CLICK_GOAL = 30;
      const IDLE_MS    = 3000;    // 3 seconds idle → show hint

      /* orbit RAF */
      let orbitRAF  = null;
      let orbitData = new Map(); // flower → { origX, origY, angle, orbitR, speed, dir }

      /* ============================================================
         MEOW  — synthesised with Web Audio API
      ============================================================ */
      let audioCtx = null;
      function getACtx() {
        if (!audioCtx) {
          const AC = window.AudioContext || window.webkitAudioContext;
          if (AC) audioCtx = new AC();
        }
        return audioCtx;
      }
      function playMeow() {
        const ctx = getACtx();
        if (!ctx) return;
        if (ctx.state === 'suspended') ctx.resume();
        const osc  = ctx.createOscillator();
        const gain = ctx.createGain();
        const flt  = ctx.createBiquadFilter();
        osc.connect(flt); flt.connect(gain); gain.connect(ctx.destination);
        flt.type = 'bandpass'; flt.frequency.value = 1100; flt.Q.value = 6;
        osc.type = 'sine';
        const t = ctx.currentTime;
        osc.frequency.setValueAtTime(520, t);
        osc.frequency.linearRampToValueAtTime(840, t + .07);
        osc.frequency.exponentialRampToValueAtTime(560, t + .22);
        osc.frequency.exponentialRampToValueAtTime(320, t + .46);
        gain.gain.setValueAtTime(0, t);
        gain.gain.linearRampToValueAtTime(.18, t + .06);
        gain.gain.setValueAtTime(.16, t + .20);
        gain.gain.exponentialRampToValueAtTime(.001, t + .52);
        osc.start(t); osc.stop(t + .54);
      }

      /* ============================================================
         IDLE TIMER  — 3 seconds
      ============================================================ */
      function resetIdleTimer() {
        clearTimeout(idleTimer);
        if (phase !== 'normal') return;
        idleTimer = setTimeout(() => {
          if (phase !== 'normal') return;
          hintText.textContent = clickCount === 0
            ? 'Click anywhere madam 🌸'
            : "That's not enough 😼";
          hintPopup.classList.add('visible');
        }, IDLE_MS);
      }
      function dismissHint() { hintPopup.classList.remove('visible'); }

      /* ============================================================
         SPAWN FLOWER
      ============================================================ */
      function spawnFlower(x, y) {
        const size = 60 + Math.random() * 72;
        const tilt = (Math.random() - 0.5) * 32;
        const img  = document.createElement('img');
        img.src = LILY_URI;
        img.className = 'flower';
        img.width = size; img.height = size; img.alt = '';
        img.style.left = x + 'px';
        img.style.top  = y + 'px';
        img.dataset.origX = x;
        img.dataset.origY = y;
        img.dataset.tilt  = tilt;
        img.style.animation = 'none';
        void img.offsetWidth;
        img.style.animation = 'bloom .65s cubic-bezier(.34,1.56,.64,1) forwards';
        img.addEventListener('animationend', () => {
          img.style.transform = `translate(-50%,-50%) scale(1) rotate(${tilt}deg)`;
          img.style.opacity   = '.92';
        }, { once: true });
        stage.appendChild(img);
      }

      /* ============================================================
         ORBIT IN PLACE
         Each lily circles a small orbit around its own click point
      ============================================================ */
      function startOrbitInPlace() {
        const flowers = Array.from(document.querySelectorAll('.flower'));
        orbitData.clear();

        flowers.forEach(flower => {
          const origX  = parseFloat(flower.style.left);
          const origY  = parseFloat(flower.style.top);
          const orbitR = 22 + Math.random() * 22;           // 22–44 px orbit radius
          const angle  = Math.random() * Math.PI * 2;       // random start angle
          const speed  = 0.035 + Math.random() * 0.035;     // radians/frame
          const dir    = Math.random() > .5 ? 1 : -1;       // CW or CCW

          /* freeze existing bloom animation so JS can drive position */
          flower.style.animation = 'none';
          flower.style.opacity   = '.92';

          orbitData.set(flower, { origX, origY, orbitR, angle, speed, dir });
        });

        function frame() {
          orbitData.forEach((d, flower) => {
            d.angle += d.speed * d.dir;
            const nx = d.origX + Math.cos(d.angle) * d.orbitR;
            const ny = d.origY + Math.sin(d.angle) * d.orbitR;
            flower.style.left      = nx + 'px';
            flower.style.top       = ny + 'px';
            flower.style.transform = `translate(-50%,-50%) scale(1) rotate(${d.angle * 57.3}deg)`;
          });
          orbitRAF = requestAnimationFrame(frame);
        }
        orbitRAF = requestAnimationFrame(frame);
      }

      function stopOrbit() {
        if (orbitRAF) { cancelAnimationFrame(orbitRAF); orbitRAF = null; }
      }

      /* ============================================================
         FALL ALL LILIES
      ============================================================ */
      function fallAllLilies() {
        stopOrbit();
        const flowers = Array.from(document.querySelectorAll('.flower'));
        flowers.forEach((flower, i) => {
          const drift = (Math.random() - 0.5) * 500;
          const spin  = ((360 + Math.random() * 400) * (Math.random() > .5 ? 1 : -1)) + 'deg';
          flower.style.setProperty('--drift', drift + 'px');
          flower.style.setProperty('--spin',  spin);
          flower.style.animationDelay = (i * 55) + 'ms';
          flower.classList.add('falling');
        });
        return (flowers.length - 1) * 55 + 2500 + 200;
      }

      /* ============================================================
         MORNING SEQUENCE  — triggered at 30 clicks
         Timeline (all relative to sequence start):
           0.0 s — lilies begin orbiting in place
           0.6 s — cat slides down from top
           1.8 s — "Good morning" popup fades in
           8.6 s — popup fades out, cat begins to leave
          10.4 s — cat fully exited; lilies start falling
          10.4+fallTime — blackout → reset
      ============================================================ */
      function startSequence() {
        phase = 'sequence';
        clearTimeout(idleTimer);

        /* lilies orbit in place immediately */
        startOrbitInPlace();

        /* cat descends */
        setTimeout(() => catAssembly.classList.add('arrive'), 600);

        /* good morning popup */
        setTimeout(() => morningPopup.classList.add('visible'), 1800);

        /* cat stays 7 seconds after arriving (0.6 + 7000 = 7600) then leaves */
        const CAT_LEAVE = 600 + 7000;
        setTimeout(() => {
          morningPopup.classList.remove('visible');
          catAssembly.classList.remove('arrive');
          catAssembly.classList.add('leave');
        }, CAT_LEAVE);

        /* after cat exits (~1.8 s transition) start fall */
        const FALL_START = CAT_LEAVE + 1900;
        setTimeout(() => {
          catAssembly.classList.remove('leave');
          const fallTime = fallAllLilies();

          /* blackout after last lily falls */
          setTimeout(() => {
            blackout.classList.add('on');

            /* reset under the blackout */
            setTimeout(() => {
              document.querySelectorAll('.flower').forEach(f => f.remove());
              orbitData.clear();
              clickCount = 0;
              phase = 'normal';
              blackout.classList.remove('on');
              resetIdleTimer();
            }, 1200);
          }, fallTime);
        }, FALL_START);
      }

      /* ============================================================
         INTERACTION HANDLER
      ============================================================ */
      function handleInteraction(x, y) {
        if (phase !== 'normal') return;
        dismissHint();
        resetIdleTimer();
        playMeow();
        clickCount++;
        spawnFlower(x, y);
        if (clickCount >= CLICK_GOAL) startSequence();
      }

      /* ============================================================
         EVENTS
      ============================================================ */
      document.addEventListener('click', e => handleInteraction(e.clientX, e.clientY));
      document.addEventListener('touchstart', e => {
        e.preventDefault();
        const t = e.changedTouches[0];
        handleInteraction(t.clientX, t.clientY);
      }, { passive: false });

      /* ============================================================
         BOOT
      ============================================================ */
      resetIdleTimer();
    </script>
  </body>
  </html>
