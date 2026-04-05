<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Mini macOS</title>

<style>

body {
    margin: 0;
    background: black;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    overflow: hidden;
}

/* ÉCRAN 1920x1080 */
#screen {
    width: 1920px;
    height: 1080px;
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

/* TOPBAR */
#topbar {
    position: absolute;
    top: 0;
    width: 100%;
    height: 30px;
    background: rgba(0,0,0,0.5);
    line-height: 30px;
    text-align: right;
    padding-right: 10px;
}

/* ICON */
.icon {
    width: 90px;
    height: 50px;
    background: rgba(0,0,0,0.5);
    position: absolute;
    text-align: center;
    line-height: 50px;
    cursor: pointer;
}

/* DOCK */
#dock {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 60px;
    background: rgba(0,0,0,0.5);
    display: flex;
    justify-content: center;
    align-items: center;
}

.dock-btn {
    margin: 5px;
    padding: 8px;
    background: #555;
    cursor: pointer;
}

/* WINDOW */
.window {
    position: absolute;
    width: 300px;
    height: 200px;
    background: rgba(255,255,255,0.9);
    color: black;
    padding: 10px;
    border-radius: 10px;
    box-shadow: 0 10px 30px rgba(0,0,0,0.4);
}

</style>
</head>

<body>

<div id="screen">

<!-- LOCK -->
<div id="lock">SWIPE ➜</div>

<!-- BOOT -->
<div id="boot">
    <h2>MINI macOS</h2>
    <div id="users"></div>
</div>

<!-- DESKTOP -->
<div id="desktop">

<div id="topbar"></div>

<div class="icon" style="top:80px;left:50px" onclick="openApp('safari')">Safari</div>
<div class="icon" style="top:150px;left:50px" onclick="openApp('finder')">Finder</div>
<div class="icon" style="top:220px;left:50px" onclick="openApp('notes')">Notes</div>

<div id="dock">
    <div class="dock-btn" onclick="openApp('safari')">Safari</div>
    <div class="dock-btn" onclick="openApp('finder')">Finder</div>
    <div class="dock-btn" onclick="openApp('notes')">Notes</div>
</div>

</div>

</div>

<script>

/* USERS */
const users = {
    Enzo: "enzo",
    Theo: "1611",
    guest: ""
};

/* CLOCK */
setInterval(() => {
    document.getElementById("lock").innerText =
        new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'}) + "  ➜ SWIPE";
}, 1000);

setInterval(() => {
    let t = document.getElementById("topbar");
    if (t) t.innerText = new Date().toLocaleTimeString();
}, 1000);

/* SWIPE */
let startX = 0;

document.getElementById("lock").onmousedown = e => startX = e.clientX;

document.getElementById("lock").onmouseup = e => {
    if (e.clientX - startX > 120) unlock();
};

document.getElementById("lock").ontouchstart = e => startX = e.touches[0].clientX;

document.addEventListener("touchend", e => {
    let endX = e.changedTouches[0].clientX;
    if (endX - startX > 120) unlock();
});

function unlock() {
    document.getElementById("lock").style.display = "none";
    boot();
}

/* BOOT */
function boot() {
    document.getElementById("boot").style.display = "block";

    let container = document.getElementById("users");
    container.innerHTML = "";

    for (let u in users) {
        let btn = document.createElement("button");
        btn.innerText = u;
        btn.onclick = () => login(u);
        container.appendChild(btn);
    }
}

/* LOGIN */
function login(user) {
    if (user === "guest") {
        openDesktop();
        return;
    }

    let pwd = prompt("Mot de passe pour " + user);

    if (pwd === users[user]) {
        openDesktop();
    } else {
        alert("❌ mauvais mot de passe");
    }
}

/* DESKTOP */
function openDesktop() {
    document.getElementById("boot").style.display = "none";
    document.getElementById("desktop").style.display = "block";
}

/* WINDOWS */
function createWindow(title, content) {
    let w = document.createElement("div");
    w.className = "window";
    w.style.top = "120px";
    w.style.left = "200px";
    w.innerHTML = "<b>" + title + "</b><br>" + content;
    document.getElementById("desktop").appendChild(w);
}

/* APPS */
function openApp(app) {

    if (app === "safari") {
        let url = prompt("URL ?");
        if (!url) return;
        if (!url.startsWith("http")) url = "https://" + url;
        window.open(url);
    }

    if (app === "notes") {
        createWindow("Notes", "<textarea style='width:100%;height:140px'></textarea>");
    }

    if (app === "finder") {
        createWindow("Finder", "📁 OK");
    }
}

</script>

</body>
</html>
