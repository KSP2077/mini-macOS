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
    background: #222;
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 60px;
    cursor: grab;
}

/* BOOT */
#boot {
    display: none;
    position: absolute;
    width: 100%;
    height: 100%;
    background: #333;
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
    background: #1e1e1e;
    color: white;
}

/* TOPBAR */
#topbar {
    position: absolute;
    top: 0;
    width: 100%;
    height: 30px;
    background: rgba(0,0,0,0.6);
    color: white;
    text-align: right;
    padding-right: 20px;
    line-height: 30px;
}

/* ICONS */
.icon {
    width: 100px;
    height: 60px;
    background: #444;
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
    display: flex;
    justify-content: center;
    align-items: center;
}

.dock-btn {
    margin: 10px;
    padding: 10px;
    background: #555;
    cursor: pointer;
}

/* WINDOW */
.window {
    position: absolute;
    width: 400px;
    height: 300px;
    background: white;
    color: black;
    border-radius: 10px;
    padding: 10px;
}
</style>
</head>

<body>

<!-- LOCK -->
<div id="lock">SWIPE ➜</div>

<!-- BOOT -->
<div id="boot">
    <h1>MINI macOS</h1>
    <div id="users"></div>
</div>

<!-- DESKTOP -->
<div id="desktop">

<div id="topbar"></div>

<div class="icon" style="top:100px;left:50px" onclick="openApp('safari')">Safari</div>
<div class="icon" style="top:200px;left:50px" onclick="openApp('finder')">Finder</div>
<div class="icon" style="top:300px;left:50px" onclick="openApp('notes')">Notes</div>

<div id="dock">
    <div class="dock-btn" onclick="openApp('safari')">Safari</div>
    <div class="dock-btn" onclick="openApp('finder')">Finder</div>
    <div class="dock-btn" onclick="openApp('notes')">Notes</div>
</div>

</div>

<script>

/* USERS */
const users = {
    admin: "1234",
    alex: "0000",
    Enzo: "enzo",
    Theo: "1611",
    guest: null
};

const noPassword = ["guest"];

/* CLOCK */
setInterval(() => {
    document.getElementById("lock").innerText =
        new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
}, 1000);

/* TOPBAR CLOCK */
setInterval(() => {
    let t = document.getElementById("topbar");
    if (t) t.innerText = new Date().toLocaleTimeString();
}, 1000);

/* SWIPE */
let startX = 0;

document.getElementById("lock").addEventListener("mousedown", e => {
    startX = e.clientX;
});

document.getElementById("lock").addEventListener("mouseup", e => {
    if (e.clientX - startX > 150) unlock();
});

document.getElementById("lock").addEventListener("touchstart", e => {
    startX = e.touches[0].clientX;
});

document.addEventListener("touchend", e => {
    let endX = e.changedTouches[0].clientX;
    if (endX - startX > 150) unlock();
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

    if (noPassword.includes(user)) {
        desktop();
        return;
    }

    let pwd = prompt("Mot de passe pour " + user);

    if (pwd && pwd.trim() === users[user]) {
        desktop();
    } else {
        alert("Mot de passe incorrect");
    }
}

/* DESKTOP */
function desktop() {
    document.getElementById("boot").style.display = "none";
    document.getElementById("desktop").style.display = "block";
}

/* APPS */
function createWindow(title, content) {
    let w = document.createElement("div");
    w.className = "window";
    w.style.top = "100px";
    w.style.left = "200px";
    w.innerHTML = `<b>${title}</b><br>${content}`;
    document.getElementById("desktop").appendChild(w);
}

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
        createWindow("Finder", "📁 Finder OK");
    }
}

</script>

</body>
</html>
