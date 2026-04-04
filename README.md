<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Mini macOS</title>

<style>
body {
    margin: 0;
    font-family: Arial;
    overflow: hidden;
}

/* LOCK */
#lock {
    position: absolute;
    width: 100%;
    height: 100%;
    background: url("DJI_0003.JPG") center/cover;
    display: flex;
    justify-content: center;
    align-items: center;
    color: white;
    font-size: 60px;
    cursor: grab;
}

/* BOOT */
#boot {
    display: none;
    position: absolute;
    width: 100%;
    height: 100%;
    background: url("DJI_0003.JPG") center/cover;
    text-align: center;
    color: white;
}

/* DESKTOP */
#desktop {
    display: none;
    position: absolute;
    width: 100%;
    height: 100%;
    background: url("DJI_0003.JPG") center/cover;
}

/* TOP BAR */
#topbar {
    position: absolute;
    top: 0;
    width: 100%;
    height: 30px;
    background: rgba(0,0,0,0.5);
    color: white;
    text-align: right;
    padding-right: 20px;
    line-height: 30px;
}

/* ICONS */
.icon {
    width: 100px;
    height: 60px;
    background: #333;
    color: white;
    text-align: center;
    line-height: 60px;
    position: absolute;
    cursor: pointer;
}

/* DOCK */
#dock {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 70px;
    background: rgba(0,0,0,0.6);
    backdrop-filter: blur(10px);
    display: flex;
    align-items: center;
    justify-content: center;
}

.dock-btn {
    margin: 10px;
    padding: 10px;
    background: #444;
    color: white;
    cursor: pointer;
    transition: 0.2s;
}

.dock-btn:hover {
    transform: scale(1.2);
}

/* WINDOW */
.window {
    position: absolute;
    width: 400px;
    height: 300px;
    background: white;
    border-radius: 10px;
    box-shadow: 0 0 20px rgba(0,0,0,0.5);
    padding: 10px;
}
</style>
</head>

<body>

<div id="lock">12:00</div>

<div id="boot">
    <h1>🍎 MINI macOS</h1>
    <div id="users"></div>
</div>

<div id="desktop">
<div id="topbar"></div>

<div class="icon" style="top:100px;left:50px" onclick="openApp('safari')">Safari</div>
<div class="icon" style="top:200px;left:50px" onclick="openApp('finder')">Finder</div>
<div class="icon" style="top:300px;left:50px" onclick="openApp('notes')">Notes</div>

<div id="dock">
    <div class="dock-btn" onclick="openApp('safari')">🌍 Safari</div>
    <div class="dock-btn" onclick="openApp('finder')">📂 Finder</div>
    <div class="dock-btn" onclick="openApp('notes')">🗒 Notes</div>
</div>
</div>

<script>

// USERS (corrigé)
const users = {
    admin: "1234",
    alex: "0000",
    Enzo: "enzo",
    Theo: "1611",
    guest: null
};

const noPassword = ["guest"];

// CLOCK LOCK
setInterval(() => {
    document.getElementById("lock").innerText =
        new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});
}, 1000);

// TOP BAR CLOCK
setInterval(() => {
    let tb = document.getElementById("topbar");
    if (tb) tb.innerText = new Date().toLocaleTimeString();
}, 1000);

// SWIPE
let startX = 0;

document.getElementById("lock").addEventListener("mousedown", e => {
    startX = e.clientX;
});

document.getElementById("lock").addEventListener("touchstart", e => {
    startX = e.touches[0].clientX;
});

document.addEventListener("mouseup", e => {
    if (e.clientX - startX > 150) unlock();
});

document.addEventListener("touchend", e => {
    let endX = e.changedTouches[0].clientX;
    if (endX - startX > 150) unlock();
});

function unlock() {
    document.getElementById("lock").style.display = "none";
    boot();
}

// BOOT
// BOOT
function boot() {
    document.getElementById("boot").style.display = "block";

    let html = "";
    for (let u in users) {
        html += `<button onclick="login('${u}')">${u}</button>`;
    }
    document.getElementById("users").innerHTML = html;
}
// LOGIN (corrigé 100%)
function boot() {
    document.getElementById("boot").style.display = "block";

    let container = document.getElementById("users");
    container.innerHTML = "";

    for (let u in users) {
        let btn = document.createElement("button");
        btn.innerText = u;

        btn.onclick = function() {
            login(u);
        };

        container.appendChild(btn);
    }
}
// DESKTOP
function desktop() {
    document.getElementById("boot").style.display = "none";
    document.getElementById("desktop").style.display = "block";
}

// WINDOW
function createWindow(title, content) {
    let win = document.createElement("div");
    win.className = "window";
    win.style.top = "100px";
    win.style.left = "200px";

    win.innerHTML = `<b>${title}</b><br>${content}`;

    document.getElementById("desktop").appendChild(win);
}    
// APPS
function openApp(app) {

    if (app === "safari") {
        let url = prompt("URL ?");
        if (url && !url.startsWith("http")) url = "https://" + url;
        window.open(url);
    }

    if (app === "notes") {
        createWindow("Notes", "<textarea style='width:100%;height:200px'></textarea>");
    }

    if (app === "finder") {
        createWindow("Finder", "📂 Finder simulé");
    }
}

</script>

</body>
</html>
