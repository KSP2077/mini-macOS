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

/* ===== LOCK SCREEN ===== */
#lock {
    position: absolute;
    width: 100%;
    height: 100%;
    background: url("DJI_0003.JPG") center/cover;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    color: white;
    font-size: 60px;
    cursor: grab;
}

/* ===== BOOT ===== */
#boot {
    display: none;
    position: absolute;
    width: 100%;
    height: 100%;
    background: url("DJI_0003.JPG") center/cover;
    text-align: center;
    color: white;
}

button {
    padding: 10px;
    margin: 5px;
    cursor: pointer;
}

/* ===== DESKTOP ===== */
#desktop {
    display: none;
    position: absolute;
    width: 100%;
    height: 100%;
    background: url("DJI_0003.JPG") center/cover;
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
}
</style>
</head>

<body>

<!-- LOCK -->
<div id="lock">12:00</div>

<!-- BOOT -->
<div id="boot">
    <h1>🍎 MINI macOS</h1>
    <div id="users"></div>
</div>

<!-- DESKTOP -->
<div id="desktop">
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
// ===== USERS =====
const users = {
    admin: "1234",
    alex: "0000",
    guest: null,
    theo: "1611",
    enzo: "enzo"
};

const noPassword = ["guest"];

// ===== CLOCK =====
setInterval(() => {
    document.getElementById("lock").innerText =
        new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});
}, 1000);

// ===== SWIPE LOCK =====
let startX = 0;
document.getElementById("lock").addEventListener("mousedown", e => {
    startX = e.clientX;
});

document.addEventListener("mouseup", e => {
    if (e.clientX - startX > 150) {
        document.getElementById("lock").style.display = "none";
        boot();
    }
});

// ===== BOOT SCREEN =====
function boot() {
    document.getElementById("boot").style.display = "block";

    let html = "";
    for (let u in users) {
        html += `<button onclick="login('${u}')">${u}</button>`;
    }
    document.getElementById("users").innerHTML = html;
}

// ===== LOGIN =====
function login(user) {
    if (noPassword.includes(user)) {
        desktop();
        return;
    }

    let pwd = prompt("Mot de passe pour " + user);
    if (pwd === users[user]) {
        desktop();
    }
}

// ===== DESKTOP =====
function desktop() {
    document.getElementById("boot").style.display = "none";
    document.getElementById("desktop").style.display = "block";
}

// ===== APPS =====
function openApp(app) {
    if (app === "safari") {
        let url = prompt("URL ?");
        if (url && !url.startsWith("http")) url = "https://" + url;
        window.open(url);
    }

    if (app === "notes") {
        let win = window.open();
        win.document.write("<textarea style='width:100%;height:100%'></textarea>");
    }

    if (app === "finder") {
        alert("Finder simulé (version web simple)");
    }
}
</script>

</body>
</html>
