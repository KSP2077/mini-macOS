<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Mini OS</title>

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
    color: white;
    font-size: 14px;
}

/* WINDOW */
.window {
    position: absolute;
    width: 350px;
    height: 250px;
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

<!-- LOCK -->
<div id="lock">SWIPE ➜</div>

<!-- BOOT -->
<div id="boot">
    <h2>MINI OS</h2>
    <div id="users"></div>
</div>

<!-- DESKTOP -->
<div id="desktop">

<!-- APPS -->
<div id="apps">

    <div class="icon" onclick="openApp('safari')">Safari</div>
    <div class="icon" onclick="openApp('finder')">Finder</div>
    <div class="icon" onclick="openApp('notes')">Notes</div>
    <div class="icon" onclick="openApp('window1')">Fenêtre</div>

</div>

<!-- TASKBAR -->
<div id="taskbar">
    <div class="task-icon" onclick="openApp('safari')">🌐 Safari</div>
    <div class="task-icon" onclick="openApp('finder')">📁 Finder</div>
    <div class="task-icon" onclick="openApp('notes')">📝 Notes</div>
    <div class="task-icon" onclick="openApp('window1')">🪟 Window</div>

    <div style="margin-left:auto;color:white;padding-right:10px" id="time"></div>
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

/* CLOCK LOCK */
setInterval(() => {
    document.getElementById("lock").innerText =
        new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'}) + " ➜ SWIPE";
}, 1000);

/* TIME TASKBAR */
function updateTime() {
    const now = new Date();
    document.getElementById("time").innerText =
        now.toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
}
setInterval(updateTime, 1000);
updateTime();

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
        createWindow("Notes", "<textarea style='width:100%;height:160px'></textarea>");
    }

    if (app === "window1") {
        createWindow("Fenêtre", "🎉 Nouvelle fenêtre ouverte !");
    }

    /* FINDER AVEC IMAGES */
    if (app === "finder") {
        createWindow(
            "Finder",
            `
            <div style="display:grid;grid-template-columns:repeat(2,1fr);gap:10px;">
                <img src="DJI_0003.JPG" style="width:100%;border-radius:8px">
                <img src="DJI_0003.JPG" style="width:100%;border-radius:8px">
            </div>
            `
        );
    }
}

</script>

</body>
</html>
