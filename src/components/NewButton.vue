<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Love Letter</title>

<link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">

<style>

*{
    margin:0;
    padding:0;
    box-sizing:border-box;
}

body{
    height:100vh;
    display:flex;
    justify-content:center;
    align-items:center;
    overflow:hidden;
    font-family:'Press Start 2P', cursive;

    background:
    radial-gradient(circle at top,#fff7fb,#ffe3ec,#fff6ef);
}

/* Floating background hearts */

.bg-heart{
    position:fixed;
    bottom:-50px;
    color:#ff5f9b;
    animation:float linear infinite;
    pointer-events:none;
    opacity:.8;
}

@keyframes float{

0%{
transform:translateY(0) rotate(0deg);
opacity:0;
}

10%{
opacity:1;
}

100%{
transform:translateY(-120vh) rotate(360deg);
opacity:0;
}

}

/* Window */

.love-window{

width:430px;
border:6px solid #d97a92;
border-radius:18px;
overflow:hidden;
box-shadow:0 20px 40px rgba(0,0,0,.15);

animation:pop .6s ease;

}

@keyframes pop{

from{
transform:scale(.8);
opacity:0;
}

to{
transform:scale(1);
opacity:1;
}

}

/* Header */

.window-header{

background:#f8b8ca;
padding:15px 18px;

display:flex;
justify-content:space-between;
align-items:center;

border-bottom:5px solid #d97a92;

}

.window-header span{

font-size:14px;
color:#74253f;

}

.window-buttons{

display:flex;
gap:8px;

}

.window-buttons button{

width:28px;
height:28px;

background:#fff4f7;

border:3px solid #d97a92;

font-family:inherit;

cursor:pointer;

transition:.2s;

}

.window-buttons button:hover{

background:#ffd6e2;
transform:scale(1.1);

}

/* Heart bar */

.window-nav{

background:#ffe9f0;
padding:15px;
text-align:center;
color:#ef4778;
font-size:18px;

border-bottom:5px solid #d97a92;

}

/* Body */

.window-body{

padding:35px;

text-align:center;

background:
linear-gradient(45deg,#f7bfd0 25%,transparent 25%),
linear-gradient(-45deg,#f7bfd0 25%,transparent 25%),
linear-gradient(45deg,transparent 75%,#f7bfd0 75%),
linear-gradient(-45deg,transparent 75%,#f7bfd0 75%);

background-size:40px 40px;
background-position:0 0,0 20px,20px -20px,-20px 0;

}

.window-body h2{

font-size:18px;
margin-bottom:25px;
color:#6d1d38;

}

.pixel{

width:170px;
image-rendering:pixelated;
transition:.4s;

}

.pixel:hover{

transform:scale(1.1) rotate(-3deg);

}

.window-body p{

margin-top:25px;

font-size:10px;

line-height:2;

color:#5f3142;

}

.highlight{

background:#3a7cff;
color:white;

padding:3px 5px;

}

/* Cute button */

button.love{

margin-top:25px;

padding:12px 18px;

border:none;

border-radius:12px;

background:#ff5b96;

color:white;

font-family:inherit;

cursor:pointer;

transition:.3s;

}

button.love:hover{

transform:translateY(-3px) scale(1.05);

box-shadow:0 10px 20px rgba(255,80,150,.3);

}

</style>

</head>
<body>

<div class="love-window">

<div class="window-header">

<span>LOVE</span>

<div class="window-buttons">

<button>—</button>
<button>□</button>
<button>×</button>

</div>

</div>

<div class="window-nav">
❤️ • ❤️ • ❤️
</div>

<div class="window-body">

<h2>Yayyy! I love you ♡</h2>

<img class="pixel"
src="cat.png"
alt="Pixel Cat">

<p>

One lifetime of marriage,

a <span class="highlight">lifetime</span>

to go.

<br><br>

Happy Anniversary,

my love ❤️

</p>

<button class="love">
Love You 💕
</button>

</div>

</div>

<script>

/* Floating Hearts */

function createHeart(){

const heart=document.createElement("div");

heart.className="bg-heart";

heart.innerHTML="❤";

heart.style.left=Math.random()*100+"vw";

heart.style.fontSize=(15+Math.random()*35)+"px";

heart.style.animationDuration=(5+Math.random()*5)+"s";

document.body.appendChild(heart);

setTimeout(()=>heart.remove(),10000);

}

setInterval(createHeart,220);

/* Cute button effect */

document.querySelector(".love").onclick=()=>{

for(let i=0;i<20;i++){

setTimeout(createHeart,i*80);

}

};

</script>

</body>
</html>
