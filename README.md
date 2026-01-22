<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>Flappy Spaceship</title>
<style>
html,body{
  margin:0;
  padding:0;
  background:#0b1020;
  overflow:hidden;
}
canvas{
  display:block;
  margin:0 auto;
  background:linear-gradient(#0b1020,#0e1633);
}
</style>
</head>

<body>
<canvas id="game"></canvas>

<script>
const canvas=document.getElementById("game");
const ctx=canvas.getContext("2d");

function resize(){
  canvas.width=Math.min(innerWidth,480);
  canvas.height=innerHeight;
}
addEventListener("resize",resize);
resize();

/* ---------- GAME ---------- */
let state="start";
let score=0;
let gravity=0.45;
let boost=-7.5;
let speed=2.6;
let gap=160;

const shipImg=new Image();
/* 🔴 YAHAN APNI IMAGE KA LINK YA BASE64 LAGANA 🔴 */
shipImg.src="YOUR_SPACESHIP_IMAGE.png";

const ship={x:0,y:0,w:64,h:40,vy:0};
let pipes=[];

function reset(){
  ship.x=canvas.width*0.25;
  ship.y=canvas.height/2;
  ship.vy=0;
  pipes=[];
  for(let i=0;i<4;i++){
    pipes.push({
      x:canvas.width+i*200,
      gapY:Math.random()*(canvas.height-gap-160)+80,
      scored:false
    });
  }
}

canvas.addEventListener("pointerdown",()=>{
  if(state==="start"){ reset(); state="playing"; }
  else if(state==="playing"){ ship.vy=boost; }
});

function update(){
  ship.vy+=gravity;
  ship.y+=ship.vy;

  if(ship.y<0||ship.y+ship.h>canvas.height) state="gameover";

  for(const p of pipes){
    p.x-=speed;
    if(!p.scored && p.x+60<ship.x){
      p.scored=true;
      score++;
      speed*=1.04;
      gap=Math.max(110,gap-4);
    }
    if(ship.x+ship.w>p.x && ship.x<p.x+60 &&
      (ship.y<p.gapY || ship.y+ship.h>p.gapY+gap)){
      state="gameover";
    }
  }

  if(pipes[0].x+60<0){
    pipes.shift();
    pipes.push({
      x:pipes[pipes.length-1].x+200,
      gapY:Math.random()*(canvas.height-gap-160)+80,
      scored:false
    });
  }
}

function draw(){
  ctx.clearRect(0,0,canvas.width,canvas.height);

  ctx.fillStyle="#3cff7a";
  pipes.forEach(p=>{
    ctx.fillRect(p.x,0,60,p.gapY);
    ctx.fillRect(p.x,p.gapY+gap,60,canvas.height);
  });

  ctx.save();
  ctx.translate(ship.x+ship.w/2,ship.y+ship.h/2);
  ctx.rotate(ship.vy/10);
  ctx.drawImage(shipImg,-ship.w/2,-ship.h/2,ship.w,ship.h);
  ctx.restore();

  ctx.fillStyle="#fff";
  ctx.font="20px sans-serif";
  ctx.textAlign="center";
  ctx.fillText(score,canvas.width/2,40);

  if(state==="start"){
    ctx.fillText("Tap to Start",canvas.width/2,canvas.height/2);
  }
  if(state==="gameover"){
    ctx.fillText("Game Over",canvas.width/2,canvas.height/2);
  }
}

function loop(){
  if(state==="playing") update();
  draw();
  requestAnimationFrame(loop);
}
loop();
</script>
</body>
</html>
