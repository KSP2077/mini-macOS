<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Mini OS PRO</title>

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
    color: white;
}

/* APPS */
#apps {
    position: absolute;
    top: 50px;
    left: 20px;
    display: grid;
    grid-template-columns: repeat(3, 90px);
    gap: 20px;
}

.icon {
    width: 90px;
    height: 50px;
    background: rgba(0,0,0,0.5);
    text-align: center;
    line-height: 50px;
    cursor: pointer;
}

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
    <h2>MINI OS PRO</h2>
    <div id="users"></div>
</div>

<div id="desktop">

<div id="apps">
    <div class="icon" onclick="openApp('finder')">Finder</div>
    <div class="icon" onclick="openApp('notes')">Notes</div>
    <div class="icon" onclick="openApp('window1')">Fenêtre</div>
</div>

<div id="taskbar">
    <div class="task-icon" onclick="openApp('finder')">📁</div>
    <div class="task-icon" onclick="openApp('notes')">📝</div>
    <div class="task-icon" onclick="openApp('window1')">🪟</div>
    <div style="margin-left:auto" id="time"></div>
</div>

</div>

</div>

<script>

/* USERS */
const users = {Enzo:"enzo", Theo:"1611", guest:""};

/* CLOCK */
setInterval(()=>{
document.getElementById("lock").innerText =
new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})+" ➜ SWIPE";
},1000);

setInterval(()=>{
document.getElementById("time").innerText =
new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
},1000);

/* SWIPE */
let startX=0;
lock.onmousedown=e=>startX=e.clientX;
lock.onmouseup=e=>{if(e.clientX-startX>120)unlock();}
lock.ontouchstart=e=>startX=e.touches[0].clientX;
document.ontouchend=e=>{
if(e.changedTouches[0].clientX-startX>120)unlock();
};

function unlock(){lock.style.display="none";boot();}

/* BOOT */
function boot(){
boot.style.display="block";
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
if(prompt("Mot de passe")==users[u])openDesktop();
else alert("❌");
}

function openDesktop(){
boot.style.display="none";
desktop.style.display="block";
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
<span style="color:gold;cursor:pointer;font-size:18px;
filter:drop-shadow(0 0 3px gold);"
onclick="this.parentElement.parentElement.remove()">⚙️</span>
</div>
<div style="margin-top:10px">${content}</div>
`;

desktop.appendChild(w);
}

/* FINDER DRAG DROP */
function setupDrop(){
const dz=document.getElementById("dropZone");
const gal=document.getElementById("gallery");

dz.ondragover=e=>{e.preventDefault();dz.style.background="#444";}
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
img.style.borderRadius="8px";
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
Glisse images JPG ici
</div>
<div id="gallery" style="display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-top:10px"></div>
`);
setTimeout(setupDrop,100);
}

}

</script>

</body>
</html>
