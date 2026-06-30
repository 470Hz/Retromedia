# Retromedia
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>レトロメディア研究会</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Noto+Serif+JP:wght@400;700;900&display=swap');

*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}

body{
  background:#d8e8f4;
  min-height:100vh;
  display:flex;
  align-items:center;
  justify-content:center;
  overflow:hidden;
  position:relative;
  font-family:'Noto Serif JP',serif;
}

#tile-canvas{position:fixed;inset:0;z-index:0;}
#tile-grid{
  width:100%;height:100%;
  display:grid;
  background:#e8f0f8;
}

.tile{
  border-radius:8px;
  cursor:default;
  transition:filter .25s ease, transform .18s ease, background 1.4s ease;
  position:relative;
  overflow:hidden;
}
/* ガラス光沢 */
.tile::after{
  content:'';
  position:absolute;inset:0;
  background:linear-gradient(135deg,rgba(255,255,255,.46) 0%,rgba(255,255,255,.08) 38%,transparent 39%);
  border-radius:inherit;
  pointer-events:none;
  z-index:1;
}

.tile.c-white  {background:#ddeef8;}
.tile.c-sky    {background:#7ecaea;}
.tile.c-blue   {background:#4a8dcb;}
.tile.c-indigo {background:#3a5faa;}
.tile.c-deep   {background:#2a3d8a;}
.tile.c-lemon  {background:#f5f870!important;transition:background .4s ease,filter .25s ease,transform .18s ease;}

.tile.lit      {filter:brightness(1.3) saturate(1.2);transform:scale(1.05);z-index:4;}
.tile.lit-near {filter:brightness(1.14);z-index:3;}

/* ── 文字タイル（縦書き・1文字） ── */
.tile.text-tile{
  display:flex;align-items:center;justify-content:center;
  cursor:pointer;
  perspective:500px;
}
.tile-char-wrap{
  width:100%;height:100%;
  display:flex;align-items:center;justify-content:center;
}
.tile-char-wrap.spin-once{
  animation:spinY .65s cubic-bezier(.4,0,.2,1) forwards;
}
@keyframes spinY{
  0%  {transform:rotateY(0deg)   scale(1);}
  25% {transform:rotateY(90deg)  scale(0.7);}
  50% {transform:rotateY(180deg) scale(1);}
  75% {transform:rotateY(270deg) scale(0.7);}
  100%{transform:rotateY(360deg) scale(1);}
}
.tile-char{
  writing-mode:vertical-rl;
  text-orientation:upright;
  font-family:'Noto Serif JP',serif;
  font-weight:900;
  color:rgba(255,255,255,.96);
  text-shadow:0 1px 2px rgba(30,60,120,.5),0 0 8px rgba(30,60,120,.3);
  line-height:1;
  user-select:none;
  letter-spacing:.02em;
}

/* ── ボタンタイル（1文字のみ） ── */
.tile.btn-tile{
  cursor:pointer;
  display:flex;align-items:center;justify-content:center;
  border:2px solid rgba(255,255,255,.5);
  transition:filter .25s ease,transform .18s ease,background 1.4s ease,border-color .2s;
}
.tile.btn-tile:hover{border-color:rgba(255,255,255,.9);}

.tile.btn-tile.squish{
  animation:squishPop .5s cubic-bezier(.36,.07,.19,.97) forwards;
}
@keyframes squishPop{
  0%  {transform:scale(1,1);}
  20% {transform:scale(1.18,.82);}
  40% {transform:scale(.88,1.18);}
  60% {transform:scale(1.08,.95);}
  80% {transform:scale(.97,1.05);}
  100%{transform:scale(1,1);}
}


.btn-single-char{
  font-family:'Noto Serif JP',serif;
  font-weight:900;
  color:rgba(255,255,255,.96);
  text-shadow:0 1px 2px rgba(20,40,100,.55),0 0 6px rgba(30,60,130,.3);
  user-select:none;
  position:relative;
  z-index:2;
  line-height:1;
}



/* ── Discord タイル ── */
.discord-tile{
  cursor:pointer;
  display:flex;flex-direction:column;
  align-items:center;justify-content:center;
  gap:4px;
  border:2px solid rgba(255,255,255,.55);
  border-radius:8px;
  text-decoration:none;
  transition:filter .2s,transform .15s,border-color .2s;
}
.discord-tile:hover{filter:brightness(1.3);transform:scale(1.06);border-color:rgba(255,255,255,.95);z-index:5;}
.discord-tile svg{fill:rgba(255,255,255,.9);}
.d-label{
  font-family:'Share Tech Mono',monospace;
  color:rgba(255,255,255,.88);
  letter-spacing:.06em;
  text-shadow:0 1px 3px rgba(20,40,100,.6);
  text-align:center;line-height:1.3;
}

/* 年号タイル */
.year-tile{display:flex;align-items:center;justify-content:center;}
.y-text{
  font-family:'Share Tech Mono',monospace;
  color:rgba(255,255,255,.75);
  text-align:center;line-height:1.5;
  text-shadow:0 1px 3px rgba(20,40,100,.5);
  white-space:pre-line;
}

/* ── ヒントオーバーレイ ── */
#hint-overlay{
  position:fixed;inset:0;z-index:50;
  display:flex;align-items:flex-end;justify-content:center;
  padding-bottom:28px;
  pointer-events:none;
  transition:opacity .8s;
}
#hint-overlay.fade{opacity:0;}
#hint-bubble{
  background:rgba(8,24,60,.82);
  border:1.5px solid rgba(126,202,234,.5);
  border-radius:6px;
  padding:10px 20px;
  color:rgba(200,230,248,.9);
  font-family:'Share Tech Mono',monospace;
  font-size:12px;
  letter-spacing:.12em;
  display:flex;align-items:center;gap:10px;
  animation:bubbleIn .6s cubic-bezier(.34,1.56,.64,1) forwards;
}
@keyframes bubbleIn{
  from{transform:translateY(20px);opacity:0;}
  to  {transform:translateY(0);   opacity:1;}
}
.hint-arrow{font-size:18px;animation:arrowBounce .8s ease-in-out infinite alternate;}
@keyframes arrowBounce{from{transform:translateY(0);}to{transform:translateY(-5px);}}

/* ════════════════════════════════════════
   タオルモーダル
   ════════════════════════════════════════ */
#modal-overlay{
  display:none;position:fixed;inset:0;
  background:rgba(10,24,60,.55);
  z-index:100;
  align-items:center;justify-content:center;
  backdrop-filter:blur(3px);
}
#modal-overlay.open{display:flex;}

/* タオル本体 */
#towel-wrap{
  position:relative;
  width:min(440px,88vw);
  animation:towelUnfold .45s cubic-bezier(.34,1.4,.64,1);
  transform-origin:top center;
}
@keyframes towelUnfold{
  from{transform:scaleY(.18) translateY(-60px);opacity:0;}
  to  {transform:scaleY(1)   translateY(0);    opacity:1;}
}

/* タオル生地 */
#towel-body{
  background:#f9f8f6;
  border-radius:6px;
  overflow:hidden;
  box-shadow:
    0 8px 32px rgba(10,28,80,.38),
    0 2px 6px rgba(10,28,80,.22),
    inset 0 0 0 1px rgba(200,210,220,.6);
  /* 布目テクスチャ */
  background-image:
    repeating-linear-gradient(0deg, rgba(180,190,200,.07) 0px, rgba(180,190,200,.07) 1px, transparent 1px, transparent 4px),
    repeating-linear-gradient(90deg, rgba(180,190,200,.07) 0px, rgba(180,190,200,.07) 1px, transparent 1px, transparent 4px);
}

/* パイル（タオル上下のふさふさ縁） */
.towel-fringe{
  height:16px;
  background:repeating-linear-gradient(
    90deg,
    #e8eef5 0px, #e8eef5 5px,
    #d4dce8 5px, #d4dce8 6px,
    #e8eef5 6px, #e8eef5 11px,
    #ccd4e0 11px, #ccd4e0 12px
  );
  position:relative;
}
.towel-fringe::after{
  content:'';
  position:absolute;inset:0;
  background:repeating-linear-gradient(
    90deg,
    transparent 0px, transparent 3px,
    rgba(255,255,255,.35) 3px, rgba(255,255,255,.35) 4px
  );
}

/* タオル内部の白いパイル風帯 */
.towel-inner{
  background:#fdfdfc;
  padding:22px 28px 18px;
  /* 縦の毛足っぽい微テクスチャ */
  background-image:repeating-linear-gradient(
    90deg,
    transparent 0px,transparent 3px,
    rgba(220,228,238,.18) 3px,rgba(220,228,238,.18) 4px
  );
}

/* 刺繍帯（青いライン） */
.towel-band{
  background:#1a3a80;
  padding:12px 28px 14px;
  display:flex;
  flex-direction:column;
  align-items:center;
  gap:3px;
  border-top:3px solid #2a5abf;
  border-bottom:3px solid #2a5abf;
  position:relative;
}
/* 刺繍帯 上下の細い装飾ライン */
.towel-band::before,.towel-band::after{
  content:'';
  position:absolute;left:0;right:0;
  height:1px;
  background:repeating-linear-gradient(90deg,
    rgba(180,210,255,.0) 0px,rgba(180,210,255,.0) 3px,
    rgba(180,210,255,.35) 3px,rgba(180,210,255,.35) 6px
  );
}
.towel-band::before{top:4px;}
.towel-band::after{bottom:4px;}

/* 刺繍小文字（住所・説明行） */
.emb-small{
  font-family:'Noto Serif JP',serif;
  font-size:10px;
  color:rgba(160,195,255,.72);
  letter-spacing:.28em;
  text-align:center;
}
/* 刺繍中文字 eyebrow */
.emb-mid{
  font-family:'Noto Serif JP',serif;
  font-size:12px;
  color:rgba(190,215,255,.82);
  letter-spacing:.22em;
  text-align:center;
}
/* 刺繍大文字（メインタイトル） */
.emb-main{
  font-family:'Noto Serif JP',serif;
  font-weight:900;
  font-size:22px;
  color:#e0eaff;
  letter-spacing:.22em;
  text-align:center;
  text-shadow:0 1px 0 rgba(0,0,60,.6),0 0 12px rgba(100,150,255,.3);
  line-height:1.25;
}
/* 刺繍最小テキスト */
.emb-addr{
  font-family:'Noto Serif JP',serif;
  font-size:9px;
  color:rgba(130,170,240,.6);
  letter-spacing:.16em;
  text-align:center;
  line-height:1.7;
}

/* タオル本文エリア */
.towel-text-area{
  padding:18px 28px 12px;
  background:#fdfdfc;
  background-image:repeating-linear-gradient(
    90deg,
    transparent 0px,transparent 3px,
    rgba(220,228,238,.15) 3px,rgba(220,228,238,.15) 4px
  );
}
/* セクションラベル（英字小見出し） */
.towel-section-label{
  font-family:'Share Tech Mono',monospace;
  font-size:9px;
  letter-spacing:.32em;
  color:#8899bb;
  margin-bottom:10px;
  padding-bottom:6px;
  border-bottom:1px solid #d8e4f0;
}
.towel-text-body{
  font-family:'Noto Serif JP',serif;
  font-size:13px;
  line-height:2.1;
  color:#2c3a52;
  text-align:justify;
}
.towel-text-body .ac{
  color:#1a3a80;
  font-weight:700;
}

/* 閉じるボタン */
#towel-close-row{
  padding:10px 28px 14px;
  display:flex;
  justify-content:flex-end;
  background:#fdfdfc;
}
#towel-close{
  background:transparent;
  border:1.5px solid #3a5faa;
  border-radius:4px;
  color:#1a3a80;
  padding:6px 20px;
  font-family:'Noto Serif JP',serif;
  font-size:12px;
  letter-spacing:.18em;
  cursor:pointer;
  transition:background .15s,color .15s;
}
#towel-close:hover{background:#1a3a80;color:#fff;}
</style>
</head>
<body>

