<!DOCTYPE html>
<html>
<head>
    <title>The Hive System</title>
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

        input {
            padding: 8px;
            width: 90%;
            margin-bottom: 10px;
            border-radius: 5px;
            border: none;
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
    </style>
</head>
<body>

<header>🐝 THE HIVE SYSTEM</header>

<div class="container">

    <!-- LOGIN -->
    <div id="loginScreen" class="panel">
        <h2>Login</h2>
        <input id="loginUser" placeholder="Enter username...">
        <button onclick="login()">Login</button>
        <br><br>
        <button onclick="showCreateAccount()">Create Account</button>
    </div>

    <!-- CREATE ACCOUNT -->
    <div id="createScreen" class="panel" style="display:none;">
        <h2>Create Account</h2>
        <input id="newUser" placeholder="New username...">
        <button onclick="createAccount()">Create</button>
        <br><br>
        <button onclick="showLogin()">Back</button>
    </div>

    <!-- MAIN SYSTEM -->
    <div id="mainSystem" style="display:none;">

        <div class="panel">
            <h2>Welcome, <span id="currentUser"></span></h2>
        </div>

        <!-- OWNER PANEL (Hidden unless j9vr logs in) -->
        <div id="ownerPanel" class="panel" style="display:none;">
            <h2>Owner Panel</h2>

            <h3>Approve Accounts</h3>
            <div id="approvalList"></div>

            <hr>

            <h3>Create Role</h3>
            <input id="roleName" placeholder="Role name...">
            <button onclick="createRole()">Add Role</button>
            <div id="roleList"></div>

            <hr>

            <h3>Add Member</h3>
            <input id="memberName" placeholder="Member username...">
            <select id="memberRole"></select>
            <button onclick="addMember()">Add Member</button>

            <div id="memberList"></div>
        </div>

        <!-- NORMAL MEMBERS VIEW -->
        <div class="panel">
            <h2>Members</h2>
            <div id="publicMemberList"></div>
        </div>

    </div>

</div>

<script>
    // Default storage
    if (!localStorage.accounts) {
        localStorage.accounts = JSON.stringify([
            { username: "j9vr", approved: true }
        ]);
    }
    if (!localStorage.roles) {
        localStorage.roles = JSON.stringify(["Owner", "Admin", "Member"]);
    }
    if (!localStorage.members) {
        localStorage.members = JSON.stringify([
            { name: "j9vr", role: "Owner" }
        ]);
    }

    let accounts = JSON.parse(localStorage.accounts);
    let roles = JSON.parse(localStorage.roles);
    let members = JSON.parse(localStorage.members);
    let currentUser = null;

    function showCreateAccount() {
        document.getElementById("loginScreen").style.display = "none";
        document.getElementById("createScreen").style.display = "block";
    }
    function showLogin() {
        document.getElementById("loginScreen").style.display = "block";
        document.getElementById("createScreen").style.display = "none";
    }

    function createAccount() {
        let name = document.getElementById("newUser").value.trim();
        if (name === "") return alert("Enter a username");

        accounts.push({ username: name, approved: false });
        localStorage.accounts = JSON.stringify(accounts);

        alert("Account created! Waiting for owner approval.");
        showLogin();
    }

    function login() {
        let name = document.getElementById("loginUser").value.trim();
        let acc = accounts.find(a => a.username === name);

        if (!acc) return alert("Account not found.");
        if (!acc.approved) return alert("Not approved yet.");

        currentUser = name;
        startSystem();
    }

    function startSystem() {
        document.getElementById("loginScreen").style.display = "none";
        document.getElementById("createScreen").style.display = "none";
        document.getElementById("mainSystem").style.display = "block";

        document.getElementById("currentUser").innerText = currentUser;

        if (currentUser === "j9vr") {
            document.getElementById("ownerPanel").style.display = "block";
            updateApprovalList();
        }

        updateRoleDropdown();
        displayRoles();
        displayMembers();
        displayPublicMembers();
    }

    // Approval System
    function updateApprovalList() {
        let list = document.getElementById("approvalList");
        list.innerHTML = "";

        accounts.forEach((acc, i) => {
            if (!acc.approved) {
                list.innerHTML += `
                    <div class="card">
                        ${acc.username}
                        <button onclick="approve(${i})">Approve</button>
                        <button class="small-btn" onclick="deny(${i})">Deny</button>
                    </div>
                `;
            }
        });
    }

    function approve(i) {
        accounts[i].approved = true;
        members.push({ name: accounts[i].username, role: "Member" });
        saveAll();
        updateApprovalList();
        displayMembers();
        displayPublicMembers();
    }

    function deny(i) {
        accounts.splice(i, 1);
        saveAll();
        updateApprovalList();
    }

    // Roles
    function updateRoleDropdown() {
        let dropdown = document.getElementById("memberRole");
        dropdown.innerHTML = "";

        roles.forEach(r => {
            dropdown.innerHTML += `<option>${r}</option>`;
        });
    }

    function displayRoles() {
        let list = document.getElementById("roleList");
        list.innerHTML = "";

        roles.forEach((role, i) => {
            list.innerHTML += `
                <div class="card">
                    ${role}
                    <button class="small-btn" onclick="deleteRole(${i})">Delete</button>
                </div>
            `;
        });
    }

    function createRole() {
        let name = document.getElementById("roleName").value.trim();
        if (name === "") return;
        roles.push(name);
        saveAll();
        displayRoles();
        updateRoleDropdown();
    }

    function deleteRole(i) {
        if (roles[i] === "Owner") return alert("Can't delete Owner role!");
        roles.splice(i, 1);
        saveAll();
        displayRoles();
        updateRoleDropdown();
    }

    // Members
    function displayMembers() {
        let list = document.getElementById("memberList");
        list.innerHTML = "";

        members.forEach((m, i) => {
            list.innerHTML += `
                <div class="card">
                    <strong>${m.name}</strong><br>
                    Role: ${m.role}<br>
                    <button class="small-btn" onclick="editMember(${i})">Edit</button>
                    <button class="small-btn" onclick="removeMember(${i})">Remove</button>
                </div>
            `;
        });
    }

    function displayPublicMembers() {
        let list = document.getElementById("publicMemberList");
        list.innerHTML = "";

        members.forEach(m => {
            list.innerHTML += `
                <div class="card">
                    <strong>${m.name}</strong><br>
                    Role: ${m.role}
                </div>
            `;
        });
    }

    function addMember() {
        let name = document.getElementById("memberName").value.trim();
        let role = document.getElementById("memberRole").value;

        if (name === "") return;

        members.push({ name, role });
        saveAll();
        displayMembers();
        displayPublicMembers();
    }

    function editMember(i) {
        let newName = prompt("New name:", members[i].name);
        if (!newName) return;

        let newRole = prompt("New role:", members[i].role);
        if (!newRole) return;

        members[i].name = newName;
        members[i].role = newRole;

        saveAll();
        displayMembers();
        displayPublicMembers();
    }

    function removeMember(i) {
        members.splice(i, 1);
        saveAll();
        displayMembers();
        displayPublicMembers();
    }

    function saveAll() {
        localStorage.accounts = JSON.stringify(accounts);
        localStorage.roles = JSON.stringify(roles);
        localStorage.members = JSON.stringify(members);
    }
</script>

</body>
</html>
