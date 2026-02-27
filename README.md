<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Loop Radio | EN VIVO 24/7</title>

<link rel="manifest" href="manifest.json">
<meta name="theme-color" content="#ff2fd2">

<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;500;700&display=swap" rel="stylesheet">

<style>
:root{
  --pink:#ff2fd2;
  --blue:#00e5ff;
  --orange:#ff8c2b;
  --bg:#12001f;
}

*{box-sizing:border-box;font-family:'Poppins',sans-serif}

body{
  margin:0;
  color:#fff;
  text-align:center;
  background:linear-gradient(120deg,#12001f,#24003d,#12001f);
  background-size:400% 400%;
  animation:bgMove 12s ease infinite;
}

@keyframes bgMove{
  0%{background-position:0% 50%}
  50%{background-position:100% 50%}
  100%{background-position:0% 50%}
}

header{
  padding:35px 20px 10px;
}

header img{
  max-width:280px;
  filter:drop-shadow(0 0 25px var(--pink));
}

/* EN VIVO */
.live{
  margin:15px auto;
  display:inline-block;
  padding:10px 24px;
  border-radius:30px;
  font-weight:700;
  letter-spacing:2px;
  color:#fff;
  background:var(--pink);
  transition:transform .1s, box-shadow .1s;
}

.logo-animado{
  max-width:420px;
  margin:10px auto 25px;
  padding:12px;
  border-radius:20px;
  background:rgba(0,0,0,.35);
  transition:transform .1s, box-shadow .1s;
}

.logo-animado video{
  width:100%;
  border-radius:14px;
  display:block;
}

main{
  padding:25px 20px;
}

.player{
  max-width:520px;
  margin:auto;
  padding:25px;
  background:rgba(0,0,0,.6);
  border-radius:22px;
  box-shadow:0 0 25px var(--blue);
}

.slogan{
  font-size:1.1rem;
  font-weight:700;
  color:var(--orange);
  letter-spacing:1px;
  text-shadow:0 0 10px var(--orange),0 0 20px var(--pink);
  margin-bottom:10px;
}

audio{width:100%;margin-top:15px}

canvas{
  width:100%;
  height:120px;
  margin-top:20px;
  filter:drop-shadow(0 0 12px var(--pink));
}

.socials{
  margin-top:35px;
}

.socials a{
  display:inline-block;
  margin:8px;
  padding:14px 22px;
  border-radius:30px;
  color:#fff;
  text-decoration:none;
  border:2px solid;
  transition:.3s;
}

.socials a:hover{transform:scale(1.1)}

.ig{border-color:var(--pink);box-shadow:0 0 12px var(--pink)}
.tk{border-color:var(--blue);box-shadow:0 0 12px var(--blue)}
.ws{border-color:#25D366;box-shadow:0 0 12px #25D366}

.retro-text{
  margin-top:14px;
  font-size:1rem;
  font-weight:700;
  letter-spacing:2px;
  color:var(--blue);
  text-shadow:0 0 8px var(--blue),0 0 16px var(--pink);
}

footer{
  margin-top:40px;
  padding:15px;
  font-size:.8rem;
  opacity:.8;
}
</style>
</head>

<body>

<header>
  <img src="logo.png" alt="Loop Radio">
  <div class="live" id="liveBtn">🔴 EN VIVO</div>
</header>

<div class="logo-animado" id="logoAnimado">
  <video autoplay loop muted playsinline>
    <source src="logo-animado.mp4" type="video/mp4">
  </video>
</div>

<main>
  <div class="player">
    <h2>Escúchanos 24/7</h2>
    <div class="slogan">Dale play al recuerdo</div>

    <audio id="audio" controls crossorigin="anonymous">
      <source src="https://radioloop.radiostream321.com/" type="audio/mpeg">
    </audio>

    <canvas id="visualizer"></canvas>
  </div>

  <div class="socials">
    <a class="ig" href="https://www.instagram.com/radio.loop.oficial/" target="_blank">Instagram</a>
    <a class="tk" href="https://www.tiktok.com/@radioloop_oficial" target="_blank">TikTok</a>
    <a class="ws" href="https://wa.me/56930029543" target="_blank">WhatsApp</a>

    <div class="retro-text">✨ Síguenos en nuestras redes sociales ✨</div>
  </div>
</main>

<footer>
  Loop Radio • Tu radio neón 24/7 • <span id="year"></span>
</footer>

<script>
document.getElementById("year").textContent=new Date().getFullYear();

const audio=document.getElementById("audio");
const canvas=document.getElementById("visualizer");
const ctx=canvas.getContext("2d");
const logo=document.getElementById("logoAnimado");
const live=document.getElementById("liveBtn");

canvas.width=canvas.offsetWidth;
canvas.height=canvas.offsetHeight;

const AudioContext=window.AudioContext||window.webkitAudioContext;
const audioCtx=new AudioContext();
const src=audioCtx.createMediaElementSource(audio);
const analyser=audioCtx.createAnalyser();

src.connect(analyser);
analyser.connect(audioCtx.destination);
analyser.fftSize=64;

const data=new Uint8Array(analyser.frequencyBinCount);

function draw(){
  requestAnimationFrame(draw);
  analyser.getByteFrequencyData(data);
  ctx.clearRect(0,0,canvas.width,canvas.height);

  let x=0;
  let sum=0;
  const w=(canvas.width/data.length)*1.5;

  data.forEach(v=>{
    sum+=v;
    const h=v/2;
    ctx.fillStyle="rgba(255,47,210,.9)";
    ctx.fillRect(x,canvas.height-h,w,h);
    x+=w+2;
  });

  const avg=sum/data.length;
  const glow=Math.min(avg*0.8,40);
  const scale=1+avg/700;

  logo.style.boxShadow=`0 0 ${glow}px rgba(255,47,210,.9),0 0 ${glow*1.6}px rgba(155,92,255,.8)`;
  logo.style.transform=`scale(${scale})`;

  live.style.boxShadow=`0 0 ${glow}px rgba(255,47,210,1)`;
  live.style.transform=`scale(${1+avg/900})`;
}

audio.addEventListener("play",()=>{
  audioCtx.resume();
  draw();
});

if("serviceWorker" in navigator){
  navigator.serviceWorker.register("sw.js");
}
</script>

</body>
</html>
