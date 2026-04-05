<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Mini OS MAX</title>

<style>
body {
    margin: 0;
    background: black;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

/* ÉCRAN */
#screen {
    width: 1024px;
    height: 720px;
    position: relative;
    font-family: Arial;
    overflow: hidden;
    background: url("DJI_0003.JPG") center center / cover no-repeat;
}

/* LOCK */
#lock {
    position: absolute;
    width: 100%;
    height: 100%;
    background: rgba(0,0,0,0.6);
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 40px;
}

/* BOOT */
#boot {
    display: none;
    position: absolute;
    width: 100%;
    height: 100%;
    background: rgba(0,0,0,0.7);
    color: white;
    text-align: center;
    padding-top: 50px;
}

/* DESKTOP */
#desktop {
    display: none;
    position: absolute;
    width: 100%;
    height: 100%;
}

/* APPS */
.icon {
    width: 90px;
    height: 50px;
    background: rgba(0,0,0,0.5);
    text-align: center;
    line-height: 50px;
    color: white;
    position: absolute;
    cursor: grab;
}

/* positions */
#app1 { top:80px; left:50px; }
#app2 { top:150px; left:50px; }
#app3 { top:220px; left:50px; }

/* TASKBAR */
#taskbar {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 45px;
    background: rgba(0,0,0,0.6);
    display: flex;
    align-items: center;
    padding: 0 10px;
    color: white;
}

/* WINDOW */
.window {
    position: absolute;
    width: 400px;
    height: 300px;
    background: white;
    padding: 10px;
    border-radius: 10px;
}
</style>
</head>

<body>

<div id="screen">

<div id="lock">SWIPE ➜</div>

<div id="boot">
    <h2>MINI OS</h2>
    <div id="users"></div>
</div>

<div id="desktop">

<div id="app1" class="icon" onclick="openApp('finder')">Finder</div>
<div id="app2" class="icon" onclick="openApp('notes')">Notes</div>
<div id="app3" class="icon" onclick="openApp('window1')">Fenêtre</div>

<div id="taskbar">
    <div style="margin-left:auto" id="time"></div>
</div>

</div>

</div>

<script>

/* ELEMENTS */
const lockEl = document.getElementById("lock");
const bootEl = document.getElementById("boot");
const desktopEl = document.getElementById("desktop");
const usersDiv = document.getElementById("users");

/* USERS */
const users = {Enzo:"enzo", Theo:"1611", guest:""};

/* CLOCK */
setInterval(()=>{
lockEl.innerText =
new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})+" ➜ SWIPE";
},1000);

setInterval(()=>{
document.getElementById("time").innerText =
new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
},1000);

/* SWIPE */
let startX=0;

lockEl.onmousedown=e=>startX=e.clientX;
lockEl.onmouseup=e=>{if(e.clientX-startX>120)unlock();};

function unlock(){
lockEl.style.display="none";
boot();
}

/* BOOT */
function boot(){
bootEl.style.display="block";
usersDiv.innerHTML="";

for(let u in users){
let b=document.createElement("button");
b.innerText=u;
b.onclick=()=>login(u);
usersDiv.appendChild(b);
}
}

/* LOGIN */
function login(u){
if(u==="guest"){openDesktop();return;}
let pwd=prompt("Mot de passe");
if(pwd===users[u])openDesktop();
else alert("❌");
}

function openDesktop(){
bootEl.style.display="none";
desktopEl.style.display="block";

document.querySelectorAll(".icon")
.forEach(icon=>makeDraggable(icon));
}

/* DRAG QUI MARCHE */
function makeDraggable(el){

let offsetX=0, offsetY=0, isDragging=false;

el.addEventListener("mousedown",(e)=>{
isDragging=true;
offsetX=e.clientX-el.offsetLeft;
offsetY=e.clientY-el.offsetTop;

document.addEventListener("mousemove",move);
document.addEventListener("mouseup",stop);
});

function move(e){
if(!isDragging) return;
el.style.left=(e.clientX-offsetX)+"px";
el.style.top=(e.clientY-offsetY)+"px";
}

function stop(){
isDragging=false;
document.removeEventListener("mousemove",move);
document.removeEventListener("mouseup",stop);
}
}

/* WINDOWS */
function createWindow(title,content){
let w=document.createElement("div");
w.className="window";
w.style.top="120px";
w.style.left="200px";

w.innerHTML=`
<div style="display:flex;justify-content:space-between;">
<b>${title}</b>
<span style="color:gold;cursor:pointer"
onclick="this.parentElement.parentElement.remove()">⚙️</span>
</div>
${content}
`;

desktopEl.appendChild(w);
}

/* APPS */
function openApp(app){

if(app==="notes"){
createWindow("Notes","<textarea style='width:100%;height:200px'></textarea>");
}

if(app==="window1"){
createWindow("Fenêtre","OK !");
}

if(app==="finder"){
createWindow("Finder","Glisse images ici (option avancée)");
}

}

</script>

</body>
</html>