<div id="tile-canvas">
  <div id="tile-grid"></div>
</div>

<div id="hint-overlay">
  <div id="hint-bubble">
    <span class="hint-arrow">↑</span>
    タイルをクリックしてみよう
  </div>
</div>

<!-- タオルモーダル -->
<div id="modal-overlay">
  <div id="towel-wrap">
    <div id="towel-body">
      <div class="towel-fringe"></div>
      <div class="towel-inner">
        <!-- 本文エリア（上） -->
        <div class="towel-text-area">
          <div class="towel-section-label" id="emb-sub">ABOUT US</div>
          <p class="towel-text-body" id="modal-body"></p>
        </div>
        <!-- 刺繍帯（下） -->
        <div class="towel-band">
          <span class="emb-addr">平成三十八年　六月創設　インターネット支部</span>
          <span class="emb-mid" id="emb-eyebrow">温泉　レトロメディア研究会</span>
          <span class="emb-main" id="modal-title">レトロメディア研究会</span>
          <span class="emb-small">民宿・お食事処　むらぎ染み入る島の宿</span>
          <span class="emb-addr">RETRO MEDIA LABORATORY　Discord 班　随時入会受付中</span>
        </div>
        <div id="towel-close-row">
          <button id="towel-close">× 閉じる</button>
        </div>
      </div>
      <div class="towel-fringe"></div>
    </div>
  </div>
