<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Mini OS PRO</title>

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
width:100%;
height:100%;
position:absolute;
}

/* icon bureau */
.icon{
position:absolute;
width:80px;
height:80px;
cursor:grab;
}

/* window */
.window{
position:absolute;
width:420px;
height:300px;
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

</div>

</div>

<script>

const desktop=document.getElementById("desktop");

/* DRAG SIMPLE (bureau + images) */
function makeDraggable(el){

let dx=0,dy=0,drag=false;

el.addEventListener("mousedown",(e)=>{
drag=true;

const r=el.getBoundingClientRect();
dx=e.clientX-r.left;
dy=e.clientY-r.top;

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
document.removeEventListener("mousemove",move);
document.removeEventListener("mouseup",stop);
}
}

/* WINDOW */
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

/* APP FINDER */
function openFinder(){

createWindow("Finder",`
<div id="dropZone">📁 Glisse des images JPG ici</div>
<div id="gallery"></div>
`);

setupFinder();
}

/* FINDER DRAG & DROP */
function setupFinder(){

const dropZone=document.getElementById("dropZone");
const gallery=document.getElementById("gallery");

if(!dropZone || !gallery) return;

/* hover */
dropZone.addEventListener("dragover",(e)=>{
e.preventDefault();
dropZone.style.background="#222";
});

dropZone.addEventListener("dragleave",()=>{
dropZone.style.background="transparent";
});

/* drop JPG */
dropZone.addEventListener("drop",(e)=>{
e.preventDefault();
dropZone.style.background="transparent";

const files=e.dataTransfer.files;

for(let file of files){

if(file.type==="image/jpeg"){

const reader=new FileReader();

reader.onload=(ev)=>{
const img=document.createElement("img");
img.src=ev.target.result;

gallery.appendChild(img);

/* 🔥 drag vers bureau */
enableDragToDesktop(img);
};

reader.readAsDataURL(file);
}
}
});
}

/* IMAGE -> BUREAU */
function enableDragToDesktop(img){

img.draggable=true;

img.addEventListener("dragstart",(e)=>{
e.dataTransfer.setData("img",img.src);
});

/* drop sur bureau */
desktop.addEventListener("dragover",(e)=>{
e.preventDefault();
});

desktop.addEventListener("drop",(e)=>{
e.preventDefault();

const src=e.dataTransfer.getData("img");

if(src){
createDesktopImage(src,e.clientX,e.clientY);
}
});
}

/* IMAGE SUR BUREAU */
function createDesktopImage(src,x,y){

let img=document.createElement("img");

img.src=src;
img.className="icon";
img.style.left=x+"px";
img.style.top=y+"px";

desktop.appendChild(img);

makeDraggable(img);
}

/* DEMO APP */
let finderBtn=document.createElement("button");
finderBtn.innerText="Finder";
finderBtn.onclick=openFinder;
desktop.appendChild(finderBtn);

</script>

</body>
</html>
