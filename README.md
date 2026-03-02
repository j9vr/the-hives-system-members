<!DOCTYPE html>
<html>
<head>
    <title>The Hive System Editor</title>
    <style>
        body {
            background-color: #0a0a0a;
            color: white;
            font-family: Arial;
            margin: 0;
            padding: 0;
        }
        header {
            background: #111;
            padding: 20px;
            text-align: center;
            font-size: 28px;
            font-weight: bold;
        }
        .container { padding: 20px; }

        .panel {
            background: #1a1a1a;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
        }

        .panel h2 { margin-top: 0; }
        button {
            background: #ffcc00;
            border: none;
            padding: 8px 15px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
        }
        button:hover { background: #ffe680; }

        input, textarea {
            padding: 8px;
            width: 95%;
            margin-bottom: 10px;
            border-radius: 5px;
            border: none;
            background: #262626;
            color: white;
            font-family: monospace;
        }

        .card {
            background: #262626;
            padding: 10px;
            border-radius: 8px;
            margin-bottom: 10px;
        }

        .small-btn {
            padding: 5px 10px;
            background: #ff4444;
            margin-left: 5px;
        }

        iframe {
            width: 100%;
            height: 400px;
            border: 1px solid #444;
            margin-top: 10px;
        }
    </style>
</head>
<body>

<header>🐝 THE HIVE SYSTEM EDITOR</header>

<div class="container">

    <!-- OWNER PANEL / EDITOR -->
    <div id="ownerPanel" class="panel">
        <h2>Owner Website Editor</h2>

        <h3>Edit HTML</h3>
        <textarea id="htmlEditor" rows="10" placeholder="Enter HTML here..."></textarea>
        
        <h3>Edit CSS</h3>
        <textarea id="cssEditor" rows="8" placeholder="Enter CSS here..."></textarea>
        
        <h3>Edit JavaScript</h3>
        <textarea id="jsEditor" rows="8" placeholder="Enter JS here..."></textarea>
        
        <button onclick="updateSite()">Preview/Update Site</button>
    </div>

    <!-- LIVE SITE PREVIEW -->
    <div class="panel">
        <h2>Live Site Preview</h2>
        <iframe id="previewFrame"></iframe>
    </div>

    <!-- MEMBERS -->
    <div class="panel">
        <h2>Members</h2>
        <input id="memberName" placeholder="Member name">
        <input id="memberRole" placeholder="Member role">
        <button onclick="addMember()">Add Member</button>
        <div id="memberList"></div>
    </div>

</div>

<script>
    // ---------------------
    // Members System
    // ---------------------
    if (!localStorage.members) {
        localStorage.members = JSON.stringify([
            {name: "j9vr", role: "Owner"}
        ]);
    }

    let members = JSON.parse(localStorage.members);

    function displayMembers() {
        let list = document.getElementById("memberList");
        list.innerHTML = "";
        members.forEach((m,i) => {
            list.innerHTML += `
                <div class="card">
                    <strong>${m.name}</strong> - ${m.role}
                    <button class="small-btn" onclick="editMember(${i})">Edit</button>
                    <button class="small-btn" onclick="removeMember(${i})">Remove</button>
                </div>
            `;
        });
    }

    function addMember() {
        let name = document.getElementById("memberName").value.trim();
        let role = document.getElementById("memberRole").value.trim();
        if(!name || !role) return alert("Enter name and role");
        members.push({name, role});
        saveMembers();
        displayMembers();
        document.getElementById("memberName").value = "";
        document.getElementById("memberRole").value = "";
    }

    function editMember(i) {
        let newName = prompt("New name:", members[i].name);
        let newRole = prompt("New role:", members[i].role);
        if(!newName || !newRole) return;
        members[i].name = newName;
        members[i].role = newRole;
        saveMembers();
        displayMembers();
    }

    function removeMember(i) {
        members.splice(i,1);
        saveMembers();
        displayMembers();
    }

    function saveMembers() {
        localStorage.members = JSON.stringify(members);
    }

    displayMembers();

    // ---------------------
    // Website Editor System
    // ---------------------
    const previewFrame = document.getElementById("previewFrame");

    function updateSite() {
        const html = document.getElementById("htmlEditor").value;
        const css = document.getElementById("cssEditor").value;
        const js = document.getElementById("jsEditor").value;

        const previewContent = `
            <html>
                <head>
                    <style>${css}</style>
                </head>
                <body>
                    ${html}
                    <script>${js}<\/script>
                </body>
            </html>
        `;

        previewFrame.srcdoc = previewContent;
    }

    // Auto-login owner panel (always visible)
    localStorage.setItem("loggedIn","yes");
    if(localStorage.getItem("loggedIn")==="yes"){
        document.getElementById("ownerPanel").style.display = "block";
    }
</script>

</body>
</html>
