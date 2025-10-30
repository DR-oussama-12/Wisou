
<html lang="ar" dir="rtl">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>عيد ميلاد Wissal</title>
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  html,body{height:100%;font-family:"Segoe UI",Tahoma,Arial,sans-serif;background:#000;color:#fff}
  body{overflow:hidden;display:flex;align-items:center;justify-content:center}

  /* خلفية الصورة */
  .bg-image{
    position:fixed;inset:0;z-index:0;
    background-image:url('mp.mp4'); /* غيّر إلى mp.img أو mp.png إن لزم */
    background-size:cover;
    background-position:center;
    filter:brightness(0.6);
    pointer-events:none;
  }

  /* المرحلة */
  .stage{position:relative;z-index:2;display:flex;flex-direction:column;align-items:center;gap:18px}
  .open-btn{
    --size:120px;
    width:var(--size);height:var(--size);
    border-radius:999px;border:0;
    background:linear-gradient(135deg,#ff6b6b,#ffb86b);
    color:#fff;font-size:18px;font-weight:700;
    display:flex;align-items:center;justify-content:center;
    box-shadow:0 10px 30px rgba(0,0,0,0.35);
    cursor:pointer;transform-origin:center;transition:transform .15s ease;
  }
  .open-btn:active{transform:scale(.96)}
  .hint{font-size:14px;opacity:.9;text-align:center;text-shadow:0 2px 6px rgba(0,0,0,.6)}
  .burst-layer{position:absolute;inset:0;pointer-events:none;z-index:3}
  .burst-dot{position:absolute;width:10px;height:10px;border-radius:50%;transform:translate(-50%,-50%)}

  /* النافذة */
  .overlay{
    position:fixed;inset:0;
    background:radial-gradient(rgba(0,0,0,0.55),rgba(0,0,0,0.75));
    display:none;align-items:center;justify-content:center;z-index:10;
  }
  .overlay.show{display:flex;animation:fadeIn .4s ease both}
  @keyframes fadeIn{from{opacity:0}to{opacity:1}}

  .card{
    width:min(820px,92%);
    background:linear-gradient(180deg,rgba(255,255,255,0.06),rgba(255,255,255,0.03));
    border-radius:16px;padding:28px;
    backdrop-filter:blur(6px);
    box-shadow:0 30px 60px rgba(0,0,0,0.6);
    color:#fff;text-align:center;position:relative;overflow:hidden;
  }
  .card h1{font-size:36px;margin-bottom:8px}
  .card p{font-size:18px;opacity:.95;margin-bottom:18px}
  .close{position:absolute;top:12px;left:12px;border:0;background:transparent;color:#fff;font-size:20px;cursor:pointer}
  #confetti-canvas{position:absolute;inset:0;z-index:1;pointer-events:none}
  @media(max-width:480px){.open-btn{--size:96px;font-size:16px}.card h1{font-size:26px}.card p{font-size:16px}}
</style>
</head>
<body>

<!-- الخلفية -->
<div class="bg-image"></div>

<!-- المرحلة -->
<div class="stage">
  <div style="position:relative">
    <button id="openBtn" class="open-btn">فتح</button>
    <div id="burstLayer" class="burst-layer"></div>
  </div>
  <div class="hint">اضغط لبدء مفاجأة عيد الميلاد 🎉</div>
</div>

<!-- النافذة -->
<div class="overlay" id="overlay">
  <div class="card">
    <button class="close" id="closeBtn">✕</button>
    <canvas id="confetti-canvas"></canvas>
    <div style="position:relative;z-index:2">
      <h1>عيد ميلاد سعيد <span style="color:#ffd86b">Wissal Falosty</span> 🎂</h1>
      <p>كل عام وأنتِ أجمل ما في أيامي،
كل عام وابتسامتك تنور حياتي،
في هذا اليوم المميز، أتمنى لكِ فرحًا لا ينتهي،
وأحلامًا تتحقق واحدة تلو الأخرى،
لأنك تستحقين كل الجمال في هذا العالم �
 💖</p>
    </div>
  </div>
</div>

<!-- صوت الموسيقى -->
<audio id="music" src="mp.mp3" preload="auto"></audio>

<script>
const rand=(a,b)=>Math.random()*(b-a)+a;
const openBtn=document.getElementById('openBtn');
const burstLayer=document.getElementById('burstLayer');
const overlay=document.getElementById('overlay');
const closeBtn=document.getElementById('closeBtn');
const canvas=document.getElementById('confetti-canvas');
const ctx=canvas.getContext('2d');
const music=document.getElementById('music');

let confettiParticles=[],animId=null;

function createBurst(x,y){
  for(let i=0;i<40;i++){
    const el=document.createElement('div');
    el.className='burst-dot';
    const size=rand(6,14);
    el.style.width=el.style.height=size+'px';
    el.style.left=x+'px';el.style.top=y+'px';
    el.style.background=['#ff6b6b','#ffd86b','#6bffb8','#6bb8ff','#c86bff','#ffffff'][Math.floor(rand(0,6))];
    burstLayer.appendChild(el);
    const angle=rand(0,Math.PI*2),speed=rand(180,420);
    const vx=Math.cos(angle)*speed,vy=Math.sin(angle)*speed-rand(60,240);
    const lifetime=rand(700,1200),start=performance.now();
    function frame(now){
      const t=now-start;
      if(t>=lifetime){el.remove();return;}
      const dt=t/1000;
      const curX=x+vx*dt,curY=y+vy*dt+400*(dt*dt);
      const progress=t/lifetime;
      el.style.transform=`translate(-50%,-50%) translate(${curX-x}px,${curY-y}px) scale(${1-progress*0.6})`;
      el.style.opacity=1-progress;
      requestAnimationFrame(frame);
    }
    requestAnimationFrame(frame);
  }
}
function resizeCanvas(){
  canvas.width=canvas.offsetWidth;canvas.height=canvas.offsetHeight;
}
function makeConfettiBurst(x,y,amount=120){
  for(let i=0;i<amount;i++){
    confettiParticles.push({
      x:x+rand(-40,40),y:y+rand(-40,40),
      vx:rand(-220,220)/60,vy:rand(-500,-120)/60,
      r:rand(4,9),rot:rand(0,Math.PI*2),vr:rand(-0.12,0.12),
      color:['#ff6b6b','#ffd86b','#6bffb8','#6bb8ff','#c86bff','#ffffff'][Math.floor(rand(0,6))],
      life:rand(1600,3000),start:performance.now()
    });
  }
  if(!animId) animateConfetti();
}
function animateConfetti(){
  resizeCanvas();
  const now=performance.now();
  ctx.clearRect(0,0,canvas.width,canvas.height);
  confettiParticles=confettiParticles.filter(p=>{
    const t=now-p.start;if(t>p.life)return false;
    p.vy+=0.03;p.x+=p.vx;p.y+=p.vy;p.rot+=p.vr;
    ctx.save();ctx.translate(p.x,p.y);ctx.rotate(p.rot);
    ctx.fillStyle=p.color;ctx.fillRect(-p.r/2,-p.r/2,p.r*1.6,p.r);ctx.restore();
    return true;
  });
  if(confettiParticles.length>0){animId=requestAnimationFrame(animateConfetti);}
  else{cancelAnimationFrame(animId);animId=null;ctx.clearRect(0,0,canvas.width,canvas.height);}
}

openBtn.addEventListener('click',()=>{
  const rect=openBtn.getBoundingClientRect();
  const bx=rect.left+rect.width/2,by=rect.top+rect.height/2;
  createBurst(bx,by);
  setTimeout(()=>{
    overlay.classList.add('show');
    music.currentTime=0;
    music.play().catch(()=>{}); // تشغيل الموسيقى
    const cRect=overlay.querySelector('.card').getBoundingClientRect();
    const cx=(cRect.left+cRect.right)/2-overlay.getBoundingClientRect().left;
    const cy=(cRect.top+cRect.bottom)/2-overlay.getBoundingClientRect().top;
    makeConfettiBurst(cx,cy-20,160);
  },200);
  openBtn.style.display='none';
});

closeBtn.addEventListener('click',()=>{
  overlay.classList.remove('show');
  confettiParticles=[];
  music.pause();
});

document.addEventListener('keydown',(e)=>{
  if(e.key==='Escape'&&overlay.classList.contains('show')) closeBtn.click();
});

(function fitCanvas(){
  const card=document.querySelector('.card');
  function setCanvas(){
    const c=document.getElementById('confetti-canvas');
    c.style.width=card.clientWidth+'px';
    c.style.height=card.clientHeight+'px';
    resizeCanvas();
  }
  setCanvas();
  new ResizeObserver(setCanvas).observe(card);
})();
</script>
</body>
</html>
