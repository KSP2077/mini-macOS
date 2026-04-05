<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Mini OS FULL PRO</title>

<style>
body{
margin:0;
background:black;
display:flex;
justify-content:center;
align-items:center;
height:100vh;
font-family:Arial;
}

#screen{
width:1024px;
height:720px;
position:relative;
background:url("DJI_0003.JPG") center/cover;
overflow:hidden;
}

/* LOCK */
#lock{
position:absolute;
width:100%;
height:100%;
background:rgba(0,0,0,0.7);
color:white;
display:flex;
justify-content:center;
align-items:center;
font-size:28px;
cursor:pointer;
}

/* BOOT */
#boot{
display:none;
position:absolute;
width:100%;
height:100%;
background:rgba(0,0,0,0.6);
color:white;
text-align:center;
padding-top:40px;
}

/* LOGIN */
#loginBox{
background:rgba(0,0,0,0.5);
padding:20px;
border-radius:10px;
display:inline-block;
}

/* DESKTOP */
#desktop{
display:none;
width:100%;
height:100%;
position:absolute;
}

/* ICONS */
.icon{
width:90px;
height:50px;
background:rgba(0,0,0,0.5);
color:white;
position:absolute;
display:flex;
justify-content:center;
align-items:center;
user-select:none;
cursor:grab;
}

#app1{top:80px;left:50px;}
#app2{top:150px;left:50px;}
#app3{top:220px;left:50px;}

/* TASKBAR */
#taskbar{
position:absolute;
bottom:0;
width:100%;
height:40px;
background:rgba(0,0,0,0.6);
display:flex;
align-items:center;
padding:0 10px;
color:white;
}

#time{margin-left:auto;}

/* WINDOW */
.window{
position:absolute;
width:420px;
height:320px;
background:white;
color:black;
border-radius:10px;
padding:10px;
overflow:auto;
}

/* FINDER */
#dropZone{
width:100%;
height:120px;
border:2px dashed gray;
display:flex;
justify-content:center;
align-items:center;
color:gray;
margin-bottom:10px;
}

#gallery img{
width:100%;
margin-bottom:10px;
border-radius:8px;
}
</style>
</head>

<body>

<div id="screen">

<!-- LOCK -->
<div id="lock" onclick="unlock()">🔒 Swipe pour ouvrir</div>

<!-- BOOT -->
<div id="boot">

<h2>MINI OS FULL</h2>

<div id="loginBox">
<select id="userSelect"></select><br><br>
<input type="password" id="passInput" placeholder="Mot de passe"><br><br>
<button onclick="login()">Entrer</button>
</div>

</div>

<!-- DESKTOP -->
<div id="desktop">

<div id="app1" class="icon">Finder</div>
<div id="app2" class="icon">Notes</div>
<div id="app3" class="icon">App</div>

<div id="taskbar">
<div>🍎 Start</div>
<div id="time"></div>
</div>

</div>

</div>

<script>

/* USERS */
const users={
theo:"theo",
enzo:"enzo"
};

/* LOCK */
function unlock(){
document.getElementById("lock").style.display="none";
document.getElementById("boot").style.display="block";
initUsers();
}

/* USERS INIT */
function initUsers(){
const sel=document.getElementById("userSelect");
sel.innerHTML="";

for(let u in users){
let opt=document.createElement("option");
opt.value=u;
opt.text=u;
sel.appendChild(opt);
}
}

/* LOGIN */
function login(){

let u=document.getElementById("userSelect").value;
let p=document.getElementById("passInput").value;

if(p===users[u]){
openDesktop();
}else{
alert("❌ mauvais mot de passe");
}
}

/* DESKTOP */
function openDesktop(){
document.getElementById("boot").style.display="none";
document.getElementById("desktop").style.display="block";

/* drag icons */
document.querySelectorAll(".icon").forEach(el=>{
makeDraggable(el);
});

/* double click */
document.getElementById("app1").ondblclick=()=>openApp("finder");
document.getElementById("app2").ondblclick=()=>openApp("notes");
document.getElementById("app3").ondblclick=()=>openApp("app");
}

/* DRAG */
function makeDraggable(el){

let dx=0,dy=0,drag=false;

el.onmousedown=(e)=>{
drag=true;

let r=el.getBoundingClientRect();
dx=e.clientX-r.left;
dy=e.clientY-r.top;

document.onmousemove=(e)=>{
if(!drag)return;
el.style.left=(e.clientX-dx)+"px";
el.style.top=(e.clientY-dy)+"px";
};

document.onmouseup=()=>{
drag=false;
};
};
}

/* WINDOWS */
function createWindow(title,content){

let w=document.createElement("div");
w.className="window";
w.style.top="100px";
w.style.left="200px";

w.innerHTML=`
<b>${title}</b><br><br>
${content}<br><br>
<button onclick="this.parentElement.remove()">Fermer</button>
`;

document.getElementById("desktop").appendChild(w);
}

/* APPS */
function openApp(app){

if(app==="finder"){
createWindow("Finder",`
<div id="dropZone">📁 Glisse JPG ici</div>
<div id="gallery"></div>
`);
setTimeout(setupFinder,100);
}

if(app==="notes"){
createWindow("Notes","<textarea style='width:100%;height:200px'></textarea>");
}

if(app==="app"){
createWindow("App","🚀 OK");
}
}

/* FINDER */
function setupFinder(){

const dropZone=document.getElementById("dropZone");
const gallery=document.getElementById("gallery");

dropZone.addEventListener("dragover",(e)=>{
e.preventDefault();
dropZone.style.background="rgba(0,0,0,0.2)";
});

dropZone.addEventListener("dragleave",()=>{
dropZone.style.background="transparent";
});

dropZone.addEventListener("drop",(e)=>{
e.preventDefault();
dropZone.style.background="transparent";

for(let file of e.dataTransfer.files){

if(file.type==="image/jpeg"){

let reader=new FileReader();

reader.onload=(ev)=>{
let img=document.createElement("img");
img.src=ev.target.result;
gallery.appendChild(img);
};

reader.readAsDataURL(file);
}
}
});
}

/* CLOCK */
setInterval(()=>{
document.getElementById("time").innerText=
new Date().toLocaleTimeString([], {hour:'2-digit',minute:'2-digit'});
},1000);

</script>

</body>
</html>