</div>

<script>
/* ════════════════════════════════════════════════
   水玉アニメーション
   ・左上→右下へ流れる
   ・水滴同士が近づくとつながり（メタボール的な橋）
   ・マウスが触れると弾き飛ぶ（慣性あり）
   ════════════════════════════════════════════════ */
(function(){
  const cvs = document.getElementById('water-canvas');
  const ctx = cvs.getContext('2d');
  let W, H, drops = [], mouse = {x:-999, y:-999};
  const N = 22;          // 水滴数
  const REPEL = 90;      // マウス反発半径
  const CONNECT = 72;    // 連結半径
  const FLOW_ANGLE = Math.PI * 0.28; // 斜め方向（左上→右下）
  const BASE_SPEED = 0.38;

  function resize(){
    W = cvs.width  = window.innerWidth;
    H = cvs.height = window.innerHeight;
  }
  resize();
  window.addEventListener('resize', resize);

  // マウス追跡（tile-canvasの上にcanvasがあるのでそちらに委譲、canvasはpointer-events:none）
  window.addEventListener('mousemove', e => { mouse.x = e.clientX; mouse.y = e.clientY; });
  window.addEventListener('mouseleave', () => { mouse.x = -999; mouse.y = -999; });

  function makeDrop(x, y){
    const angle = FLOW_ANGLE + (Math.random()-.5)*.45;
    const spd   = BASE_SPEED + Math.random()*.28;
    const r     = 7 + Math.random()*9;
    return {
      x: x ?? Math.random()*W,
      y: y ?? Math.random()*H,
      vx: Math.cos(angle)*spd,
      vy: Math.sin(angle)*spd,
      r,
      base_r: r,
      alpha: .55 + Math.random()*.3,
      phase: Math.random()*Math.PI*2,  // 揺らぎ位相
      wobble: .6 + Math.random()*.8,   // 揺らぎ速度
    };
  }

  // 初期配置：画面全体にランダムに散らす
  for(let i=0;i<N;i++) drops.push(makeDrop());

  /* ── 描画：水滴1つ ── */
  function drawDrop(d, t){
    const wobbleAmt = 0.08;
    const squeeze = 1 + Math.sin(t * d.wobble + d.phase) * wobbleAmt;
    const rx = d.r * (1/squeeze);
    const ry = d.r * squeeze;

    ctx.save();
    ctx.translate(d.x, d.y);
    ctx.rotate(FLOW_ANGLE);

    // 外側グロー（水らしい輝き）
    const glow = ctx.createRadialGradient(0,0,rx*.1, 0,0,rx*1.7);
    glow.addColorStop(0,   `rgba(180,220,255,${d.alpha*.18})`);
    glow.addColorStop(0.6, `rgba(120,180,240,${d.alpha*.07})`);
    glow.addColorStop(1,   `rgba(80,140,200,0)`);
    ctx.beginPath();
    ctx.ellipse(0,0,rx*1.7,ry*1.7,0,0,Math.PI*2);
    ctx.fillStyle = glow;
    ctx.fill();

    // 本体グラデーション（ガラス玉風）
    const grad = ctx.createRadialGradient(-rx*.28,-ry*.28,rx*.04, rx*.1,ry*.1,rx*1.05);
    grad.addColorStop(0,   `rgba(240,250,255,${d.alpha*.82})`);
    grad.addColorStop(0.22,`rgba(180,218,252,${d.alpha*.72})`);
    grad.addColorStop(0.6, `rgba(90,160,230,${d.alpha*.65})`);
    grad.addColorStop(0.88,`rgba(50,110,200,${d.alpha*.58})`);
    grad.addColorStop(1,   `rgba(30,80,170,${d.alpha*.45})`);
    ctx.beginPath();
    ctx.ellipse(0,0,rx,ry,0,0,Math.PI*2);
    ctx.fillStyle = grad;
    ctx.fill();

    // ハイライト（左上の白い光）
    const hl = ctx.createRadialGradient(-rx*.32,-ry*.36,0, -rx*.18,-ry*.2,rx*.46);
    hl.addColorStop(0,   `rgba(255,255,255,${d.alpha*.88})`);
    hl.addColorStop(0.5, `rgba(220,240,255,${d.alpha*.3})`);
    hl.addColorStop(1,   `rgba(200,230,255,0)`);
    ctx.beginPath();
    ctx.ellipse(-rx*.22,-ry*.26, rx*.34,ry*.28,0,0,Math.PI*2);
    ctx.fillStyle = hl;
    ctx.fill();

    // 下端の小さい反射光
    const lr = ctx.createRadialGradient(rx*.2,ry*.36,0, rx*.2,ry*.36,rx*.28);
    lr.addColorStop(0,   `rgba(180,220,255,${d.alpha*.35})`);
    lr.addColorStop(1,   `rgba(180,220,255,0)`);
    ctx.beginPath();
    ctx.ellipse(rx*.2,ry*.36,rx*.22,ry*.16,0,0,Math.PI*2);
    ctx.fillStyle = lr;
    ctx.fill();

    ctx.restore();
  }

  /* ── 描画：水の橋（2滴が近いとき） ── */
  function drawBridge(a, b){
    const dx = b.x - a.x, dy = b.y - a.y;
    const dist = Math.sqrt(dx*dx + dy*dy);
    if(dist > CONNECT + a.r + b.r) return;
    const t = Math.max(0, 1 - dist / (CONNECT + a.r + b.r));
    if(t < .01) return;

    const alpha = t * .38;
    const nx = -dy/dist, ny = dx/dist; // 法線
    const w  = Math.min(a.r, b.r) * t * .72;

    // ベジェ曲線で橋を描く
    const mid = { x:(a.x+b.x)*.5, y:(a.y+b.y)*.5 };
    const bend = t * 4; // 橋のふくらみ

    ctx.beginPath();
    ctx.moveTo(a.x + nx*w,  a.y + ny*w);
    ctx.quadraticCurveTo(mid.x + nx*(w+bend), mid.y + ny*(w+bend), b.x + nx*w, b.y + ny*w);
    ctx.lineTo(b.x - nx*w,  b.y - ny*w);
    ctx.quadraticCurveTo(mid.x - nx*(w+bend), mid.y - ny*(w+bend), a.x - nx*w, a.y - ny*w);
    ctx.closePath();

    const bg = ctx.createLinearGradient(a.x,a.y,b.x,b.y);
    bg.addColorStop(0,   `rgba(120,180,240,${alpha})`);
    bg.addColorStop(0.5, `rgba(160,210,252,${alpha*1.3})`);
    bg.addColorStop(1,   `rgba(120,180,240,${alpha})`);
    ctx.fillStyle = bg;
    ctx.fill();
  }

  let last = 0;
  function frame(ts){
    const t = ts * .001;
    const dt = Math.min(ts - last, 32);
    last = ts;

    ctx.clearRect(0, 0, W, H);

    drops.forEach(d => {
      // マウス反発
      const mdx = d.x - mouse.x, mdy = d.y - mouse.y;
      const md  = Math.sqrt(mdx*mdx + mdy*mdy);
      if(md < REPEL && md > .1){
        const f = (1 - md/REPEL) * 3.8;
        d.vx += (mdx/md) * f * dt*.01;
        d.vy += (mdy/md) * f * dt*.01;
      }

      // 速度を流れ方向に引き戻す（ゆっくり）
      const target_vx = Math.cos(FLOW_ANGLE) * BASE_SPEED;
      const target_vy = Math.sin(FLOW_ANGLE) * BASE_SPEED;
      d.vx += (target_vx - d.vx) * .018;
      d.vy += (target_vy - d.vy) * .018;

      // 速度上限
      const spd = Math.sqrt(d.vx*d.vx + d.vy*d.vy);
      if(spd > 4){ d.vx = d.vx/spd*4; d.vy = d.vy/spd*4; }

      d.x += d.vx * dt*.5;
      d.y += d.vy * dt*.5;

      // 画面外に出たら反対側から再登場
      const margin = d.r * 2;
      if(d.x > W + margin){ d.x = -margin; d.y = Math.random()*H; d.vx = Math.cos(FLOW_ANGLE)*BASE_SPEED; d.vy = Math.sin(FLOW_ANGLE)*BASE_SPEED; }
      if(d.y > H + margin){ d.y = -margin; d.x = Math.random()*W; d.vx = Math.cos(FLOW_ANGLE)*BASE_SPEED; d.vy = Math.sin(FLOW_ANGLE)*BASE_SPEED; }
      if(d.x < -margin*3) { d.x = W+margin; }
      if(d.y < -margin*3) { d.y = H+margin; }
    });

    // 橋を先に描く（水滴の下）
    for(let i=0;i<drops.length;i++)
      for(let j=i+1;j<drops.length;j++)
        drawBridge(drops[i], drops[j]);

    // 水滴を描く
    drops.forEach(d => drawDrop(d, t));

    requestAnimationFrame(frame);
  }
  requestAnimationFrame(frame);
})();
</script>

