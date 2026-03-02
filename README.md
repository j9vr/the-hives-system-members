<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>DXRP Hive System</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
    body { margin:0; font-family:Arial, sans-serif; background:#000; color:#fff; }
    .center { display:flex; flex-direction:column; justify-content:center; align-items:center; height:100vh; }
    input, button, textarea { padding:10px; margin:5px; border-radius:5px; border:none; }
    button { cursor:pointer; }
    #siteContent { padding:20px; }
    #ownerPanel {
        display:none; 
        position:fixed; 
        top:0; left:0; 
        width:100%; height:100%;
        background:rgba(0,0,0,0.9); 
        padding:20px;
        overflow:auto;
    }
    #ownerPanel textarea {
        width:100%;
        height:60vh;
        background:#111;
        color:#0f0;
        font-family:monospace;
    }
</style>
</head>
<body>

<!-- LOGIN SCREEN -->
<div id="loginScreen" class="center">
    <h1>Login</h1>
    <input id="usernameInput" type="text" placeholder="Enter username">
    <button onclick="login()">Login</button>
</div>

<!-- MAIN SITE -->
<div id="mainSite" style="display:none;">
    <div style="padding:20px; background:#111;">
        <h2>DXRP Hive System</h2>
        <button id="ownerBtn" style="display:none;" onclick="openOwnerPanel()">Owner Panel</button>
        <button onclick="logout()">Logout</button>
    </div>

    <!-- Editable website content -->
    <div id="siteContent"></div>
</div>

<!-- OWNER EDITOR -->
<div id="ownerPanel">
    <h2>Owner Website Editor</h2>
    <textarea id="editor"></textarea><br>
    <button onclick="saveChanges()">Save Changes</button>
    <button onclick="closeOwnerPanel()">Close</button>
</div>

<script>
// Default site content if none saved yet
const defaultContent = `
<h1>Welcome to The Hive</h1>
<p>This is the official DXRP Hive website.</p>
<p>Edit this using the Owner Panel.</p>
`;

// Load saved site content
document.getElementById("siteContent").innerHTML = 
    localStorage.getItem("siteContent") || defaultContent;

// Owner username (you can change it)
const OWNER = "j9vr";

function login() {
    const user = document.getElementById("usernameInput").value.trim();
    if (!user) return alert("Enter a username!");

    localStorage.setItem("currentUser", user);

    document.getElementById("loginScreen").style.display = "none";
    document.getElementById("mainSite").style.display = "block";

    if (user === OWNER) {
        document.getElementById("ownerBtn").style.display = "inline-block";
    }
}

function logout() {
    localStorage.removeItem("currentUser");
    location.reload();
}

function openOwnerPanel() {
    document.getElementById("ownerPanel").style.display = "block";
    document.getElementById("editor").value = 
        document.getElementById("siteContent").innerHTML;
}

function closeOwnerPanel() {
    document.getElementById("ownerPanel").style.display = "none";
}

function saveChanges() {
    const updated = document.getElementById("editor").value;
    localStorage.setItem("siteContent", updated);
    document.getElementById("siteContent").innerHTML = updated;
    alert("Changes saved!");
}
</script>

</body>
</html>
