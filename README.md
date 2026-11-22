<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>رحلة الركشة نيالا 🛺</title>
<style>
  *{margin:0;padding:0;box-sizing:border-box;}
  body{background:#111;display:flex;justify-content:center;align-items:center;min-height:100vh;overflow:hidden;font-family:sans-serif;touch-action:none;}
  #gameWrap{position:relative;width:100%;max-width:420px;height:100vh;max-height:800px;background:#2c2c2c;overflow:hidden;}
  canvas{width:100%;height:100%;display:block;}
  #ui{position:absolute;top:0;left:0;right:0;padding:10px;display:flex;justify-content:space-between;color:#fff;font-weight:bold;z-index:10;pointer-events:none;}
  #ui span{background:rgba(0,0,0,0.6);padding:6px 12px;border-radius:20px;font-size:14px;}
  #controls{position:absolute;bottom:0;left:0;right:0;height:180px;display:flex;z-index:10;padding-bottom:20px;}
  .btn{flex:1;display:flex;justify-content:center;align-items:center;}
  .btn div{width:90px;height:90px;background:rgba(255,255,255,0.15);border:2px solid rgba(255,255,255,0.3);border-radius:50%;display:flex;justify-content:center;align-items:center;font-size:40px;color:#fff;}
  .btn:active div{background:rgba(255,235,0,0.4);transform:scale(0.92);}
  #startScreen, #gameOver{position:absolute;inset:0;background:rgba(0,0,0,0.85);display:flex;flex-direction:column;justify-content:center;align-items:center;color:#fff;z-index:20;text-align:center;padding:20px;}
  #gameOver{display:none;}
  h1{font-size:32px;margin-bottom:10px;}
  .subtitle{opacity:0.8;margin-bottom:20px;font-size:16px;}
  .playBtn{background:#ffeb00;color:#000;border:none;padding:16px 40px;font-size:20px;font-weight:bold;border-radius:50px;cursor:pointer;}
</style>
</head>
<body>
<div id="gameWrap">
  <canvas id="game" width="400" height="700"></canvas>
  <div id="ui"><span id="score">💰 0</span><span id="distance">🛣️ 0م</span></div>
  <div id="controls"><div class="btn" id="leftBtn"><div>◀</div></div><div class="btn" id="rightBtn"><div>▶</div></div></div>
  <div id="startScreen"><h1>🛺 رحلة الركشة<br>نيالا</h1><p class="subtitle">من عطبرة لنيالا<br>لم القروش، اتفادى الجمال والحفر!</p><button class="playBtn" onclick="startGame()">ابدأ الرحلة ▶</button></div>
  <div id="gameOver"><h1>انقلبت! 😵</h1><p class="subtitle" id="finalScore"></p><button class="playBtn" onclick="startGame()">حاول تاني 🔄</button></div>
</div>
<script>
const canvas=document.getElementById('game');const ctx=canvas.getContext('2d');let W=400,H=700;let gameRunning=false,score=0,dist=0,speed=4;let player={x:W/2-20,y:H-140,w:40,h:70};let obstacles=[],coins=[];let keys={left:false,right:false};
function reset(){score=0;dist=0;speed=4;player.x=W/2-20;obstacles=[];coins=[];}
function startGame(){document.getElementById('startScreen').style.display='none';document.getElementById('gameOver').style.display='none';reset();gameRunning=true;requestAnimationFrame(loop);}
function endGame(){gameRunning=false;document.getElementById('finalScore').innerHTML=`جبت <b style="color:#ffeb00">${score}</b> جنيه<br>ومشيت ${Math.floor(dist)} متر`;document.getElementById('gameOver').style.display='flex';}
function spawn(){if(Math.random()<0.04){let type=Math.random()<0.6?'camel':'hole';let x=50+Math.random()*(W-100);obstacles.push({x:x,y:-80,w:50,h:40,type:type});}if(Math.random()<0.05){let x=50+Math.random()*(W-100);coins.push({x:x,y:-30,w:30,h:30,taken:false});}}
function update(){if(keys.left)player.x-=7;if(keys.right)player.x+=7;player.x=Math.max(10,Math.min(W-player.w-10,player.x));dist+=speed*0.2;speed+=0.002;obstacles.forEach(o=>o.y+=speed);coins.forEach(c=>c.y+=speed);obstacles=obstacles.filter(o=>o.y<H+100);coins=coins.filter(c=>!c.taken&&c.y<H+100);for(let o of obstacles){if(player.x<o.x+o.w&&player.x+player.w>o.x&&player.y<o.y+o.h&&player.y+player.h>o.y){endGame();return;}}for(let c of coins){if(player.x<c.x+c.w&&player.x+player.w>c.x&&player.y<c.y+c.h&&player.y+player.h>c.y){c.taken=true;score+=10;}}document.getElementById('score').textContent='💰 '+score;document.getElementById('distance').textContent='🛣️ '+Math.floor(dist)+'م';}
function drawRoad(){ctx.fillStyle='#3d3d3d';ctx.fillRect(0,0,W,H);ctx.fillStyle='#2a2a2a';ctx.fillRect(40,0,W-80,H);ctx.strokeStyle='rgba(255,255,255,0.3)';ctx.setLineDash([20,20]);ctx.lineWidth=3;ctx.beginPath();ctx.moveTo(W/2,0);ctx.lineTo(W/2,H);ctx.stroke();ctx.setLineDash([]);ctx.fillStyle='#ffeb00';ctx.fillRect(40,0,6,H);ctx.fillRect(W-46,0,6,H);}
function drawPlayer(){ctx.font='50px serif';ctx.textAlign='center';ctx.fillText('🛺',player.x+player.w/2,player.y+player.h-5);}
function drawObstacles(){ctx.textAlign='center';obstacles.forEach(o=>{if(o.type=='camel'){ctx.font='40px serif';ctx.fillText('🐪',o.x+o.w/2,o.y+o.h);}else{ctx.fillStyle='#111';ctx.beginPath();ctx.ellipse(o.x+o.w/2,o.y+o.h/2,22,18,0,0,Math.PI*2);ctx.fill();}});}
function drawCoins(){coins.forEach(c=>{ctx.font='24px serif';ctx.fillText('💰',c.x+c.w/2,c.y+c.h);});}
function loop(){if(!gameRunning)return;spawn();update();ctx.clearRect(0,0,W,H);drawRoad();drawCoins();drawObstacles();drawPlayer();requestAnimationFrame(loop);}
const leftBtn=document.getElementById('leftBtn');const rightBtn=document.getElementById('rightBtn');
function bindBtn(el,key){el.addEventListener('touchstart',e=>{e.preventDefault();keys[key]=true;},{passive:false});el.addEventListener('touchend',e=>{e.preventDefault();keys[key]=false;},{passive:false});el.addEventListener('mousedown',()=>keys[key]=true);el.addEventListener('mouseup',()=>keys[key]=false);}
bindBtn(leftBtn,'left');bindBtn(rightBtn,'right');
canvas.addEventListener('touchstart',e=>{let x=(e.touches[0].clientX-canvas.getBoundingClientRect().left)/canvas.getBoundingClientRect().width*W;if(x<W/2){keys.left=true;keys.right=false;}else{keys.right=true;keys.left=false;}});
canvas.addEventListener('touchend',()=>{keys.left=false;keys.right=false;});
drawRoad();drawPlayer();
</script></body></html>