<script>
const GAP=4, MIN_T=46;
let COLS,ROWS,TS,tiles=[],specialCells={};

const MODALS={
  about:{
    title:'レトロメディア研究会',
    eyebrow:'温泉　研究会案内',
    sub:'ABOUT US',
    body:'<span class="ac">レトロメディア研究会</span>は、ゲーム・映像・音楽（DTM）などのデジタルメディアと、アナログ・レトロを掛け合わせたモノづくりを総合的に楽しむ研究会です。古きよきものを追求したり、レトロメディアからヒントを得て、新しいものを作ってみる活動を行っています。'
  },
  activity:{
    title:'活　動　内　容',
    eyebrow:'温泉　レトロメディア研究会',
    sub:'ACTIVITY',
    body:'<span class="ac">ゲーム</span>　レトロゲームの研究・自作ゲーム制作<br><span class="ac">映像</span>　アナログフィルム・VHS・CG映像制作<br><span class="ac">音楽（DTM）</span>　チップチューン・シンセ・宅録<br><br>月に数回のオンライン集会と、不定期の制作合宿を行っています。'
  },
  join:{
    title:'ご　参　加　に　つ　い　て',
    eyebrow:'温泉　入会案内',
    sub:'JOIN US',
    body:'<span class="ac">平成38年（2026年）6月</span>に創設されたばかりの新しい研究会です。メンバーは随時募集中。ジャンルを問わず、レトロとデジタルに興味のある方ならどなたでも歓迎します。まずは Discord でお気軽にどうぞ。'
  }
};

