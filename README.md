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
    cursor: pointer;
    color: white;
    position: absolute;
}

/* POSITION INIT */
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

.task-icon {
    background: rgba(255,255,255,0.2);
    padding: 6px 10px;
    margin-right: 8px;
    border-radius: 6px;
    cursor: pointer;
}

/* WINDOW */
.window {
    position: absolute;
    width: 400px;
    height: 300px;
    background: rgba(255,255,255,0.95);
    color: black;
    padding: 10px;
    border-radius: 10px;
    box-shadow: 0 10px 30px rgba(0,0,0,0.4);
    overflow: auto;
}
</style>
</head>

<body>

<div id="screen">

<div id="lock">SWIPE ➜</div>

<div id="boot">
    <h2>MINI OS MAX</h2>
    <div id="users"></div>
</div>

<div id="desktop">

<div id="app1" class="icon" onclick="openApp('finder')">Finder</div>
<div id="app2" class="icon" onclick="openApp('notes')">Notes</div>
<div id="app3" class="icon" onclick="openApp('window1')">Fenêtre</div>

<div id="taskbar">
    <div class="task-icon" onclick="openApp('finder')">📁</div>
    <div class="task-icon" onclick="openApp('notes')">📝</div>
    <div class="task-icon" onclick="openApp('window1')">🪟</div>
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

lockEl.ontouchstart=e=>startX=e.touches[0].clientX;

document.addEventListener("touchend",e=>{
if(e.changedTouches[0].clientX-startX>120)unlock();
});

/* UNLOCK */
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

/* DESKTOP */
function openDesktop(){
bootEl.style.display="none";
desktopEl.style.display="block";
initDrag();
}

/* WINDOW */
function createWindow(title,content){
let w=document.createElement("div");
w.className="window";
w.style.top="120px";
w.style.left="200px";

w.innerHTML=`
<div style="display:flex;justify-content:space-between;">
<b>${title}</b>
<span style="color:gold;cursor:pointer;font-size:18px"
onclick="this.parentElement.parentElement.remove()">⚙️</span>
</div>
<div style="margin-top:10px">${content}</div>
`;

desktopEl.appendChild(w);
}

/* DRAG APPS */
function makeDraggable(el){
let offsetX=0, offsetY=0, isDown=false;

el.onmousedown=e=>{
isDown=true;
offsetX=e.clientX-el.offsetLeft;
offsetY=e.clientY-el.offsetTop;
};

document.onmousemove=e=>{
if(!isDown) return;
el.style.left=(e.clientX-offsetX)+"px";
el.style.top=(e.clientY-offsetY)+"px";
};

document.onmouseup=()=>isDown=false;
}

function initDrag(){
document.querySelectorAll(".icon")
.forEach(icon=>makeDraggable(icon));
}

/* FINDER DRAG IMAGE */
function setupDrop(){
const dz=document.getElementById("dropZone");
const gal=document.getElementById("gallery");

if(!dz) return;

dz.ondragover=e=>{e.preventDefault();dz.style.background="#444";};
dz.ondragleave=()=>dz.style.background="transparent";

dz.ondrop=e=>{
e.preventDefault();
dz.style.background="transparent";

for(let f of e.dataTransfer.files){
if(f.type.startsWith("image/")){
let r=new FileReader();
r.onload=ev=>{
let img=document.createElement("img");
img.src=ev.target.result;
img.style.width="100%";
gal.appendChild(img);
};
r.readAsDataURL(f);
}
}
};
}

/* APPS */
function openApp(app){

if(app==="notes"){
createWindow("Notes","<textarea style='width:100%;height:200px'></textarea>");
}

if(app==="window1"){
createWindow("Fenêtre","🎉 OK !");
}

if(app==="finder"){
createWindow("Finder",`
<div id="dropZone" style="border:2px dashed gray;padding:20px;text-align:center">
Glisse images ici
</div>
<div id="gallery" style="display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-top:10px"></div>
`);
setTimeout(setupDrop,200);
}

}

</script>

</body>
</html>
