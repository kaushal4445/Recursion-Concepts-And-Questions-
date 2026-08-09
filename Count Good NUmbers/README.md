<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Count Good Numbers — Exponentiation by Squaring</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#0A0D12;
    --surface:#12161F;
    --surface-2:#171C27;
    --border:#232838;
    --text:#E9EDF4;
    --muted:#8992A6;
    --even:#4FD8C4;      /* teal — even index / findPower(5,..) */
    --odd:#B490FA;        /* violet — odd index / findPower(4,..) */
    --gold:#F2B84B;        /* multiply-by-a highlight */
    --danger:#FF7A7A;
  }
  *{box-sizing:border-box;}
  html{scroll-behavior:smooth;}
  body{
    margin:0;
    background:var(--bg);
    color:var(--text);
    font-family:'Space Grotesk', sans-serif;
    -webkit-font-smoothing:antialiased;
    overflow-x:hidden;
  }
  .mono{font-family:'JetBrains Mono', monospace;}
  ::selection{background:var(--even); color:#001;}

  /* ---------- layout helpers ---------- */
  section{
    max-width:920px;
    margin:0 auto;
    padding:110px 28px;
    position:relative;
  }
  .eyebrow{
    font-family:'JetBrains Mono', monospace;
    font-size:12.5px;
    letter-spacing:.14em;
    text-transform:uppercase;
    color:var(--muted);
    display:flex;
    align-items:center;
    gap:10px;
    margin-bottom:18px;
  }
  .eyebrow::before{
    content:"";
    width:22px; height:1px;
    background:var(--border);
  }
  h2{
    font-size:clamp(26px,4vw,38px);
    line-height:1.15;
    margin:0 0 18px;
    font-weight:600;
    letter-spacing:-.01em;
  }
  p.lead{
    color:var(--muted);
    font-size:17px;
    line-height:1.7;
    max-width:640px;
    margin:0 0 40px;
  }
  .accent-even{color:var(--even);}
  .accent-odd{color:var(--odd);}
  .accent-gold{color:var(--gold);}

  .reveal{opacity:0; transform:translateY(22px); transition:opacity .7s ease, transform .7s ease;}
  .reveal.in{opacity:1; transform:translateY(0);}

  /* ---------- hero ---------- */
  .hero{
    min-height:100vh;
    display:flex;
    flex-direction:column;
    align-items:center;
    justify-content:center;
    text-align:center;
    padding:40px 28px;
    background:
      radial-gradient(700px 400px at 50% -10%, rgba(79,216,196,.10), transparent 60%),
      radial-gradient(700px 400px at 100% 110%, rgba(180,144,250,.10), transparent 60%);
  }
  .hero-tag{
    font-family:'JetBrains Mono',monospace;
    font-size:12.5px;
    color:var(--muted);
    letter-spacing:.18em;
    text-transform:uppercase;
    margin-bottom:26px;
    border:1px solid var(--border);
    padding:6px 14px;
    border-radius:100px;
  }
  .hero h1{
    font-size:clamp(38px,7vw,68px);
    line-height:1.04;
    margin:0 0 20px;
    font-weight:700;
    letter-spacing:-.02em;
  }
  .hero h1 span{
    background:linear-gradient(95deg, var(--even), var(--odd));
    -webkit-background-clip:text;
    background-clip:text;
    color:transparent;
  }
  .hero p{
    color:var(--muted);
    font-size:18px;
    max-width:560px;
    line-height:1.7;
    margin:0 0 44px;
  }
  .digit-strip{
    display:flex;
    gap:8px;
    font-family:'JetBrains Mono',monospace;
    font-weight:700;
    font-size:28px;
    margin-bottom:14px;
  }
  .digit-strip .d{
    width:46px; height:56px;
    border-radius:10px;
    display:flex; align-items:center; justify-content:center;
    border:1px solid var(--border);
    background:var(--surface);
    transition:transform .25s ease;
  }
  .digit-strip .d.even{color:var(--even); box-shadow:inset 0 0 0 1px rgba(79,216,196,.25);}
  .digit-strip .d.odd{color:var(--odd); box-shadow:inset 0 0 0 1px rgba(180,144,250,.25);}
  .strip-caption{
    font-family:'JetBrains Mono',monospace;
    font-size:12.5px; color:var(--muted); letter-spacing:.05em;
    margin-bottom:60px;
  }
  .strip-caption b.accent-even{font-weight:600;}
  .strip-caption b.accent-odd{font-weight:600;}
  .scroll-cue{
    position:absolute; bottom:38px; left:50%; transform:translateX(-50%);
    display:flex; flex-direction:column; align-items:center; gap:8px;
    color:var(--muted); font-size:11px; letter-spacing:.14em; text-transform:uppercase;
    font-family:'JetBrains Mono',monospace;
  }
  .scroll-cue .line{width:1px; height:34px; background:linear-gradient(var(--muted), transparent); animation:pulse-line 1.8s ease-in-out infinite;}
  @keyframes pulse-line{0%,100%{opacity:.3;}50%{opacity:1;}}

  /* ---------- section 1: the rule / digit diagram ---------- */
  .rule-diagram{
    display:flex; justify-content:center; gap:10px; flex-wrap:wrap;
    margin:36px 0 30px;
  }
  .pos{
    display:flex; flex-direction:column; align-items:center; gap:10px;
  }
  .pos .idx{font-family:'JetBrains Mono',monospace; font-size:12px; color:var(--muted);}
  .pos .cell{
    width:58px; height:70px; border-radius:12px;
    display:flex; align-items:center; justify-content:center;
    font-family:'JetBrains Mono',monospace; font-weight:700; font-size:26px;
    border:1px solid var(--border); background:var(--surface);
  }
  .pos.even .cell{color:var(--even); border-color:rgba(79,216,196,.35); box-shadow:0 0 24px -12px rgba(79,216,196,.6);}
  .pos.odd .cell{color:var(--odd); border-color:rgba(180,144,250,.35); box-shadow:0 0 24px -12px rgba(180,144,250,.6);}
  .pos .rule{font-size:11px; color:var(--muted); text-align:center; max-width:70px; line-height:1.4;}

  .legend{
    display:flex; gap:28px; justify-content:center; flex-wrap:wrap;
    font-size:14px; color:var(--muted); margin-bottom:8px;
  }
  .legend .item{display:flex; align-items:center; gap:8px;}
  .legend .dot{width:10px; height:10px; border-radius:50%;}
  .legend .dot.even{background:var(--even);}
  .legend .dot.odd{background:var(--odd);}

  /* ---------- section 2: formula ---------- */
  .formula-card{
    background:var(--surface);
    border:1px solid var(--border);
    border-radius:16px;
    padding:36px;
    display:flex; flex-direction:column; align-items:center; gap:22px;
    margin-top:10px;
  }
  .formula-row{
    font-family:'JetBrains Mono',monospace;
    font-size:clamp(18px,3.4vw,26px);
    display:flex; align-items:center; gap:12px; flex-wrap:wrap; justify-content:center;
  }
  .formula-row .box{
    padding:8px 14px; border-radius:10px; border:1px solid var(--border); background:var(--surface-2);
  }
  .formula-row .box.even{color:var(--even); border-color:rgba(79,216,196,.35);}
  .formula-row .box.odd{color:var(--odd); border-color:rgba(180,144,250,.35);}
  .formula-row .op{color:var(--muted);}
  .formula-arrow{color:var(--muted); font-size:22px;}
  .formula-note{font-size:13.5px; color:var(--muted); text-align:center; max-width:520px; line-height:1.7;}

  /* ---------- section 3: race ---------- */
  .race{
    display:flex; flex-direction:column; gap:26px; margin-top:20px;
  }
  .race-row{display:flex; align-items:center; gap:18px;}
  .race-label{
    width:150px; flex-shrink:0; font-family:'JetBrains Mono',monospace; font-size:13px; color:var(--muted);
  }
  .race-track{
    flex:1; height:14px; border-radius:100px; background:var(--surface); border:1px solid var(--border); overflow:hidden; position:relative;
  }
  .race-fill{
    height:100%; width:0%; border-radius:100px;
  }
  .race-fill.linear{background:linear-gradient(90deg, var(--danger), #ff9f7a);}
  .race-fill.log{background:linear-gradient(90deg, var(--even), var(--odd));}
  section.race-in .race-fill.linear{width:78%; transition:width 5.5s cubic-bezier(.2,.6,.3,1);}
  section.race-in .race-fill.log{width:100%; transition:width .5s cubic-bezier(.2,.6,.3,1) .15s;}
  .race-steps{width:150px; text-align:right; font-family:'JetBrains Mono',monospace; font-size:13px; color:var(--muted); flex-shrink:0;}

  /* ---------- section 4: recursion tree ---------- */
  .tree-wrap{
    background:var(--surface);
    border:1px solid var(--border);
    border-radius:16px;
    padding:20px 10px 30px;
    margin-top:10px;
  }
  .tree-toolbar{
    display:flex; justify-content:space-between; align-items:center;
    padding:8px 20px 20px;
    font-family:'JetBrains Mono',monospace; font-size:12.5px; color:var(--muted);
  }
  .replay-btn{
    background:var(--surface-2); border:1px solid var(--border); color:var(--text);
    font-family:'JetBrains Mono',monospace; font-size:12.5px; padding:8px 14px; border-radius:8px;
    cursor:pointer; display:flex; align-items:center; gap:8px; transition:border-color .2s;
  }
  .replay-btn:hover{border-color:var(--even);}
  svg#tree{width:100%; height:auto; display:block;}
  .tnode{opacity:0; transform:translateY(-8px); transition:opacity .5s ease, transform .5s ease;}
  .tree-wrap.play .tnode{opacity:1; transform:translateY(0);}
  .tline{stroke-dasharray:120; stroke-dashoffset:120; transition:stroke-dashoffset .5s ease;}
  .tree-wrap.play .tline{stroke-dashoffset:0;}
  .tresult{opacity:0; transform:scale(.7); transition:opacity .45s ease, transform .45s ease; transform-origin:center;}
  .tree-wrap.play .tresult{opacity:1; transform:scale(1);}
  .tbadge{opacity:0; transition:opacity .4s ease;}
  .tree-wrap.play .tbadge{opacity:1;}

  /* ---------- section 5: code ---------- */
  .code-layout{display:flex; gap:26px; align-items:flex-start; flex-wrap:wrap; margin-top:10px;}
  .code-panel{
    flex:1 1 400px;
    background:var(--surface);
    border:1px solid var(--border);
    border-radius:14px;
    overflow:hidden;
  }
  .code-panel .head{
    display:flex; align-items:center; gap:8px;
    padding:12px 16px; border-bottom:1px solid var(--border);
    font-family:'JetBrains Mono',monospace; font-size:12px; color:var(--muted);
  }
  .code-panel .head .dot{width:9px;height:9px;border-radius:50%;}
  .code-panel pre{
    margin:0; padding:18px 0; font-family:'JetBrains Mono',monospace; font-size:13.5px; line-height:1.9;
    overflow-x:auto;
  }
  .code-panel .line{display:block; padding:0 20px; white-space:pre; color:#C7CEDB;}
  .code-panel .gutter{display:inline-block; width:24px; color:#454C5F; user-select:none;}
  .kw{color:#79A7FF;} .tp{color:#4FD8C4;} .fn{color:#F2B84B;} .cm{color:#5C6478;} .num{color:#FFB672;}
  .tok{border-radius:5px; padding:1px 3px; transition:background .3s, box-shadow .3s;}
  .tok.hl{background:rgba(242,184,75,.18); box-shadow:0 0 0 1px rgba(242,184,75,.4);}

  .anno-panel{
    flex:1 1 260px;
    display:flex; flex-direction:column; gap:8px;
  }
  .anno-btn{
    text-align:left; background:var(--surface); border:1px solid var(--border); color:var(--text);
    padding:13px 16px; border-radius:10px; cursor:pointer; font-family:'Space Grotesk',sans-serif;
    font-size:14.5px; transition:border-color .2s, background .2s;
  }
  .anno-btn:hover{border-color:var(--muted);}
  .anno-btn.active{border-color:var(--gold); background:rgba(242,184,75,.08);}
  .anno-desc{
    min-height:64px; font-size:13.5px; color:var(--muted); line-height:1.6;
    padding:14px 16px; border:1px dashed var(--border); border-radius:10px; margin-top:4px;
  }

  /* ---------- complexity cards ---------- */
  .cards{display:grid; grid-template-columns:1fr 1fr; gap:18px; margin-top:10px;}
  @media(max-width:600px){.cards{grid-template-columns:1fr;}}
  .card{
    background:var(--surface); border:1px solid var(--border); border-radius:14px; padding:24px;
  }
  .card .k{font-family:'JetBrains Mono',monospace; font-size:12px; color:var(--muted); text-transform:uppercase; letter-spacing:.1em;}
  .card .v{font-family:'JetBrains Mono',monospace; font-size:30px; font-weight:700; margin:10px 0; color:var(--even);}
  .card.space .v{color:var(--odd);}
  .card .d{font-size:13.5px; color:var(--muted); line-height:1.6;}

  footer{
    text-align:center; padding:60px 28px 90px; color:var(--muted); font-size:13px;
    font-family:'JetBrains Mono',monospace;
  }
  footer a{color:var(--even); text-decoration:none;}
</style>
</head>
<body>

<!-- ================= HERO ================= -->
<div class="hero">
  <div class="hero-tag">LeetCode 1922 · Modular Exponentiation</div>
  <h1>Count <span>Good</span> Numbers</h1>
  <p>Every digit string of length <span class="mono">n</span> is really just a product of
     independent choices. The hard part isn't counting them — it's raising a number to a
     power that can be as large as <span class="mono accent-gold">10¹⁵</span> without your
     program running until the heat death of the universe.</p>
  <div class="digit-strip" id="heroStrip"></div>
  <div class="strip-caption"><b class="accent-even">even index</b> → 0,2,4,6,8&nbsp;&nbsp;·&nbsp;&nbsp;<b class="accent-odd">odd index</b> → 2,3,5,7</div>
  <div class="scroll-cue"><span>scroll</span><span class="line"></span></div>
</div>

<!-- ================= 1. THE RULE ================= -->
<section id="rule">
  <div class="eyebrow reveal">01 — The Rule</div>
  <h2 class="reveal">What makes a number "good"?</h2>
  <p class="lead reveal">A digit string is <strong>good</strong> if, at every even index, the digit is even —
    and at every odd index, the digit is prime. Position decides the alphabet.</p>

  <div class="rule-diagram reveal" id="ruleDiagram"></div>
  <div class="legend reveal">
    <div class="item"><span class="dot even"></span> even index — 5 choices: 0, 2, 4, 6, 8</div>
    <div class="item"><span class="dot odd"></span> odd index — 4 choices: 2, 3, 5, 7</div>
  </div>
</section>

<!-- ================= 2. THE FORMULA ================= -->
<section id="formula">
  <div class="eyebrow reveal">02 — The Formula</div>
  <h2 class="reveal">Multiply the choices, position by position</h2>
  <p class="lead reveal">A string of length <span class="mono">n</span> has
    <span class="mono">⌈n/2⌉</span> even indices and <span class="mono">⌊n/2⌋</span> odd indices.
    Since each position's choice is independent, the total count is just the product.</p>

  <div class="formula-card reveal">
    <div class="formula-row">
      <span class="box even">5<sup>⌈n/2⌉</sup></span>
      <span class="op">×</span>
      <span class="box odd">4<sup>⌊n/2⌋</sup></span>
      <span class="op">mod (10⁹ + 7)</span>
    </div>
    <div class="formula-arrow">↓ same shape as the code</div>
    <div class="formula-row" style="font-size:16px;">
      <span class="box even mono">findPower(5, (n+1)/2)</span>
      <span class="op">×</span>
      <span class="box odd mono">findPower(4, n/2)</span>
    </div>
    <p class="formula-note">The only real work left is computing <span class="mono">aᵇ mod M</span> fast —
      that's the entire job of <span class="mono accent-gold">findPower</span>.</p>
  </div>
</section>

<!-- ================= 3. WHY NOT JUST LOOP ================= -->
<section id="race">
  <div class="eyebrow reveal">03 — Why Not Just Loop?</div>
  <h2 class="reveal">n can be 10¹⁵. Looping isn't an option.</h2>
  <p class="lead reveal">Multiplying <span class="mono">a</span> by itself <span class="mono">b</span> times takes
    <span class="mono">b</span> steps. Exponentiation by squaring takes about
    <span class="mono">log₂ b</span> steps — for <span class="mono">b ≈ 5×10¹⁴</span>, that's the difference
    between roughly <strong class="accent-gold">50 steps</strong> and a program that never finishes.</p>

  <div class="race reveal">
    <div class="race-row">
      <div class="race-label">naïve loop</div>
      <div class="race-track"><div class="race-fill linear"></div></div>
      <div class="race-steps">O(b) steps</div>
    </div>
    <div class="race-row">
      <div class="race-label">binary exponentiation</div>
      <div class="race-track"><div class="race-fill log"></div></div>
      <div class="race-steps">O(log b) steps</div>
    </div>
  </div>
</section>

<!-- ================= 4. RECURSION TREE ================= -->
<section id="treeSection">
  <div class="eyebrow reveal">04 — Exponentiation by Squaring</div>
  <h2 class="reveal">Halve the exponent, square the result</h2>
  <p class="lead reveal">Walk through <span class="mono accent-gold">findPower(5, 13)</span>. Going down,
    the exponent halves each call. Going back up, each result is squared — and wherever the exponent
    was odd, one extra factor of <span class="mono">a</span> is folded back in.</p>

  <div class="tree-wrap reveal" id="treeWrap">
    <div class="tree-toolbar">
      <span>findPower(5, 13) → 1,220,703,125</span>
      <button class="replay-btn" id="replayBtn">↻ replay</button>
    </div>
    <svg id="tree" viewBox="0 0 880 470" xmlns="http://www.w3.org/2000/svg">
      <!-- connecting lines: down the spine -->
      <line class="tline" x1="440" y1="70"  x2="440" y2="130" stroke="#2b3145" stroke-width="2"/>
      <line class="tline" x1="440" y1="160" x2="440" y2="220" stroke="#2b3145" stroke-width="2"/>
      <line class="tline" x1="440" y1="250" x2="440" y2="310" stroke="#2b3145" stroke-width="2"/>
      <line class="tline" x1="440" y1="340" x2="440" y2="400" stroke="#2b3145" stroke-width="2"/>

      <!-- nodes, down the spine: b=13,6,3,1,0 -->
      <g class="tnode" style="transition-delay:.05s">
        <rect x="380" y="18" width="120" height="52" rx="12" fill="#171C27" stroke="#B490FA" stroke-width="1.5"/>
        <text x="440" y="40" text-anchor="middle" font-family="JetBrains Mono" font-size="12" fill="#8992A6">b = 13 (odd)</text>
        <text x="440" y="58" text-anchor="middle" font-family="JetBrains Mono" font-size="13" fill="#E9EDF4" font-weight="700">findPower(5,13)</text>
      </g>
      <g class="tnode" style="transition-delay:.35s">
        <rect x="380" y="130" width="120" height="52" rx="12" fill="#171C27" stroke="#4FD8C4" stroke-width="1.5"/>
        <text x="440" y="152" text-anchor="middle" font-family="JetBrains Mono" font-size="12" fill="#8992A6">b = 6 (even)</text>
        <text x="440" y="170" text-anchor="middle" font-family="JetBrains Mono" font-size="13" fill="#E9EDF4" font-weight="700">findPower(5,6)</text>
      </g>
      <g class="tnode" style="transition-delay:.65s">
        <rect x="380" y="220" width="120" height="52" rx="12" fill="#171C27" stroke="#B490FA" stroke-width="1.5"/>
        <text x="440" y="242" text-anchor="middle" font-family="JetBrains Mono" font-size="12" fill="#8992A6">b = 3 (odd)</text>
        <text x="440" y="260" text-anchor="middle" font-family="JetBrains Mono" font-size="13" fill="#E9EDF4" font-weight="700">findPower(5,3)</text>
      </g>
      <g class="tnode" style="transition-delay:.95s">
        <rect x="380" y="310" width="120" height="52" rx="12" fill="#171C27" stroke="#B490FA" stroke-width="1.5"/>
        <text x="440" y="332" text-anchor="middle" font-family="JetBrains Mono" font-size="12" fill="#8992A6">b = 1 (odd)</text>
        <text x="440" y="350" text-anchor="middle" font-family="JetBrains Mono" font-size="13" fill="#E9EDF4" font-weight="700">findPower(5,1)</text>
      </g>
      <g class="tnode" style="transition-delay:1.25s">
        <rect x="380" y="400" width="120" height="52" rx="12" fill="#12161F" stroke="#2b3145" stroke-width="1.5"/>
        <text x="440" y="422" text-anchor="middle" font-family="JetBrains Mono" font-size="12" fill="#8992A6">b = 0</text>
        <text x="440" y="440" text-anchor="middle" font-family="JetBrains Mono" font-size="13" fill="#4FD8C4" font-weight="700">base case → 1</text>
      </g>

      <!-- returning results, right side, bottom to top -->
      <g class="tresult" style="transition-delay:1.55s">
        <line x1="500" y1="426" x2="560" y2="426" stroke="#2b3145" stroke-width="2"/>
        <text x="570" y="431" font-family="JetBrains Mono" font-size="13" fill="#4FD8C4">1</text>
      </g>
      <g class="tresult" style="transition-delay:1.85s">
        <line x1="500" y1="336" x2="560" y2="336" stroke="#2b3145" stroke-width="2"/>
        <text x="570" y="326" font-family="JetBrains Mono" font-size="12" fill="#8992A6">1×1=1</text>
        <text x="570" y="345" font-family="JetBrains Mono" font-size="13" fill="#E9EDF4">×5 → 5</text>
      </g>
      <g class="tbadge" style="transition-delay:1.9s">
        <rect x="500" y="313" width="34" height="20" rx="6" fill="rgba(242,184,75,.15)" stroke="#F2B84B"/>
        <text x="517" y="327" text-anchor="middle" font-family="JetBrains Mono" font-size="11" fill="#F2B84B">×5</text>
      </g>

      <g class="tresult" style="transition-delay:2.15s">
        <line x1="500" y1="246" x2="560" y2="246" stroke="#2b3145" stroke-width="2"/>
        <text x="570" y="236" font-family="JetBrains Mono" font-size="12" fill="#8992A6">5×5=25</text>
        <text x="570" y="255" font-family="JetBrains Mono" font-size="13" fill="#E9EDF4">×5 → 125</text>
      </g>
      <g class="tbadge" style="transition-delay:2.2s">
        <rect x="500" y="223" width="34" height="20" rx="6" fill="rgba(242,184,75,.15)" stroke="#F2B84B"/>
        <text x="517" y="237" text-anchor="middle" font-family="JetBrains Mono" font-size="11" fill="#F2B84B">×5</text>
      </g>

      <g class="tresult" style="transition-delay:2.45s">
        <line x1="500" y1="156" x2="560" y2="156" stroke="#2b3145" stroke-width="2"/>
        <text x="570" y="151" font-family="JetBrains Mono" font-size="12" fill="#8992A6">125×125</text>
        <text x="570" y="168" font-family="JetBrains Mono" font-size="13" fill="#E9EDF4">= 15,625</text>
      </g>

      <g class="tresult" style="transition-delay:2.75s">
        <line x1="500" y1="44" x2="620" y2="44" stroke="#2b3145" stroke-width="2"/>
        <text x="630" y="38" font-family="JetBrains Mono" font-size="12" fill="#8992A6">15625² × 5</text>
        <text x="630" y="56" font-family="JetBrains Mono" font-size="14" fill="#4FD8C4" font-weight="700">= 1,220,703,125</text>
      </g>
      <g class="tbadge" style="transition-delay:2.8s">
        <rect x="500" y="21" width="34" height="20" rx="6" fill="rgba(242,184,75,.15)" stroke="#F2B84B"/>
        <text x="517" y="35" text-anchor="middle" font-family="JetBrains Mono" font-size="11" fill="#F2B84B">×5</text>
      </g>

      <!-- side labels -->
      <text x="180" y="450" font-family="JetBrains Mono" font-size="11" fill="#454C5F">↓ exponent halves</text>
      <text x="700" y="450" font-family="JetBrains Mono" font-size="11" fill="#454C5F" text-anchor="end">result squares back up ↑</text>
    </svg>
  </div>
</section>

<!-- ================= 5. CODE WALKTHROUGH ================= -->
<section id="code">
  <div class="eyebrow reveal">05 — The Code</div>
  <h2 class="reveal">Line by line</h2>
  <p class="lead reveal">Click a step to see exactly what it does in the source.</p>

  <div class="code-layout reveal">
    <div class="code-panel">
      <div class="head"><span class="dot" style="background:#FF6159;"></span><span class="dot" style="background:#FFBD2E;"></span><span class="dot" style="background:#28C840;"></span>&nbsp; solution.cpp</div>
      <pre><span class="line"><span class="gutter">1</span><span class="kw">class</span> Solution {</span><span class="line"><span class="gutter">2</span><span class="kw">public</span>:</span><span class="line"><span class="gutter">3</span>    <span class="kw">const</span> <span class="tp">int</span> M = <span class="num">1e9</span> + <span class="num">7</span>;</span><span class="line"><span class="gutter">4</span>    <span class="tp">int</span> <span class="fn">findPower</span>(<span class="tp">long long</span> a, <span class="tp">long</span> b) {</span><span class="line"><span class="gutter">5</span>        <span class="tok" data-line="5"><span class="kw">if</span>(b == <span class="num">0</span>){</span></span><span class="line"><span class="gutter">6</span>            <span class="tok" data-line="5"><span class="kw">return</span> <span class="num">1</span>;</span></span><span class="line"><span class="gutter">7</span>        }</span><span class="line"><span class="gutter">8</span>        <span class="tp">long long</span> half = <span class="tok" data-line="8"><span class="fn">findPower</span>(a, b/<span class="num">2</span>)</span>;</span><span class="line"><span class="gutter">9</span>        <span class="tp">long long</span> result = <span class="tok" data-line="9">(half * half) % M</span>;</span><span class="line"><span class="gutter">10</span>        <span class="tok" data-line="10"><span class="kw">if</span>(b % <span class="num">2</span> == <span class="num">1</span>){</span></span><span class="line"><span class="gutter">11</span>            <span class="tok" data-line="10">result = (result*a) % M;</span></span><span class="line"><span class="gutter">12</span>        }</span><span class="line"><span class="gutter">13</span>       <span class="tok" data-line="13"><span class="kw">return</span> result;</span></span><span class="line"><span class="gutter">14</span>    }</span><span class="line"><span class="gutter">15</span>    <span class="tp">int</span> <span class="fn">countGoodNumbers</span>(<span class="tp">long long</span> n) {</span><span class="line"><span class="gutter">16</span>        <span class="kw">return</span> (<span class="tp">long long</span>) <span class="tok" data-line="16a"><span class="fn">findPower</span>(<span class="num">5</span>, (n+<span class="num">1</span>)/<span class="num">2</span>)</span> * <span class="tok" data-line="16b"><span class="fn">findPower</span>(<span class="num">4</span>,n/<span class="num">2</span>)</span> % M;</span><span class="line"><span class="gutter">17</span>    }</span><span class="line"><span class="gutter">18</span>};</span></pre>
    </div>

    <div class="anno-panel">
      <button class="anno-btn" data-target="5">Base case</button>
      <button class="anno-btn" data-target="8">Halve the exponent</button>
      <button class="anno-btn" data-target="9">Square the half</button>
      <button class="anno-btn" data-target="10">Fix odd exponents</button>
      <button class="anno-btn" data-target="13">Return up the stack</button>
      <button class="anno-btn" data-target="16a">Count even positions</button>
      <button class="anno-btn" data-target="16b">Count odd positions</button>
      <div class="anno-desc" id="annoDesc">Click a step above to see how it maps to the code.</div>
    </div>
  </div>
</section>

<!-- ================= COMPLEXITY ================= -->
<section id="complexity">
  <div class="eyebrow reveal">06 — Complexity</div>
  <h2 class="reveal">Why it scales to n = 10¹⁵</h2>
  <div class="cards reveal">
    <div class="card">
      <div class="k">Time</div>
      <div class="v">O(log n)</div>
      <div class="d">Each recursive call halves <span class="mono">b</span>, so the call stack depth — and total work — grows logarithmically, not linearly.</div>
    </div>
    <div class="card space">
      <div class="k">Space</div>
      <div class="v">O(log n)</div>
      <div class="d">The recursion stack holds one frame per halving, mirroring the time complexity.</div>
    </div>
  </div>
</section>

<footer>
  built to explain <span style="color:#E9EDF4;">LeetCode 1922 — Count Good Numbers</span> · modular exponentiation by squaring
</footer>

<script>
// ---------- scroll reveal ----------
const io = new IntersectionObserver((entries)=>{
  entries.forEach(e=>{
    if(e.isIntersecting){
      e.target.classList.add('in');
      if(e.target.id === 'race') e.target.parentElement === document.body ? null : null;
    }
  });
},{threshold:.2});
document.querySelectorAll('.reveal').forEach(el=>io.observe(el));

// race section needs its own trigger class on the <section>
const raceSection = document.getElementById('race');
const raceIo = new IntersectionObserver((entries)=>{
  entries.forEach(e=>{ if(e.isIntersecting) raceSection.classList.add('race-in'); });
},{threshold:.4});
raceIo.observe(raceSection);

// tree section play trigger
const treeWrap = document.getElementById('treeWrap');
const treeIo = new IntersectionObserver((entries)=>{
  entries.forEach(e=>{ if(e.isIntersecting) treeWrap.classList.add('play'); });
},{threshold:.3});
treeIo.observe(treeWrap);

document.getElementById('replayBtn').addEventListener('click', ()=>{
  treeWrap.classList.remove('play');
  // force reflow so the CSS transitions restart
  void treeWrap.offsetWidth;
  requestAnimationFrame(()=> treeWrap.classList.add('play'));
});

// ---------- digit strips ----------
const evenDigits = ['0','2','4','6','8'];
const oddDigits  = ['2','3','5','7'];

function buildStrip(container, len, animate){
  container.innerHTML = '';
  for(let i=0;i<len;i++){
    const isEven = i % 2 === 0;
    const d = document.createElement('div');
    d.className = 'd ' + (isEven ? 'even' : 'odd');
    d.textContent = isEven ? evenDigits[0] : oddDigits[0];
    container.appendChild(d);
  }
  if(animate){
    setInterval(()=>{
      [...container.children].forEach((el,i)=>{
        const isEven = i % 2 === 0;
        const pool = isEven ? evenDigits : oddDigits;
        el.textContent = pool[Math.floor(Math.random()*pool.length)];
        el.style.transform = 'translateY(-4px)';
        setTimeout(()=> el.style.transform = 'translateY(0)', 180);
      });
    }, 900);
  }
}
buildStrip(document.getElementById('heroStrip'), 9, true);

// rule diagram: labeled positions 0..5
const ruleDiagram = document.getElementById('ruleDiagram');
for(let i=0;i<6;i++){
  const isEven = i % 2 === 0;
  const wrap = document.createElement('div');
  wrap.className = 'pos ' + (isEven ? 'even':'odd');
  wrap.innerHTML = `
    <div class="idx">index ${i}</div>
    <div class="cell">${isEven ? '4' : '3'}</div>
    <div class="rule">${isEven ? 'even digit' : 'prime digit'}</div>
  `;
  ruleDiagram.appendChild(wrap);
}
setInterval(()=>{
  [...ruleDiagram.querySelectorAll('.cell')].forEach((el,i)=>{
    const isEven = i % 2 === 0;
    const pool = isEven ? evenDigits : oddDigits;
    el.textContent = pool[Math.floor(Math.random()*pool.length)];
  });
}, 1100);

// ---------- code annotations ----------
const descriptions = {
  '5': "The base case. Anything raised to the power 0 is 1 — this is what stops the recursion from going forever.",
  '8': "Recurse on half the exponent. Instead of b multiplications, we only ever need one recursive call at roughly half the size — this single line is what turns O(b) into O(log b).",
  '9': "Squaring the half gives the full answer whenever b is even: (a^(b/2))² = a^b.",
  '10': "If b is odd, halving it truncates a remainder of 1, so one extra factor of a has to be multiplied back in to correct the result.",
  '13': "The computed power is handed back up to whichever call is waiting for it — this is the 'squaring back up' phase in the diagram above.",
  '16a': "5 choices per even index, and there are ⌈n/2⌉ of them — this call counts every even-index arrangement.",
  '16b': "4 choices per odd index, and there are ⌊n/2⌋ of them — this call counts every odd-index arrangement."
};

document.querySelectorAll('.anno-btn').forEach(btn=>{
  btn.addEventListener('click', ()=>{
    const target = btn.dataset.target;
    document.querySelectorAll('.anno-btn').forEach(b=>b.classList.remove('active'));
    btn.classList.add('active');
    document.querySelectorAll('.tok').forEach(t=>t.classList.remove('hl'));
    document.querySelectorAll(`.tok[data-line="${target}"]`).forEach(t=>t.classList.add('hl'));
    document.getElementById('annoDesc').textContent = descriptions[target];
  });
});
</script>
</body>
</html>
