<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Mini OS</title>

<style>
body {
    margin: 0;
    font-family: Arial;
    background: url("DJI_0003.JPG") center/cover;
    height: 100vh;
    overflow: hidden;
}

/* ===== ICONES ===== */
.icon {
    position: absolute;
    width: 90px;
    height: 60px;
    background: #333;
    color: white;
    text-align: center;
    line-height: 60px;
    border-radius: 10px;
    cursor: pointer;
}

/* ===== DOCK ===== */
#dock {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 60px;
    background: rgba(0,0,0,0.6);
    display: flex;
    justify-content: center;
}

.dock-btn {
    margin: 10px;
    padding: 10px;
    background: #444;
    color: white;
    border-radius: 10px;
    cursor: pointer;
}

/* ===== FENETRE ===== */
.window {
    position: absolute;
    width: 300px;
    height: 200px;
    background: white;
    border-radius: 10px;
    padding: 10px;
    top: 100px;
    left: 150px;
}
</style>
</head>

<body>

<!-- ICONES -->
<div class="icon" style="top:100px;left:50px" ondblclick="notes()">Notes</div>
<div class="icon" style="top:200px;left:50px" ondblclick="save()">Save</div>

<!-- DOCK -->
<div id="dock">
    <div class="dock-btn" onclick="notes()">🗒</div>
    <div class="dock-btn" onclick="save()">💾</div>
</div>

<script>

// ===== CREER FENETRE =====
function createWindow(content) {
    let w = document.createElement("div");
    w.className = "window";
    w.innerHTML = content;
    document.body.appendChild(w);
}

// ===== NOTES =====
function notes() {
    createWindow(`<textarea style="width:100%;height:100%"></textarea>`);
}

// ===== SAVE =====
function save() {
    createWindow(`
        Nom fichier:<br>
        <input id="name"><br><br>

        Contenu:<br>
        <textarea id="text" style="width:100%;height:60px"></textarea><br>

        <button onclick="download()">💾 Save</button>
    `);
}

// ===== TELECHARGER FICHIER =====
function download() {
    let name = document.getElementById("name").value || "file.txt";
    let text = document.getElementById("text").value;

    let blob = new Blob([text], {type: "text/plain"});
    let a = document.createElement("a");
    a.href = URL.createObjectURL(blob);
    a.download = name;
    a.click();
}

</script>

</body>
</html>
