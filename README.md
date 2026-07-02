<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Ajay R — AI Engineer</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;600;700&family=Manrope:wght@400;500;600;700;800&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<style>
  :root{
    --ink:#08090c;
    --panel:#10131a;
    --panel-2:#151a23;
    --line:#242b38;
    --text:#e9edf3;
    --muted:#8a94a8;
    --scan:#5fe3a3;
    --scan-dim:#5fe3a333;
    --amber:#ffb454;
    --blue:#5fc8ff;
    --mono:'JetBrains Mono', monospace;
    --sans:'Manrope', sans-serif;
  }
  *{box-sizing:border-box; margin:0; padding:0;}
  html{scroll-behavior:smooth;}
  body{
    background:var(--ink);
    color:var(--text);
    font-family:var(--sans);
    overflow-x:hidden;
  }
  ::selection{ background:var(--scan); color:#08090c; }
  a{ color:inherit; text-decoration:none; }

  .noise{
    position:fixed; inset:0; pointer-events:none; z-index:999;
    opacity:.035; mix-blend-mode:overlay;
    background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='2' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
  }

  /* ---------- TOP BAR ---------- */
  .topbar{
    position:fixed; top:0; left:0; right:0; z-index:100;
    display:flex; align-items:center; justify-content:space-between;
    padding:16px 28px;
    font-family:var(--mono); font-size:12px; letter-spacing:.06em;
    color:var(--muted);
    background:linear-gradient(to bottom, rgba(8,9,12,.85), transparent);
    backdrop-filter:blur(2px);
  }
  .topbar .brand{ color:var(--text); font-weight:600; }
  .topbar .status{ display:flex; align-items:center; gap:8px; }
  .dot{ width:7px; height:7px; border-radius:50%; background:var(--scan); box-shadow:0 0 8px var(--scan); animation:pulse 1.8s infinite; }
  @keyframes pulse{ 0%,100%{opacity:1;} 50%{opacity:.25;} }
  .topbar nav{ display:flex; gap:22px; }
  .topbar nav a:hover{ color:var(--scan); }

  /* ---------- HERO / VIEWFINDER ---------- */
  .viewfinder{
    position:relative;
    height:100vh; min-height:640px;
    display:flex; align-items:center; justify-content:center;
    overflow:hidden;
    border-bottom:1px solid var(--line);
  }
  #scene-canvas{ position:absolute; inset:0; z-index:0; }
  .vf-vignette{
    position:absolute; inset:0; z-index:1; pointer-events:none;
    background:radial-gradient(ellipse at center, transparent 35%, var(--ink) 92%);
  }

  .bracket{ position:absolute; width:38px; height:38px; z-index:3; opacity:.8; }
  .bracket svg{ width:100%; height:100%; }
  .bracket.tl{ top:26px; left:26px; }
  .bracket.tr{ top:26px; right:26px; transform:rotate(90deg); }
  .bracket.bl{ bottom:26px; left:26px; transform:rotate(-90deg); }
  .bracket.br{ bottom:26px; right:26px; transform:rotate(180deg); }

  .scanline{
    position:absolute; left:0; right:0; height:1px; z-index:2;
    background:linear-gradient(90deg, transparent, var(--scan), transparent);
    box-shadow:0 0 12px 1px var(--scan);
    animation:sweep 5s linear infinite;
    opacity:.55;
  }
  @keyframes sweep{ 0%{ top:8%; } 100%{ top:92%; } }

  .rec{
    position:absolute; top:26px; left:50%; transform:translateX(-50%);
    font-family:var(--mono); font-size:11px; letter-spacing:.1em; color:var(--muted);
    display:flex; align-items:center; gap:8px; z-index:3;
  }
  .rec .dot{ background:#ff5f5f; box-shadow:0 0 8px #ff5f5f; }
  #clock{ color:var(--text); }

  .hero-content{
    position:relative; z-index:3; text-align:center;
    padding:0 20px;
  }
  .eyebrow{
    font-family:var(--mono); font-size:12px; letter-spacing:.2em;
    color:var(--scan); text-transform:uppercase; margin-bottom:18px;
    opacity:0; animation:rise .7s .2s ease forwards;
  }
  h1.hero-name{
    font-size:clamp(48px,9vw,108px); font-weight:800; line-height:.95;
    letter-spacing:-.02em;
    background:linear-gradient(180deg,#fff, #b9c3d4 65%, #7f8aa0);
    -webkit-background-clip:text; background-clip:text; color:transparent;
    opacity:0; animation:rise .8s .35s ease forwards;
  }
  .hero-role{
    margin-top:14px; font-family:var(--mono); font-size:clamp(13px,1.6vw,17px);
    color:var(--muted); letter-spacing:.03em;
    opacity:0; animation:rise .8s .5s ease forwards;
  }
  @keyframes rise{ from{ opacity:0; transform:translateY(16px);} to{ opacity:1; transform:translateY(0);} }

  .tag-cloud{ position:absolute; inset:0; z-index:3; pointer-events:none; }
  .det-tag{
    position:absolute; font-family:var(--mono); font-size:11px;
    padding:5px 9px; border:1px solid var(--scan-dim);
    background:rgba(16,19,26,.55); backdrop-filter:blur(3px);
    color:var(--scan); border-radius:2px; white-space:nowrap;
    opacity:0; animation:tagIn .6s ease forwards;
  }
  .det-tag span{ color:var(--muted); margin-left:6px; }
  @keyframes tagIn{ from{opacity:0; transform:scale(.85);} to{opacity:1; transform:scale(1);} }

  .scroll-cue{
    position:absolute; bottom:34px; left:50%; transform:translateX(-50%);
    z-index:3; font-family:var(--mono); font-size:10px; letter-spacing:.15em;
    color:var(--muted); display:flex; flex-direction:column; align-items:center; gap:8px;
  }
  .scroll-cue .line{ width:1px; height:26px; background:linear-gradient(var(--muted), transparent); animation:cueMove 1.6s infinite; }
  @keyframes cueMove{ 0%{opacity:.2;} 50%{opacity:1;} 100%{opacity:.2;} }

  /* ---------- SECTION SHELL ---------- */
  section{ position:relative; padding:110px 8vw; max-width:1280px; margin:0 auto; }
  .section-head{ display:flex; align-items:baseline; gap:16px; margin-bottom:44px; }
  .section-idx{ font-family:var(--mono); font-size:12px; color:var(--scan); }
  .section-title{ font-size:clamp(26px,3vw,38px); font-weight:800; letter-spacing:-.01em; }
  .section-sub{ color:var(--muted); font-size:14px; margin-top:10px; max-width:560px; font-family:var(--mono); }

  /* ---------- ABOUT ---------- */
  .about-grid{ display:grid; grid-template-columns:1.1fr .9fr; gap:48px; align-items:start; }
  .about-copy{ font-size:16px; line-height:1.75; color:#c7cede; }
  .about-copy strong{ color:var(--text); }
  .code-panel{
    background:var(--panel); border:1px solid var(--line); border-radius:8px;
    overflow:hidden;
  }
  .code-panel .bar{
    display:flex; gap:6px; padding:11px 14px; border-bottom:1px solid var(--line);
    background:var(--panel-2);
  }
  .code-panel .bar i{ width:9px; height:9px; border-radius:50%; background:#3a4152; }
  .code-panel pre{
    padding:20px; font-family:var(--mono); font-size:12.5px; line-height:1.75;
    color:#c7cede; overflow-x:auto;
  }
  .code-panel .k{ color:var(--blue); }
  .code-panel .s{ color:var(--amber); }
  .code-panel .c{ color:var(--muted); }
  .code-panel .f{ color:var(--scan); }

  @media(max-width:860px){ .about-grid{ grid-template-columns:1fr; } }

  /* ---------- DASHBOARD ---------- */
  .dash-grid{ display:grid; grid-template-columns:repeat(3,1fr); gap:16px; }
  .dash-card{
    background:var(--panel); border:1px solid var(--line); border-radius:8px; padding:22px;
    position:relative;
  }
  .dash-card .icon{ font-size:20px; }
  .dash-card h3{ font-size:15px; margin:12px 0 4px; font-weight:700; }
  .dash-card p.tools{ font-family:var(--mono); font-size:11.5px; color:var(--muted); margin-bottom:16px; }
  .conf-bar{ height:4px; border-radius:2px; background:var(--line); overflow:hidden; }
  .conf-fill{ height:100%; width:0; background:linear-gradient(90deg,var(--scan),var(--blue)); transition:width 1.2s cubic-bezier(.2,.8,.2,1); }
  .conf-label{ display:flex; justify-content:space-between; margin-top:8px; font-family:var(--mono); font-size:10.5px; color:var(--muted); }
  @media(max-width:860px){ .dash-grid{ grid-template-columns:1fr 1fr; } }
  @media(max-width:560px){ .dash-grid{ grid-template-columns:1fr; } }

  /* ---------- STACK ---------- */
  .stack-groups{ display:flex; flex-direction:column; gap:22px; }
  .stack-row{ display:flex; align-items:center; gap:18px; flex-wrap:wrap; }
  .stack-row .label{ font-family:var(--mono); font-size:11px; color:var(--muted); width:150px; letter-spacing:.05em; }
  .chip{
    font-family:var(--mono); font-size:12px; padding:7px 12px;
    border:1px solid var(--line); border-radius:20px; color:#c7cede;
    background:var(--panel);
  }

  /* ---------- PROJECTS ---------- */
  .proj-grid{ display:grid; grid-template-columns:1fr 1fr; gap:22px; perspective:1200px; }
  .proj-card{
    position:relative; background:var(--panel); border:1px solid var(--line); border-radius:10px;
    padding:26px; transform-style:preserve-3d; transition:transform .12s ease, border-color .2s;
    overflow:hidden;
  }
  .proj-card:hover{ border-color:#3a4358; }
  .proj-card .glare{
    position:absolute; inset:0; pointer-events:none; opacity:0; transition:opacity .2s;
    background:radial-gradient(circle at var(--mx,50%) var(--my,50%), rgba(95,227,163,.14), transparent 55%);
  }
  .proj-card:hover .glare{ opacity:1; }
  .proj-top{ display:flex; justify-content:space-between; align-items:flex-start; margin-bottom:6px; transform:translateZ(30px); }
  .proj-domain{ font-family:var(--mono); font-size:10.5px; color:var(--scan); letter-spacing:.08em; }
  .conf-badge{
    font-family:var(--mono); font-size:10.5px; color:var(--amber);
    border:1px solid #ffb45444; padding:3px 8px; border-radius:3px; background:#ffb45411;
  }
  .proj-card h3{ font-size:20px; margin:10px 0 10px; transform:translateZ(30px); }
  .proj-card p{ font-size:13.5px; line-height:1.65; color:var(--muted); margin-bottom:16px; transform:translateZ(20px); }
  .proj-card ul{ list-style:none; font-size:13px; color:#c7cede; margin-bottom:18px; transform:translateZ(20px); }
  .proj-card ul li{ padding-left:14px; position:relative; margin-bottom:6px; }
  .proj-card ul li::before{ content:'▸'; position:absolute; left:0; color:var(--scan); }
  .proj-tags{ display:flex; gap:8px; flex-wrap:wrap; transform:translateZ(20px); margin-bottom:16px; }
  .proj-tags span{ font-family:var(--mono); font-size:10.5px; color:var(--blue); border:1px solid #5fc8ff33; padding:3px 8px; border-radius:3px; }
  .proj-link{ font-family:var(--mono); font-size:12px; color:var(--text); display:inline-flex; align-items:center; gap:6px; transform:translateZ(20px); }
  .proj-link:hover{ color:var(--scan); }
  .proj-card.wide{ grid-column:1 / -1; }
  @media(max-width:860px){ .proj-grid{ grid-template-columns:1fr; } }

  /* ---------- ANALYTICS ---------- */
  .analytics-wrap img{ max-width:100%; border-radius:8px; }
  .analytics-row{ display:flex; gap:16px; flex-wrap:wrap; margin-bottom:16px; }
  .analytics-row img{ flex:1; min-width:280px; background:var(--panel); border:1px solid var(--line); border-radius:8px; }
  .panel-frame{ border:1px solid var(--line); border-radius:8px; padding:14px; background:var(--panel); }

  /* ---------- GOALS ---------- */
  .goal-list{ display:flex; flex-direction:column; gap:0; border-top:1px solid var(--line); }
  .goal-item{
    display:flex; align-items:center; gap:16px; padding:16px 4px;
    border-bottom:1px solid var(--line); font-size:14.5px;
  }
  .goal-item .box{ width:16px; height:16px; border:1px solid var(--muted); border-radius:3px; flex:none; }
  .goal-item .idx{ font-family:var(--mono); font-size:11px; color:var(--muted); width:26px; }

  /* ---------- RECRUITER PANEL ---------- */
  .recruit-panel{
    background:var(--panel); border:1px solid var(--line); border-radius:10px; padding:6px;
  }
  .recruit-row{
    display:grid; grid-template-columns:200px 1fr; gap:20px; padding:16px 20px;
    border-bottom:1px solid var(--line); align-items:center;
  }
  .recruit-row:last-child{ border-bottom:none; }
  .recruit-row .k{ font-family:var(--mono); font-size:11px; color:var(--scan); letter-spacing:.06em; }
  .recruit-row .v{ font-size:14px; color:#c7cede; }
  .cta-row{ display:flex; gap:14px; margin-top:28px; flex-wrap:wrap; }
  .btn{
    font-family:var(--mono); font-size:12.5px; padding:12px 20px; border-radius:6px;
    border:1px solid var(--line); display:inline-flex; align-items:center; gap:8px;
  }
  .btn.primary{ background:var(--scan); color:#08090c; border-color:var(--scan); font-weight:700; }
  .btn:hover{ border-color:var(--scan); }

  footer{
    text-align:center; padding:50px 20px 70px; font-family:var(--mono);
    font-size:11px; color:var(--muted); border-top:1px solid var(--line);
  }
  footer .dot{ display:inline-block; margin:0 6px; }
</style>
</head>
<body>

<div class="noise"></div>

<div class="topbar">
  <div class="brand">AJAY_R.SYS</div>
  <div class="status"><span class="dot"></span> OPEN_TO_AI_ML_ROLES</div>
  <nav>
    <a href="mailto:ajayramaraj1210@gmail.com">EMAIL</a>
    <a href="https://www.linkedin.com/in/ajay-r-4686b739a/" target="_blank">LINKEDIN</a>
    <a href="#projects">PROJECTS</a>
  </nav>
</div>

<!-- ===================== HERO / VIEWFINDER ===================== -->
<div class="viewfinder">
  <canvas id="scene-canvas"></canvas>
  <div class="vf-vignette"></div>
  <div class="scanline"></div>

  <div class="bracket tl"><svg viewBox="0 0 38 38"><path d="M2 20 L2 2 L20 2" stroke="#5fe3a3" stroke-width="2" fill="none"/></svg></div>
  <div class="bracket tr"><svg viewBox="0 0 38 38"><path d="M2 20 L2 2 L20 2" stroke="#5fe3a3" stroke-width="2" fill="none"/></svg></div>
  <div class="bracket bl"><svg viewBox="0 0 38 38"><path d="M2 20 L2 2 L20 2" stroke="#5fe3a3" stroke-width="2" fill="none"/></svg></div>
  <div class="bracket br"><svg viewBox="0 0 38 38"><path d="M2 20 L2 2 L20 2" stroke="#5fe3a3" stroke-width="2" fill="none"/></svg></div>

  <div class="rec"><span class="dot"></span> LIVE <span id="clock">00:00:00</span></div>

  <div class="tag-cloud" id="tagCloud"></div>

  <div class="hero-content">
    <div class="eyebrow">// object_class: ai_engineer</div>
    <h1 class="hero-name">Ajay&nbsp;R</h1>
    <div class="hero-role">Computer Vision · Generative AI · RAG Systems — building AI that survives production</div>
  </div>

  <div class="scroll-cue">SCROLL<div class="line"></div></div>
</div>

<!-- ===================== ABOUT ===================== -->
<section id="about">
  <div class="section-head">
    <span class="section-idx">01</span>
    <div>
      <div class="section-title">About</div>
      <div class="section-sub">// what gets shipped, not just what gets demoed</div>
    </div>
  </div>
  <div class="about-grid">
    <div class="about-copy">
      <p>I build and ship <strong>production-grade AI systems</strong> — not notebooks. My work spans real-time
      <strong>computer vision pipelines</strong>, <strong>retrieval-augmented generation (RAG)</strong> systems running
      on local LLMs, and <strong>conversational AI</strong> products served through FastAPI.</p><br/>
      <p>I care about the part most tutorials skip: inference latency, Docker deployment, vector index design, and
      making a model someone can actually depend on.</p>
    </div>
    <div class="code-panel">
      <div class="bar"><i></i><i></i><i></i></div>
      <pre><span class="k">class</span> <span class="f">AjayR</span>:
    <span class="k">def</span> <span class="f">__init__</span>(self):
        self.role        = <span class="s">"AI Engineer"</span>
        self.specialties = [<span class="s">"Computer Vision"</span>, <span class="s">"Generative AI"</span>, <span class="s">"RAG Systems"</span>]
        self.stack       = [<span class="s">"Python"</span>, <span class="s">"PyTorch"</span>, <span class="s">"YOLOv8"</span>, <span class="s">"FastAPI"</span>, <span class="s">"FAISS"</span>, <span class="s">"Docker"</span>]
        self.philosophy  = <span class="s">"Ship models that survive production, not just demos."</span>

    <span class="k">def</span> <span class="f">currently</span>(self):
        <span class="c"># real-time CV pipelines + local-LLM RAG architectures</span>
        <span class="k">return</span> <span class="s">"Designing real-time CV pipelines + local-LLM RAG"</span></pre>
    </div>
  </div>
</section>

<!-- ===================== CAPABILITY DASHBOARD ===================== -->
<section id="dashboard">
  <div class="section-head">
    <span class="section-idx">02</span>
    <div>
      <div class="section-title">AI Capability Dashboard</div>
      <div class="section-sub">// confidence = maturity in production</div>
    </div>
  </div>
  <div class="dash-grid" id="dashGrid">
    <div class="dash-card" data-conf="96">
      <div class="icon">👁️</div>
      <h3>Computer Vision</h3>
      <p class="tools">YOLOv8 · OpenCV · PyTorch</p>
      <div class="conf-bar"><div class="conf-fill"></div></div>
      <div class="conf-label"><span>Detection · tracking · analytics</span><span>PRODUCTION</span></div>
    </div>
    <div class="dash-card" data-conf="94">
      <div class="icon">🔎</div>
      <h3>Generative AI / RAG</h3>
      <p class="tools">FAISS · Ollama · Gemma · MiniLM</p>
      <div class="conf-bar"><div class="conf-fill"></div></div>
      <div class="conf-label"><span>Vector search · local inference</span><span>PRODUCTION</span></div>
    </div>
    <div class="dash-card" data-conf="92">
      <div class="icon">💬</div>
      <h3>Conversational AI</h3>
      <p class="tools">FastAPI · Gemini API · Streamlit</p>
      <div class="conf-bar"><div class="conf-fill"></div></div>
      <div class="conf-label"><span>Context-aware, personalized</span><span>PRODUCTION</span></div>
    </div>
    <div class="dash-card" data-conf="90">
      <div class="icon">⚙️</div>
      <h3>MLOps / Deployment</h3>
      <p class="tools">Docker · FastAPI · Flask</p>
      <div class="conf-bar"><div class="conf-fill"></div></div>
      <div class="conf-label"><span>Containerized inference, REST</span><span>PRODUCTION</span></div>
    </div>
    <div class="dash-card" data-conf="80">
      <div class="icon">📊</div>
      <h3>Data & Modeling</h3>
      <p class="tools">Scikit-learn · Pandas · NumPy · SQL</p>
      <div class="conf-bar"><div class="conf-fill"></div></div>
      <div class="conf-label"><span>Classical ML, evaluation</span><span>SOLID</span></div>
    </div>
    <div class="dash-card" data-conf="62">
      <div class="icon">🧩</div>
      <h3>Prompt Engineering</h3>
      <p class="tools">Ollama · Gemma · Gemini</p>
      <div class="conf-bar"><div class="conf-fill"></div></div>
      <div class="conf-label"><span>Structured prompting, orchestration</span><span>GROWING</span></div>
    </div>
  </div>
</section>

<!-- ===================== STACK ===================== -->
<section id="stack">
  <div class="section-head">
    <span class="section-idx">03</span>
    <div>
      <div class="section-title">Core Stack</div>
      <div class="section-sub">// instrumented for detection, retrieval and serving</div>
    </div>
  </div>
  <div class="stack-groups">
    <div class="stack-row"><div class="label">LANGUAGES/DATA</div>
      <span class="chip">Python</span><span class="chip">SQL</span><span class="chip">Pandas</span><span class="chip">NumPy</span>
    </div>
    <div class="stack-row"><div class="label">COMPUTER VISION</div>
      <span class="chip">PyTorch</span><span class="chip">YOLOv8</span><span class="chip">OpenCV</span><span class="chip">Scikit-learn</span>
    </div>
    <div class="stack-row"><div class="label">GENERATIVE AI</div>
      <span class="chip">RAG</span><span class="chip">FAISS</span><span class="chip">Ollama</span><span class="chip">Gemma</span><span class="chip">Prompt Engineering</span>
    </div>
    <div class="stack-row"><div class="label">BACKEND/INFRA</div>
      <span class="chip">FastAPI</span><span class="chip">REST APIs</span><span class="chip">Docker</span><span class="chip">Git</span><span class="chip">GitHub</span>
    </div>
  </div>
</section>

<!-- ===================== PROJECTS ===================== -->
<section id="projects">
  <div class="section-head">
    <span class="section-idx">04</span>
    <div>
      <div class="section-title">Flagship Projects</div>
      <div class="section-sub">// tilt to inspect — cursor-tracked 3D cards</div>
    </div>
  </div>
  <div class="proj-grid" id="projGrid">

    <div class="proj-card">
      <div class="glare"></div>
      <div class="proj-top"><span class="proj-domain">COMPUTER_VISION · REAL-TIME</span><span class="conf-badge">0.97</span></div>
      <h3>Traffic Violation Detection System</h3>
      <p>Real-time pipeline detecting vehicles, helmet usage, and traffic violations from live video, packaged for containerized deployment.</p>
      <ul>
        <li>Vehicle & helmet detection at video framerate</li>
        <li>Violation flagging + video analytics layer</li>
        <li>Dockerized inference service</li>
      </ul>
      <div class="proj-tags"><span>Python</span><span>YOLOv8</span><span>OpenCV</span><span>Flask</span><span>Docker</span></div>
      <a class="proj-link" href="https://github.com/ajayramaraj" target="_blank">View architecture →</a>
    </div>

    <div class="proj-card">
      <div class="glare"></div>
      <div class="proj-top"><span class="proj-domain">GENERATIVE_AI · RAG</span><span class="conf-badge">0.95</span></div>
      <h3>Industrial Maintenance System</h3>
      <p>Predictive maintenance assistant combining fault-detection signals with a RAG pipeline over a local LLM for grounded troubleshooting answers.</p>
      <ul>
        <li>FAISS vector search over maintenance docs</li>
        <li>Local inference via Ollama + Gemma</li>
        <li>MiniLM embeddings, fully offline-capable</li>
      </ul>
      <div class="proj-tags"><span>FAISS</span><span>Ollama</span><span>Gemma</span><span>MiniLM</span><span>Python</span></div>
      <a class="proj-link" href="https://github.com/ajayramaraj" target="_blank">View architecture →</a>
    </div>

    <div class="proj-card wide">
      <div class="glare"></div>
      <div class="proj-top"><span class="proj-domain">CONVERSATIONAL_AI · MULTI-TURN</span><span class="conf-badge">0.93</span></div>
      <h3>Healthcare Chatbot</h3>
      <p>Context-aware health assistant delivering personalized workout and diet recommendations with persistent conversation memory.</p>
      <ul>
        <li>Multi-turn memory backed by MySQL</li>
        <li>Gemini-powered personalized responses</li>
        <li>Streamlit front end, FastAPI backend</li>
      </ul>
      <div class="proj-tags"><span>FastAPI</span><span>Gemini API</span><span>Streamlit</span><span>MySQL</span></div>
      <a class="proj-link" href="https://github.com/ajayramaraj" target="_blank">View architecture →</a>
    </div>

  </div>
</section>

<!-- ===================== ANALYTICS ===================== -->
<section id="analytics">
  <div class="section-head">
    <span class="section-idx">05</span>
    <div>
      <div class="section-title">GitHub Telemetry</div>
      <div class="section-sub">// live feeds, refreshed nightly by CI</div>
    </div>
  </div>
  <div class="analytics-row">
    <img src="https://github-readme-stats.vercel.app/api?username=ajayramaraj&show_icons=true&theme=tokyonight&hide_border=true&bg_color=0d1117&title_color=00fff0&icon_color=7f5af0&text_color=c9d1d9" alt="GitHub stats" />
    <img src="https://github-readme-streak-stats.herokuapp.com/?user=ajayramaraj&theme=tokyonight&hide_border=true&background=0d1117&ring=00fff0&fire=7f5af0&currStreakLabel=00fff0" alt="Streak stats" />
  </div>
  <div class="panel-frame">
    <img src="https://github-readme-activity-graph.vercel.app/graph?username=ajayramaraj&theme=tokyo-night&hide_border=true&bg_color=0d1117&color=00fff0&line=7f5af0&point=ffffff" alt="Activity graph" style="width:100%;" />
  </div>
</section>

<!-- ===================== GOALS ===================== -->
<section id="goals">
  <div class="section-head">
    <span class="section-idx">06</span>
    <div>
      <div class="section-title">Open Source Goals</div>
      <div class="section-sub">// queued detections, not yet confirmed</div>
    </div>
  </div>
  <div class="goal-list">
    <div class="goal-item"><span class="idx">01</span><span class="box"></span>Ship a public, documented release of the Industrial Maintenance RAG pipeline</div>
    <div class="goal-item"><span class="idx">02</span><span class="box"></span>Contribute inference-optimization PRs to a YOLOv8/Ultralytics-adjacent project</div>
    <div class="goal-item"><span class="idx">03</span><span class="box"></span>Publish a reusable FastAPI + FAISS RAG starter template</div>
    <div class="goal-item"><span class="idx">04</span><span class="box"></span>Maintain a CV model benchmark repo (latency vs. accuracy across YOLO variants)</div>
  </div>
</section>

<!-- ===================== RECRUITERS ===================== -->
<section id="recruiters">
  <div class="section-head">
    <span class="section-idx">07</span>
    <div>
      <div class="section-title">For Recruiters</div>
      <div class="section-sub">// detection summary</div>
    </div>
  </div>
  <div class="recruit-panel">
    <div class="recruit-row"><div class="k">TARGET_ROLES</div><div class="v">AI Engineer · ML Engineer · Computer Vision Engineer · Generative AI Engineer</div></div>
    <div class="recruit-row"><div class="k">CORE_STRENGTH</div><div class="v">Taking CV/GenAI models from notebook → containerized, served, monitored system</div></div>
    <div class="recruit-row"><div class="k">AVAILABLE_FOR</div><div class="v">Full-time roles, contract AI engineering work</div></div>
    <div class="recruit-row"><div class="k">PROOF_POINTS</div><div class="v">Traffic Violation Detection (CV) · Industrial Maintenance RAG (GenAI) · Healthcare Chatbot (Conversational AI)</div></div>
  </div>
  <div class="cta-row">
    <a class="btn primary" href="mailto:ajayramaraj1210@gmail.com">EMAIL ME →</a>
    <a class="btn" href="https://www.linkedin.com/in/ajay-r-4686b739a/" target="_blank">LINKEDIN</a>
    <a class="btn" href="./assets/resume/Ajay_R_AI_Engineer_Resume.pdf" target="_blank">DOWNLOAD RESUME</a>
  </div>
</section>

<footer>
  AJAY_R.SYS <span class="dot">•</span> BUILT WITH THREE.JS + INTENT <span class="dot">•</span> <span id="year"></span>
</footer>

<script>
/* ---------------- live clock ---------------- */
function tick(){
  const d = new Date();
  document.getElementById('clock').textContent = d.toTimeString().slice(0,8);
}
tick(); setInterval(tick, 1000);
document.getElementById('year').textContent = new Date().getFullYear();

/* ---------------- floating detection tags ---------------- */
const tags = [
  {t:'AI_ENGINEER', c:'0.98', top:'22%', left:'8%', delay:0.9},
  {t:'COMPUTER_VISION', c:'0.95', top:'70%', left:'12%', delay:1.3},
  {t:'GENERATIVE_AI', c:'0.93', top:'28%', left:'82%', delay:1.7},
  {t:'RAG_SYSTEMS', c:'0.91', top:'66%', left:'80%', delay:2.1},
];
const cloud = document.getElementById('tagCloud');
tags.forEach(o=>{
  const el = document.createElement('div');
  el.className = 'det-tag';
  el.style.top = o.top; el.style.left = o.left;
  el.style.animationDelay = o.delay + 's';
  el.innerHTML = o.t + '<span>· ' + o.c + '</span>';
  cloud.appendChild(el);
});

/* ---------------- three.js knowledge-graph scan scene ---------------- */
(function(){
  const canvas = document.getElementById('scene-canvas');
  const renderer = new THREE.WebGLRenderer({ canvas, antialias:true, alpha:true });
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(50, 1, 0.1, 100);
  camera.position.set(0,0,14);

  function resize(){
    const w = canvas.parentElement.clientWidth, h = canvas.parentElement.clientHeight;
    renderer.setSize(w,h);
    camera.aspect = w/h; camera.updateProjectionMatrix();
  }
  resize(); window.addEventListener('resize', resize);

  const group = new THREE.Group();
  scene.add(group);

  const geo = new THREE.IcosahedronGeometry(5.4, 2);
  const edges = new THREE.EdgesGeometry(geo);
  const lineMat = new THREE.LineBasicMaterial({ color:0x5fe3a3, transparent:true, opacity:0.28 });
  const wire = new THREE.LineSegments(edges, lineMat);
  group.add(wire);

  const ptsMat = new THREE.PointsMaterial({ color:0xffb454, size:0.09, transparent:true, opacity:0.9 });
  const pts = new THREE.Points(geo, ptsMat);
  group.add(pts);

  // ambient outer particle field (embedding space)
  const fieldGeo = new THREE.BufferGeometry();
  const N = 260, positions = new Float32Array(N*3);
  for(let i=0;i<N;i++){
    const r = 8 + Math.random()*6;
    const theta = Math.random()*Math.PI*2, phi = Math.acos(2*Math.random()-1);
    positions[i*3]   = r*Math.sin(phi)*Math.cos(theta);
    positions[i*3+1] = r*Math.sin(phi)*Math.sin(theta);
    positions[i*3+2] = r*Math.cos(phi);
  }
  fieldGeo.setAttribute('position', new THREE.BufferAttribute(positions,3));
  const fieldMat = new THREE.PointsMaterial({ color:0x5fc8ff, size:0.045, transparent:true, opacity:0.5 });
  const field = new THREE.Points(fieldGeo, fieldMat);
  scene.add(field);

  // scan plane (lidar-style sweep through the graph)
  const planeGeo = new THREE.PlaneGeometry(16,16);
  const planeMat = new THREE.MeshBasicMaterial({ color:0x5fe3a3, transparent:true, opacity:0.05, side:THREE.DoubleSide });
  const scanPlane = new THREE.Mesh(planeGeo, planeMat);
  scanPlane.rotation.x = Math.PI/2;
  scene.add(scanPlane);

  let mouseX = 0, mouseY = 0;
  window.addEventListener('mousemove', e=>{
    mouseX = (e.clientX/window.innerWidth - 0.5);
    mouseY = (e.clientY/window.innerHeight - 0.5);
  });

  const clock = new THREE.Clock();
  function animate(){
    requestAnimationFrame(animate);
    const t = clock.getElapsedTime();
    group.rotation.y = t*0.12;
    group.rotation.x = Math.sin(t*0.15)*0.15;
    field.rotation.y = -t*0.03;
    scanPlane.position.y = Math.sin(t*0.5)*5.5;

    camera.position.x += (mouseX*3 - camera.position.x)*0.03;
    camera.position.y += (-mouseY*3 - camera.position.y)*0.03;
    camera.lookAt(0,0,0);

    renderer.render(scene, camera);
  }
  animate();
})();

/* ---------------- confidence bar reveal on scroll ---------------- */
const dashCards = document.querySelectorAll('.dash-card');
const io = new IntersectionObserver((entries)=>{
  entries.forEach(en=>{
    if(en.isIntersecting){
      const fill = en.target.querySelector('.conf-fill');
      fill.style.width = en.target.dataset.conf + '%';
      io.unobserve(en.target);
    }
  });
}, { threshold:0.4 });
dashCards.forEach(c=>io.observe(c));

/* ---------------- project card 3D tilt ---------------- */
document.querySelectorAll('.proj-card').forEach(card=>{
  card.addEventListener('mousemove', e=>{
    const r = card.getBoundingClientRect();
    const x = e.clientX - r.left, y = e.clientY - r.top;
    const cx = x/r.width - 0.5, cy = y/r.height - 0.5;
    card.style.transform = `rotateY(${cx*10}deg) rotateX(${-cy*10}deg) translateZ(0)`;
    card.style.setProperty('--mx', x+'px');
    card.style.setProperty('--my', y+'px');
  });
  card.addEventListener('mouseleave', ()=>{
    card.style.transform = 'rotateY(0deg) rotateX(0deg)';
  });
});
</script>

</body>
</html>
