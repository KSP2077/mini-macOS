<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Mini OS ULTRA FIX</title>

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

/* desktop */
#desktop{
display:none;
width:100%;
height:100%;
position:absolute;
}

/* icons */
.icon{
width:90px;
height:50px;
background:rgba(0,0,0,0.5);
color:white;
position:absolute;
display:flex;
justify-content:center;
align-items:center;
cursor:grab;
user-select:none;
}

/* positions */
#app1{top:80px;left:50px;}
#app2{top:150px;left:50px;}
#app3{top:220px;left:50px;}

/* window */
.window{
position:absolute;
width:420px;
height:320px;
background:white;
border-radius:10px;
padding:10px;
overflow:auto;
}

/* finder */
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

#gallery{
display:grid;
grid-template-columns:repeat(2,1fr);
gap:10px;
}

#gallery img{
width:100%;
border-radius:8px;
cursor:grab;
}
</style>
</head>

<body>

<div id="screen">

<div id="desktop">

<div id="app1" class="icon" onclick="openApp('finder')">Finder</div>
<div id="app2" class="icon" onclick="openApp('notes')">Notes</div>
<div id="app3" class="icon" onclick="openApp('window1')">App</div>

</div>

</div>

<script>

const desktop=document.getElementById("desktop");

/* OPEN */
desktop.style.display="block";

/* DRAG ICONS + IMAGES */
function makeDraggable(el){

let dx=0,dy=0,drag=false;

el.addEventListener("mousedown",(e)=>{
drag=true;

const rect=el.getBoundingClientRect();
dx=e.clientX-rect.left;
dy=e.clientY-rect.top;

document.addEventListener("mousemove",move);
document.addEventListener("mouseup",stop);
});

function move(e){
if(!drag) return;
el.style.left=(e.clientX-dx)+"px";
el.style.top=(e.clientY-dy)+"px";
}

function stop(){
drag=false;
savePosition(el);
document.removeEventListener("mousemove",move);
document.removeEventListener("mouseup",stop);
}
}

/* SAVE */
function savePosition(el){
if(!el.id) return;

localStorage.setItem("icon-"+el.id, JSON.stringify({
x:el.style.left||"0px",
y:el.style.top||"0px"
}));
}

/* LOAD */
function loadPositions(){
document.querySelectorAll(".icon").forEach(el=>{
const data=localStorage.getItem("icon-"+el.id);
if(data){
const pos=JSON.parse(data);
el.style.left=pos.x;
el.style.top=pos.y;
}
});
}

/* WINDOWS */
function createWindow(title,content){

let w=document.createElement("div");
w.className="window";
w.style.top="100px";
w.style.left="200px";

w.innerHTML=`
<div style="display:flex;justify-content:space-between;">
<b>${title}</b>
<span style="cursor:pointer;color:red"
onclick="this.parentElement.parentElement.remove()">✖</span>
</div>
<div style="margin-top:10px">${content}</div>
`;

desktop.appendChild(w);
}

/* APPS */
function openApp(app){

if(app==="finder"){

createWindow("Finder",`
<div id="dropZone">📁 Glisse JPG ici</div>
<div id="gallery"></div>
`);

setTimeout(setupFinder,200);
}

if(app==="notes"){
createWindow("Notes","<textarea style='width:100%;height:200px'></textarea>");
}

if(app==="window1"){
createWindow("App","OK");
}
}

/* FINDER */
function setupFinder(){

const dropZone=document.getElementById("dropZone");
const gallery=document.getElementById("gallery");

if(!dropZone || !gallery) return;

dropZone.addEventListener("dragover",(e)=>{
e.preventDefault();
dropZone.style.background="#222";
});

dropZone.addEventListener("dragleave",()=>{
dropZone.style.background="transparent";
});

dropZone.addEventListener("drop",(e)=>{
e.preventDefault();
dropZone.style.background="transparent";

for(let file of e.dataTransfer.files){

if(file.type==="image/jpeg"){

const reader=new FileReader();

reader.onload=(ev)=>{
const img=document.createElement("img");
img.src=ev.target.result;
gallery.appendChild(img);
makeDraggable(img); // drag possible
};

reader.readAsDataURL(file);
}
}
});
}

/* INIT */
document.querySelectorAll(".icon").forEach(el=>{
makeDraggable(el);
});

loadPositions();

</script>

</body>
</html>
