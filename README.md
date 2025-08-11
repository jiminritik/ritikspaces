<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>RitikSpace</title>
  <style>
    /* ---------- base ---------- */
    :root{
      --bg1:#020617;
      --bg2:#081126;
      --accent:#00eaff;
      --accent-2:#00b8cc;
      --glass: rgba(255,255,255,0.04);
    }
    *{box-sizing:border-box}
    body{
      margin:0;
      font-family: Inter, "Segoe UI", Roboto, system-ui, -apple-system, sans-serif;
      background: radial-gradient(1200px 800px at 10% 10%, #072033 0%, #000611 20%, #000000 60%), var(--bg1);
      color:#e9f1f7;
      overflow-x:hidden;
      -webkit-font-smoothing:antialiased;
      -moz-osx-font-smoothing:grayscale;
    }

    /* ---------- canvas (stars) ---------- */
    canvas#stars{
      position:fixed;
      inset:0;
      z-index:0;
      display:block;
    }

    /* ---------- planet & astronaut ---------- */
    .planet{
      position:fixed;
      left:36px;
      bottom:36px;
      width:160px;
      height:160px;
      border-radius:50%;
      background:
        radial-gradient(circle at 30% 25%, #7fe9ff, #1880b6 30%, #06243a 65%, #01101a 100%);
      box-shadow: 0 0 40px rgba(0,234,255,0.12), inset 0 -12px 40px rgba(0,0,0,0.6);
      transform-origin:center;
      animation:planet-rotate 24s linear infinite;
      z-index:1;
      filter: drop-shadow(0 8px 28px rgba(0,0,0,0.6));
    }
    @keyframes planet-rotate { to { transform: rotate(360deg); } }

    .astronaut{
      position:fixed;
      right:48px;
      top:80px;
      width:110px;
      z-index:1;
      transform-origin:center;
      animation:astronaut-float 4s ease-in-out infinite;
    }
    @keyframes astronaut-float {
      0%,100%{ transform: translateY(0) rotate(-6deg); }
      50%{ transform: translateY(-22px) rotate(6deg); }
    }

    /* ---------- layout ---------- */
    header{
      text-align:center;
      padding:100px 20px 40px;
      position:relative;
      z-index:2;
    }
    header h1{
      margin:0;
      font-size:3rem;
      letter-spacing:0.6px;
      display:inline-block;
      padding:8px 18px;
      border-radius:12px;
      background:linear-gradient(90deg, rgba(255,255,255,0.03), rgba(255,255,255,0.02));
      text-shadow: 0 0 12px rgba(0,234,255,0.08);
      animation:title-glow 2s infinite alternate;
    }
    @keyframes title-glow {
      from { text-shadow: 0 0 8px rgba(0,234,255,0.12); }
      to   { text-shadow: 0 0 20px rgba(0,234,255,0.28); }
    }
    header p{ color:#bcd8e6; margin:10px 0 0; }

    nav{ text-align:center; margin-top:18px; z-index:2; position:relative; }
    nav a{
      color: #dff6ff;
      text-decoration:none;
      margin:0 14px;
      font-weight:600;
      transition: color .18s ease, transform .18s ease;
    }
    nav a:hover{ color:var(--accent); transform: translateY(-2px); }

    main{ max-width:960px; margin:40px auto; padding:0 20px; z-index:2; position:relative; }
    section.card{
      background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
      border-radius:14px;
      padding:28px;
      margin-bottom:28px;
      box-shadow: 0 6px 30px rgba(3,8,20,0.6);
      border:1px solid rgba(255,255,255,0.02);
    }
    .btn{
      display:inline-block;
      background:var(--accent);
      color:#001;
      padding:10px 16px;
      border-radius:999px;
      text-decoration:none;
      font-weight:700;
      box-shadow: 0 6px 18px rgba(0,232,255,0.08);
    }
    .muted{ color:#9fb6c8; font-size:0.95rem; }

    footer{ text-align:center; padding:20px 12px; color:#9fb6c8; z-index:2; pos:relative; }

    /* small screens */
    @media (max-width:640px){
      header{ padding-top:60px; }
      header h1{ font-size:2rem; }
      .planet{ display:none; }
      .astronaut{ display:none; }
    }
  </style>
</head>
<body>
  <!-- star canvas -->
  <canvas id="stars"></canvas>

  <!-- planet (pure CSS) -->
  <div class="planet" aria-hidden="true"></div>

  <!-- embedded astronaut PNG (base64) -->
  <img alt="astronaut" class="astronaut" src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAOEAAADhCAYAAADQ5ztGAAAACXBIWXMAAA7EAAAOxAGVKw4bAAACfUlEQVR4nO3dQW7DIBAF0HZq/9sfymViFFzfc3dnUSZytAZoJvV2cnVvjQAAAAAAAAAAAAAAAAAAAAAAAAD8IXMQZc5fZXPW5u35swMsshmViXcz6/qlmczT3rF7er39VtZvVfrNvYm8/fym7Wf8hf8j9rpzmcvU+8n6HPd7mP9zk9xmV8fn6ZbP6Zz/LyyrpOPdufcPuz/bvvZ75Xf1lV4QvHHYSc54ydlxvEbnHLsw+8rZZH+WY0x1lzWlj5F7T8r7VnbbO0m5MefyF8dfqTrPz1id+6rp6qL5X/Xw90k9uEfzr5Msp/2Ze6/sZ27rD2Tj+xn1H80dizOq6vdaGf9/p68LPaPP1p9V7Hpp78/yzmr4rnqd5n9fyzH+cU1H01Xpl6o9VnVjVfuX5Nlb+btOeVtzV5rJ1m8rRfdq6jq5Pb+9J/O+niIuZp3uF5ZvVeVX1rVbrLuv9irP6M7rbf+e1I8pc1u9j9ZP1PlfdUNV+H9Vqru8d57zXvZnP+K1ncuu/GHc7rmN9M+VX+nZr8ry+42t+4ffaeq7LwMfqeXbnz75p63u/Dj6f5TyeF2r5vm+t5Y9aY/cH+u1pvf1tZZzVYv18L9PVa2f+r3vJ9anKu2Wx7K1+Pnn+kmRdlXUfrMb+tfJrknax/VtM/vU89p+7/Xfs3YOrP37i9y9AAAAAAAAAAAAAAAAAAAAAAAAAH8p/wAATSEEVCKhIgAAAABJRU5ErkJggg==" />

  <!-- content -->
  <header>
    <h1>🚀 RitikSpace</h1>
    <p class="muted">A small corner of the web where I share projects, experiments, and ideas.</p>
    <nav>
      <a href="#about">About</a>
      <a href="#projects">Projects</a>
      <a href="#contact">Contact</a>
    </nav>
  </header>

  <main>
    <section id="about" class="card">
      <h2>About Me</h2>
      <p>Hi — I'm Ritik. I build things on the web, learn new tech, and share experiments here.</p>
      <p class="muted">This is a GitHub Pages-friendly single-file site — just drop this file in the repo root.</p>
    </section>

    <section id="projects" class="card">
      <h2>Projects</h2>
      <p>Links to code, mini-projects, and weird experiments will appear here soon.</p>
      <a class="btn" href="#contact">Contact / Hire</a>
    </section>

    <section id="contact" class="card">
      <h2>Contact</h2>
      <p>Email: <a href="mailto:ritik@example.com" style="color:var(--accent)">@ritik</a></p>
      <p class="muted">Replace the email with your real address in the file before publishing.</p>
    </section>
  </main>

  <footer>
    &copy; <span id="year"></span> RitikSpace — made with 🔭
  </footer>

  <script>
    // set copyright year
    document.getElementById('year').textContent = new Date().getFullYear();

    // ----- STARFIELD -----
    const canvas = document.getElementById('stars');
    const ctx = canvas.getContext('2d');
    let W = canvas.width = innerWidth;
    let H = canvas.height = innerHeight;
    window.addEventListener('resize',()=>{ W = canvas.width = innerWidth; H = canvas.height = innerHeight; initStars(); });

    let stars = [];
    const NUM = Math.round((innerWidth+innerHeight)/8);

    function rand(min,max){ return Math.random()*(max-min)+min; }

    function initStars(){
      stars = [];
      for(let i=0;i<NUM;i++){
        stars.push({
          x: Math.random()*W,
          y: Math.random()*H,
          r: Math.random()*1.4 + 0.2,
          s: Math.random()*0.6 + 0.2,
          flick: Math.random()*0.8
        });
      }
    }
    initStars();

    function draw(){
      ctx.clearRect(0,0,W,H);
      for(let s of stars){
        ctx.beginPath();
        ctx.globalAlpha = 0.8 + Math.sin((Date.now()/1000)*s.flick)*0.2;
        ctx.fillStyle = 'white';
        ctx.arc(s.x,s.y,s.r,0,Math.PI*2);
        ctx.fill();
      }
    }
    function update(){
      for(let s of stars){
        s.y += s.s;
        if(s.y > H){ s.y = 0; s.x = Math.random()*W; }
      }
    }
    (function loop(){ draw(); update(); requestAnimationFrame(loop); })();
  </script>
</body>
</html>
