<!DOCTYPE html>
<html>
<head>
<title>The Hive System</title>
<style>
    body { background:#000; color:white; font-family:Arial; padding:20px; }
    h1 { text-align:center; color:#4da3ff; text-shadow:0 0 10px #0088ff; }

    .panel {
        background:#0a1a26;
        padding:20px;
        margin-top:20px;
        border-radius:10px;
        border:1px solid #1e3a5f;
        box-shadow:0 0 15px #0044ff55;
    }

    .member {
        background:#0f2438;
        padding:10px;
        margin-top:10px;
        border-radius:8px;
        border:1px solid #1c4b7f;
        font-size:18px;
    }

    button {
        background:#4da3ff;
        border:none;
        padding:10px 20px;
        font-size:16px;
        border-radius:5px;
        cursor:pointer;
        margin-top:10px;
    }
    button:hover { background:#6db3ff; }

    .remove-btn {
        float:right;
        background:red;
        color:white;
        border:none;
        padding:5px 10px;
        border-radius:4px;
        cursor:pointer;
    }
    .remove-btn:hover { background:#ff4f4f; }

    input, textarea {
        padding:10px;
        width:90%;
        border-radius:5px;
        border:none;
        font-size:16px;
        background:#0f2438;
        color:white;
    }

    textarea { height:200px; resize:vertical; }
</style>
</head>
<body>

<h1>🐝 The Hive System</h1>

<!-- LOGIN PANEL -->
<div id="loginPanel" class="panel">
    <h2>Owner Login</h2>
    <input id="username" placeholder="Username"><br><br>
    <input id="password" placeholder="Password" type="password"><br><br>
    <button onclick="login()">Login</button>
</div>

<!-- OWNER PANEL -->
<div id="ownerPanel" class="panel" style="display:none;">
    <h2>Owner Panel</h2>

    <!-- MEMBER SECTION -->
    <h3>Add Member</h3>
    <input id="memberName" placeholder="Enter member name"><br><br>
    <button onclick="addMember()">Add Member</button>

    <h3 style="margin-top:30px;">Members</h3>
    <div id="memberList"></div>

    <!-- LIVE CODE EDITOR -->
    <h3 style="margin-top:30px;">Edit Website Code</h3>
    <textarea id="editor" placeholder="Type HTML/JS/CSS here..."></textarea><br>
    <button onclick="runCode()">Run & Save Code</button>

    <br><br>
    <button onclick="logout()" style="background:#ff3333; color:white;">Logout</button>
</div>

<!-- PUBLIC VIEW -->
<div id="preview" style="margin-top:20px; border:1px solid #0044ff; padding:10px; border-radius:10px;">
    <p style="color:#6db3ff;">The Hive live code will appear here.</p>
</div>

<script>
/* --------------------------
   LOGIN SYSTEM
--------------------------- */
const CORRECT_USERNAME = "j9vr";
const CORRECT_PASSWORD = "dxhive";

function login() {
    const user = document.getElementById("username").value.trim();
    const pass = document.getElementById("password").value.trim();

    if (!user || !pass) { alert("Enter username and password."); return; }
    if (user !== CORRECT_USERNAME || pass !== CORRECT_PASSWORD) {
        alert("Incorrect username or password.");
        return;
    }

    localStorage.setItem("loggedIn", "yes");
    showOwnerPanel();
}

function logout() {
    localStorage.removeItem("loggedIn");
    location.reload();
}

function showOwnerPanel() {
    document.getElementById("loginPanel").style.display = "none";
    document.getElementById("ownerPanel").style.display = "block";
    showMembers();
    // Load saved code into editor if exists
    const saved = localStorage.getItem("siteCode");
    if (saved) document.getElementById("editor").value = saved;
}

/* Auto-login if previously logged in */
if (localStorage.getItem("loggedIn") === "yes") { showOwnerPanel(); }

/* --------------------------
   MEMBER SYSTEM
--------------------------- */
let members = JSON.parse(localStorage.getItem("members")) || [];

function showMembers() {
    const list = document.getElementById("memberList");
    list.innerHTML = "";
    members.forEach((name, i) => {
        list.innerHTML += `
            <div class="member">
                ${name}
                <button class="remove-btn" onclick="removeMember(${i})">Remove</button>
            </div>
        `;
    });
}

function addMember() {
    const name = document.getElementById("memberName").value.trim();
    if (!name) { alert("Enter a name."); return; }
    members.push(name);
    localStorage.setItem("members", JSON.stringify(members));
    showMembers();
    document.getElementById("memberName").value = "";
}

function removeMember(i) {
    members.splice(i, 1);
    localStorage.setItem("members", JSON.stringify(members));
    showMembers();
}

/* --------------------------
   LIVE CODE EDITOR
--------------------------- */
function runCode() {
    const code = document.getElementById("editor").value;
    // Save code so others can see it
    localStorage.setItem("siteCode", code);
    renderCode(code);
}

// Function to render code to preview (for everyone)
function renderCode(code) {
    const preview = document.getElementById("preview");
    preview.innerHTML = "";
    try {
        const script = document.createElement("script");
        script.type = "text/javascript";
        script.text = code;
        preview.innerHTML = code;
        preview.appendChild(script);
    } catch(e) {
        preview.innerHTML = "<p style='color:red;'>Error in code: " + e.message + "</p>";
    }
}

// On page load, render saved code for public view
window.onload = function() {
    const saved = localStorage.getItem("siteCode");
    if (saved) renderCode(saved);
}
</script>

</body>
</html>