function openModal(key){
  const m=MODALS[key]; if(!m)return;
  document.getElementById('emb-eyebrow').textContent=m.eyebrow;
  document.getElementById('modal-title').textContent=m.title;
  document.getElementById('emb-sub').textContent=m.sub;
  document.getElementById('modal-body').innerHTML=m.body;
  // タオルを毎回アニメーション再実行
  const tw=document.getElementById('towel-wrap');
  tw.style.animation='none'; void tw.offsetWidth;
  tw.style.animation='';
  document.getElementById('modal-overlay').classList.add('open');
}
document.getElementById('towel-close').addEventListener('click',()=>{
  document.getElementById('modal-overlay').classList.remove('open');
});
document.getElementById('modal-overlay').addEventListener('click',e=>{
  if(e.target===document.getElementById('modal-overlay'))
    document.getElementById('modal-overlay').classList.remove('open');
});

// ── ボタン定義（1文字ラベル） ──
const BTNS=[
  {char:'紹',key:'about'},
  {char:'動',key:'activity'},
  {char:'入',key:'join'},
];

const COLOR_POOL=[
  {cls:'c-white',w:18},{cls:'c-sky',w:24},{cls:'c-blue',w:22},
  {cls:'c-indigo',w:20},{cls:'c-deep',w:16},
];
let colorWheel=[];
COLOR_POOL.forEach(c=>{for(let i=0;i<c.w;i++)colorWheel.push(c.cls);});
function randColor(){return colorWheel[Math.floor(Math.random()*colorWheel.length)];}

