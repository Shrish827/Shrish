<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Flappy Bird</title>
<style>
body{
    margin:0;
    display:flex;
    justify-content:center;
    align-items:center;
    height:100vh;
    background:#70c5ce;
    overflow:hidden;
}
canvas{
    border:3px solid #000;
    background:#87CEEB;
}
</style>
</head>
<body>
 
<canvas id="game" width="400" height="600"></canvas>
 
<script>
const canvas=document.getElementById("game");
const ctx=canvas.getContext("2d");
 
const bird={
    x:80,
    y:250,
    radius:15,
    velocity:0,
    gravity:0.5,
    jump:-8
};
 
const pipes=[];
const pipeWidth=60;
const gap=170;
let score=0;
let gameOver=false;
 
function createPipe(){
    const topHeight=Math.random()*250+50;
    pipes.push({
        x:canvas.width,
        top:topHeight,
        bottom:topHeight+gap,
        passed:false
    });
}
 
setInterval(()=>{
    if(!gameOver) createPipe();
},1800);
 
function drawBird(){
    ctx.beginPath();
    ctx.arc(bird.x,bird.y,bird.radius,0,Math.PI*2);
    ctx.fillStyle="yellow";
    ctx.fill();
    ctx.stroke();
}
 
function drawPipes(){
    ctx.fillStyle="green";
 
    pipes.forEach(pipe=>{
        ctx.fillRect(pipe.x,0,pipeWidth,pipe.top);
        ctx.fillRect(pipe.x,pipe.bottom,pipeWidth,
                     canvas.height-pipe.bottom);
    });
}
 
function update(){
 
    if(gameOver) return;
 
    bird.velocity+=bird.gravity;
    bird.y+=bird.velocity;
 
    pipes.forEach(pipe=>{
 
        pipe.x-=3;
 
        if(
            bird.x+bird.radius>pipe.x &&
            bird.x-bird.radius<pipe.x+pipeWidth &&
            (
                bird.y-bird.radius<pipe.top ||
                bird.y+bird.radius>pipe.bottom
            )
        ){
            gameOver=true;
        }
 
        if(!pipe.passed && pipe.x+pipeWidth<bird.x){
            pipe.passed=true;
            score++;
        }
 
    });
 
    while(pipes.length && pipes[0].x<-pipeWidth){
        pipes.shift();
    }
 
    if(
        bird.y+bird.radius>canvas.height ||
        bird.y-bird.radius<0
    ){
        gameOver=true;
    }
}
 
function draw(){
 
    ctx.clearRect(0,0,canvas.width,canvas.height);
 
    drawPipes();
    drawBird();
 
    ctx.fillStyle="black";
    ctx.font="28px Arial";
    ctx.fillText("Score: "+score,10,40);
 
    if(gameOver){
        ctx.fillStyle="red";
        ctx.font="40px Arial";
        ctx.fillText("Game Over",80,280);
        ctx.font="20px Arial";
        ctx.fillText("Press Space to Restart",70,330);
    }
}
 
function loop(){
    update();
    draw();
    requestAnimationFrame(loop);
}
 
loop();
 
function flap(){
    if(gameOver){
        restart();
        return;
    }
    bird.velocity=bird.jump;
}
 
function restart(){
    bird.y=250;
    bird.velocity=0;
    pipes.length=0;
    score=0;
    gameOver=false;
}
 
document.addEventListener("keydown",e=>{
    if(e.code==="Space"){
        flap();
    }
});
 
canvas.addEventListener("click",flap);
 
canvas.addEventListener("touchstart",e=>{
    e.preventDefault();
    flap();
});
</script>
 
</body>
</html>