const TITLE_CHARS=['レ','ト','ロ','メ','デ','ィ','ア','研','究','会'];

function calcGrid(){
  const W=window.innerWidth,H=window.innerHeight;
  COLS=Math.max(7,Math.floor((W+GAP)/(MIN_T+GAP)));
  ROWS=Math.max(7,Math.floor((H+GAP)/(MIN_T+GAP)));
  TS  =Math.floor((W-GAP*(COLS+1))/COLS);
}

let titleKeys=[],btnKeys=[];

function placeSpecials(){
  specialCells={};titleKeys=[];btnKeys=[];
  if(COLS<5||ROWS<5)return;
  const midCol=Math.floor(COLS/2);

  // タイトル縦書き
  const titleStart=Math.max(1,Math.floor((ROWS-TITLE_CHARS.length)/2));
  TITLE_CHARS.forEach((ch,i)=>{
    const r=titleStart+i;
    if(r<ROWS){
      const k=`${midCol},${r}`;
      specialCells[k]={type:'char',char:ch,idx:i};
      titleKeys.push(k);
    }
  });

  // ボタン（右側）
  const btnCol=Math.min(midCol+2,COLS-2);
  const midR=titleStart+Math.floor(TITLE_CHARS.length/2);
  const btnRows=[midR-3,midR,midR+3];
  BTNS.forEach((b,i)=>{
    const r=btnRows[i];
    if(r!==undefined&&r>=0&&r<ROWS){
      const k=`${btnCol},${r}`;
      specialCells[k]={type:'btn',...b};
      btnKeys.push(k);
    }
  });

  // Discord
  const dCol=Math.max(1,midCol-2);
  const dRow=titleStart+Math.floor(TITLE_CHARS.length/2);
  specialCells[`${dCol},${dRow}`]={type:'discord'};

  // 年号
  const yRow=dRow+2;
  if(yRow<ROWS) specialCells[`${dCol},${yRow}`]={type:'year'};


}

// タイトルスピン
let titleSpinning=false;
let titleElems=[];

function spinTitle(){
  if(titleSpinning)return;
  titleSpinning=true;
  titleElems.forEach(({wrap},i)=>{
    setTimeout(()=>{
      wrap.classList.remove('spin-once');
      void wrap.offsetWidth;
      wrap.classList.add('spin-once');
      if(i===titleElems.length-1) setTimeout(()=>{titleSpinning=false;},700);
    },i*60);
  });
}

function buildGrid(){
  const grid=document.getElementById('tile-grid');
  grid.innerHTML=''; tiles=[]; titleElems=[];

  grid.style.gridTemplateColumns=`repeat(${COLS},${TS}px)`;
  grid.style.gridTemplateRows=`repeat(${ROWS},${TS}px)`;
  grid.style.gap=GAP+'px';
  grid.style.padding=GAP+'px';
  grid.style.background='#e8f0f8';

  const fsBig  =Math.max(10,Math.min(TS*.52,28));
  const fsMid  =Math.max(9, Math.min(TS*.46,26));
  const fsSmall=Math.max(7, Math.min(TS*.26,11));

  for(let r=0;r<ROWS;r++){
    for(let c=0;c<COLS;c++){
      const key=`${c},${r}`;
      const spec=specialCells[key];
      const el=document.createElement('div');
      el.className='tile';

      if(spec){
        if(spec.type==='char'){
          el.classList.add('text-tile','c-indigo');
          el.title='クリックで回転';
          const wrap=document.createElement('div');
          wrap.className='tile-char-wrap';
          const span=document.createElement('span');
          span.className='tile-char';
          span.textContent=spec.char;
          span.style.fontSize=fsBig+'px';
          wrap.appendChild(span);
          el.appendChild(wrap);
          el.addEventListener('click',spinTitle);
          titleElems[spec.idx]={el,wrap};

        }else if(spec.type==='btn'){
          el.classList.add('btn-tile','c-blue');
          // 1文字だけ表示
          const span=document.createElement('span');
          span.className='btn-single-char';
          span.style.fontSize=fsMid+'px';
          span.textContent=spec.char;
          el.appendChild(span);
          el.dataset.modalKey=spec.key;
          el.addEventListener('click',()=>{
            el.classList.remove('squish');
            void el.offsetWidth;
            el.classList.add('squish');
            openModal(spec.key);
            dismissHint();
          });

        }else if(spec.type==='discord'){
          el.classList.add('discord-tile','c-deep');
          const span=document.createElement('span');
          span.className='btn-single-char';
          span.style.fontSize=fsMid*.88+'px';
          span.style.letterSpacing='.08em';
          span.textContent='参加';
          el.appendChild(span);
          el.addEventListener('click',()=>{window.open('https://discord.gg/','_blank');dismissHint();});

        }else if(spec.type==='year'){
          el.classList.add('year-tile','c-sky');
          const span=document.createElement('span');
          span.className='y-text';
          span.style.fontSize=fsSmall+'px';
          span.textContent='平成38年\n2026.06';
          el.appendChild(span);

        }

      }else{
        el.classList.add(randColor());
      }

      grid.appendChild(el);
      tiles.push({el,col:c,row:r,spec:!!spec});
    }
  }
}

/* マウス照明 */
let mouseCol=-1,mouseRow=-1;
document.getElementById('tile-canvas').addEventListener('mousemove',e=>{
  const x=e.clientX-GAP,y=e.clientY-GAP;
  const c=Math.floor(x/(TS+GAP)),r=Math.floor(y/(TS+GAP));
  if(c===mouseCol&&r===mouseRow)return;
  mouseCol=c;mouseRow=r;
  tiles.forEach(t=>{
    const dc=Math.abs(t.col-c),dr=Math.abs(t.row-r);
    t.el.classList.remove('lit','lit-near');
    if(dc===0&&dr===0)t.el.classList.add('lit');
    else if(dc<=1&&dr<=1)t.el.classList.add('lit-near');
  });
});
document.getElementById('tile-canvas').addEventListener('mouseleave',()=>{
  tiles.forEach(t=>t.el.classList.remove('lit','lit-near'));
});

/* レモン色 */
function schedLemon(){
  const plain=tiles.filter(t=>!t.spec);
  if(!plain.length){setTimeout(schedLemon,3000);return;}
  const t=plain[Math.floor(Math.random()*plain.length)];
  const orig=Array.from(t.el.classList).find(c=>c.startsWith('c-'));
  t.el.classList.remove(orig);t.el.classList.add('c-lemon');
  setTimeout(()=>{t.el.classList.remove('c-lemon');t.el.classList.add(orig);}
    ,1800+Math.random()*1400);
  setTimeout(schedLemon,2800+Math.random()*5200);
}

/* タイル揺らぎ */
function schedFlicker(){
  const plain=tiles.filter(t=>!t.spec&&!t.el.classList.contains('c-lemon'));
  if(plain.length){
    const t=plain[Math.floor(Math.random()*plain.length)];
    const orig=Array.from(t.el.classList).find(c=>c.startsWith('c-'));
    const next=randColor();
    if(next!==orig){
      t.el.style.transition='background 2.2s ease,filter .25s ease,transform .18s ease';
      t.el.classList.remove(orig);t.el.classList.add(next);
      setTimeout(()=>{t.el.style.transition='';},'2400');
    }
  }
  setTimeout(schedFlicker,800+Math.random()*1200);
}

let hintDismissed=false;
function dismissHint(){
  if(hintDismissed)return;
  hintDismissed=true;
  const ov=document.getElementById('hint-overlay');
  ov.classList.add('fade');
  setTimeout(()=>{ov.style.display='none';},900);
}

function init(){
  calcGrid();placeSpecials();buildGrid();
}
init();
setTimeout(schedLemon,1500);
setTimeout(schedFlicker,2000);

let resizeTimer;
window.addEventListener('resize',()=>{
  clearTimeout(resizeTimer);
  resizeTimer=setTimeout(init,200);
});
</script>
<!-- 水玉Canvas -->
<canvas id="water-canvas" style="position:fixed;inset:0;z-index:2;pointer-events:none;width:100%;height:100%;"></canvas>

</body>
</html>
