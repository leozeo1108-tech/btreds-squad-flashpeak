<!doctype html>
<html lang="id">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>BTReDS SQUAD — Flash Peak Online</title>
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<style>
*{box-sizing:border-box}body{margin:0;background:#08090d;color:#f5f5f5;font:15px system-ui,Arial,sans-serif}
header{padding:24px 16px;text-align:center;background:#11141b;border-bottom:1px solid #2b2f3a}
header h1{margin:0 0 6px;font-size:25px}main{max-width:1150px;margin:auto;padding:16px}
.card{background:#12151d;border:1px solid #303542;border-radius:14px;padding:16px;margin:12px 0}
input,select,button{padding:11px;border-radius:9px;border:1px solid #353a48;margin:4px;background:#0c0f15;color:#fff}
input,select{width:auto}button{cursor:pointer;font-weight:700}.primary{background:#d91f35;border-color:#d91f35}
.nav{display:flex;gap:7px;overflow:auto;margin:12px 0}.tab{display:none}.tab.active{display:block}
.grid{display:grid;grid-template-columns:repeat(4,1fr);gap:10px}.metric{padding:15px;background:#1a1e28;border-radius:12px}
.metric b{display:block;font-size:25px;margin-top:3px}.muted{color:#9da5b7}.error{color:#ff6c7c}
.table{overflow:auto}table{width:100%;border-collapse:collapse;min-width:760px}
th,td{padding:9px;border-bottom:1px solid #303542;text-align:left}th{background:#1a1e28}
.badge{display:inline-block;padding:4px 8px;border-radius:999px;background:#242936;margin-left:5px}
.formgrid{display:grid;grid-template-columns:repeat(3,1fr);gap:6px}
.squad{border:1px solid #303542;border-radius:12px;padding:12px;margin:10px 0}
@media(max-width:700px){.grid{grid-template-columns:1fr 1fr}.formgrid{grid-template-columns:1fr}header h1{font-size:20px}}
</style>
</head>

<body>

<header>
<h1>⚡ BTReDS SQUAD FLASH PEAK</h1>
<div class="muted">ONLINE • SPARRING • JADWAL • STATISTIK • BE A STAR</div>
</header>

<div id="login" class="card" style="max-width:430px;margin:40px auto">
<h2>🔐 Login</h2>

<input id="email" type="email" placeholder="Email" style="width:100%">
<input id="pass" type="password" placeholder="Password" style="width:100%">

<button class="primary" onclick="login()">Login</button>

<button onclick="loginGoogle()">
🔵 Lanjutkan dengan Google
</button>

<button onclick="signup()">
Daftar Player
</button>

<p id="err" class="error"></p>
</div>


<div id="app" style="display:none">

<main>

<div class="card">

<b id="user"></b>
<span id="role" class="badge"></span>

<button onclick="logout()" style="float:right">
Keluar
</button>

<div id="squadLabel" class="muted" style="margin-top:8px"></div>

</div>


<div class="nav">

<button onclick="showTab('dash')">
Dashboard
</button>

<button onclick="showTab('roster')">
Roster
</button>

<button onclick="showTab('schedule')">
Jadwal
</button>

<button onclick="showTab('matches')">
Sparring
</button>

<button onclick="showTab('champ')">
BE A STAR
</button>

</div>


<section id="dash" class="tab active">

<div class="grid">

<div class="metric">
Player
<b id="np">0</b>
</div>

<div class="metric">
Laga
<b id="nm">0</b>
</div>

<div class="metric">
Fee
<b id="nf">Rp 0</b>
</div>

<div class="metric">
Target
<b>5× / minggu</b>
</div>

</div>


<div class="card">

<h2>📌 Sistem</h2>

<p>
Minimum 5 laga sparring per minggu.
Fee sparring <b>Rp900/laga</b>.
Jadwal dan hasil dicatat oleh Admin/PJ.
</p>

<p class="muted">
Data tersimpan online di Supabase,
sehingga akun yang berbeda dapat melihat
database yang sama sesuai hak akses.
</p>

</div>

</section>


<section id="roster" class="tab">

<h2>👥 Roster BTReDS</h2>

<div id="rosterBox"></div>

</section>


<section id="schedule" class="tab">

<h2>🗓️ Jadwal</h2>


<div id="scheduleForm" class="card" style="display:none">

<div class="formgrid">

<input id="sd" placeholder="Hari">

<input id="sdate" type="date">

<input id="stime" type="time">

<select id="ss"></select>

<select id="st">

<option>Sparring</option>
<option>Turnamen</option>

</select>

<input id="so" placeholder="Lawan">

</div>

<button class="primary" onclick="addSchedule()">
Simpan Jadwal
</button>

</div>


<div class="table">

<table>

<thead>

<tr>
<th>Hari</th>
<th>Tanggal</th>
<th>Jam</th>
<th>Squad</th>
<th>Jenis</th>
<th>Lawan</th>
<th>Status</th>
</tr>

</thead>

<tbody id="sched"></tbody>

</table>

</div>

</section>


<section id="matches" class="tab">

<h2>📊 Rekap Sparring</h2>


<div id="matchForm" class="card" style="display:none">

<div class="formgrid">

<input id="md" type="date">

<select id="ms"></select>

<input id="mo" placeholder="Lawan">

<select id="mr">

<option>WIN</option>
<option>DRAW</option>
<option>LOSE</option>

</select>

<input id="mg" type="number" value="1" min="1">

</div>


<button class="primary" onclick="addMatch()">
Simpan Hasil
</button>

</div>


<div class="table">

<table>

<thead>

<tr>
<th>Tanggal</th>
<th>Squad</th>
<th>Lawan</th>
<th>Hasil</th>
<th>Laga</th>
<th>Fee</th>
</tr>

</thead>

<tbody id="match"></tbody>

</table>

</div>

</section>


<section id="champ" class="tab">

<h2>🏆 BE A STAR CHAMPIONSHIP</h2>

<div class="card">

Champion Rp50.000 • Runner Up Rp30.000 •
Peringkat 3 & 4 Rp15.000

<br>

Top Scorer Rp10.000 • Top Assist Rp10.000 •
Star Player Rp10.000

<br>

Best GK / CB / CM / WF / ST
masing-masing Rp5.000

</div>

</section>

</main>

</div>


<script>

const URL =
'https://oztmylktxjymmpkzroim.supabase.co';

const KEY =
'sb_publishable_vaaNcr7zd-2M1ebHAN_4oQ_pAZzHVYb';

const db =
supabase.createClient(URL,KEY);


let profile = null;
let currentUser = null;
let squads = [];


const R = {

PALS:[
["Azka","GK"],
["Fyrei","ST Rafael"],
["Yusaa-OP","CM Homer"],
["Shey","WF Kelly"],
["Frost","CB Andrew"],
["Kotaro","CB Andrew"],
["Dans-OP","WF Kelly"],
["Deino","CB Andrew"]
],

POISE:[
["Cello","GK"],
["Esco","CB Andrew"],
["Skyzo","WF Kelly"],
["Velz Zeref","WF Kelly / ST Shirou"],
["Vascooo","CB Andrew"],
["Rall","CB Ford"],
["Suyouuu","WF Kelly"],
["[BUTUH ST RAFAEL]","ST Rafael"]
],

PRIMO:[
["Azka","GK"],
["OneD","WF Kelly"],
["Steve","ST Shirou"],
["Brewok","CB Andrew"],
["Reyyzx","ST Orion"],
["Histeria","CB Ford"],
["Sumanto","CB Andrew"],
["[BUTUH WF KELLY]","WF Kelly"]
],

PRAISE:[
["HAMZZ","GK"],
["Alpinn","WF Kelly"],
["Raziel","CB Andrew"],
["Khai","CB Ford"],
["Catt","CB Ford"],
["Noirr","ST Shirou"],
["Wakwak","CM Homer"],
["Zhalee","CB Ford / CB Andrew"]
]

};


const money =
n => "Rp " +
(Number(n)||0).toLocaleString("id-ID");


const esc =
s => String(s??"").replace(
/[&<>"']/g,
m => ({
"&":"&amp;",
"<":"&lt;",
">":"&gt;",
'"':"&quot;",
"'":"&#39;"
}[m])
);


function showTab(id){

document
.querySelectorAll(".tab")
.forEach(
x => x.classList.remove("active")
);

document
.getElementById(id)
.classList.add("active");

}


async function loginGoogle(){

const r =
await db.auth.signInWithOAuth({

provider:"google",

options:{
redirectTo:
window.location.origin +
window.location.pathname
}

});

if(r.error)
err.textContent =
r.error.message;

}


async function login(){

const r =
await db.auth.signInWithPassword({

email:email.value.trim(),
password:pass.value

});

if(r.error)
err.textContent =
r.error.message;

}


async function signup(){

const e =
email.value.trim();

const p =
pass.value;

if(!e||!p){

err.textContent =
"Isi email dan password dulu.";

return;

}

const r =
await db.auth.signUp({

email:e,
password:p

});

err.textContent =
r.error
?r.error.message
:"Akun dibuat. Jika diminta, verifikasi email lalu login.";

}


async function logout(){

await db.auth.signOut();

location.reload();

}


async function initApp(u){

currentUser = u;


const pr =
await db
.from("profiles")
.select("id,full_name,role,squad_id")
.eq("id",u.id)
.single();


if(pr.error || !pr.data){

document.getElementById("login")
.style.display="block";

document.getElementById("app")
.style.display="none";

err.textContent =
"Akun belum memiliki profile. Admin perlu membuat profile akun ini.";

return;

}


profile =
pr.data;


document.getElementById("login")
.style.display="none";

document.getElementById("app")
.style.display="block";


user.textContent =
profile.full_name || u.email;


role.textContent =
profile.role.toUpperCase();


const sq =
(squads.find(
x => x.id === profile.squad_id
)||{}).name || "Semua Squad";


squadLabel.textContent =
"Akses: " + sq;


const can =
profile.role==="admin" ||
profile.role==="pj";


scheduleForm.style.display =
can ? "block" : "none";


matchForm.style.display =
can ? "block" : "none";


load();

}


async function load(){

const [
q1,
q2,
q3,
q4
] =
await Promise.all([

db
.from("squads")
.select("*")
.order("id"),

db
.from("players")
.select("*")
.order("squad_id")
.order("id"),

db
.from("schedules")
.select("*")
.order("date")
.order("time"),

db
.from("sparring_matches")
.select("*")
.order("match_date",{ascending:false})
.order("id",{ascending:false})

]);


squads =
q1.data || squads;


const ps =
q2.data || [];

const ss =
q3.data || [];

const mm =
q4.data || [];


np.textContent =
ps.length;


const games =
mm.reduce(
(a,x) =>
a + (Number(x.games_played)||0),
0
);


nm.textContent =
games;


nf.textContent =
money(games*900);


ssOptions();


rosterBox.innerHTML =
squads.map(s => {

const players =
ps.filter(
p => p.squad_id === s.id
);

return `

<div class="squad">

<h3>

${esc(s.name)}

<span class="badge">
${esc(s.person_in_charge)}
</span>

</h3>

<ul>

${players.map(p => `

<li>
${esc(p.name)}
—
<b>${esc(p.position)}</b>
</li>

`).join("")}

</ul>

</div>

`;

}).join("");


sched.innerHTML =
ss.map(x => `

<tr>

<td>${esc(x.day)}</td>
<td>${esc(x.date)}</td>
<td>${esc(x.time)}</td>
<td>${esc(x.squad_id)}</td>
<td>${esc(x.type)}</td>
<td>${esc(x.opponent)}</td>
<td>${esc(x.status)}</td>

</tr>

`).join("");


match.innerHTML =
mm.map(x => `

<tr>

<td>${esc(x.match_date)}</td>
<td>${esc(x.squad_id)}</td>
<td>${esc(x.opponent)}</td>
<td>${esc(x.result)}</td>
<td>${x.games_played}</td>
<td>${money(
Number(x.games_played)*900
)}</td>

</tr>

`).join("");

}


function ssOptions(){

const choices =
squads.map(s => `

<option value="${esc(s.id)}">
${esc(s.name.replace("BTReDS ",""))}
</option>

`).join("");


ss.innerHTML =
choices;


ms.innerHTML =
choices;


if(
profile?.role==="pj" &&
profile.squad_id
){

ss.value =
profile.squad_id;

ms.value =
profile.squad_id;

ss.disabled=true;

ms.disabled=true;

}

}


async function addSchedule(){

const row = {

day:sd.value,
date:sdate.value,
time:stime.value,
squad_id:ss.value,
type:st.value,
opponent:so.value,
status:"Terjadwal",
created_by:currentUser.id

};


const r =
await db
.from("schedules")
.insert(row);


if(r.error)

alert(r.error.message);

else{

alert("Jadwal tersimpan.");

load();

}

}


async function addMatch(){

const row = {

match_date:md.value,
squad_id:ms.value,
opponent:mo.value,
result:mr.value,
games_played:
Number(mg.value)||1,
created_by:currentUser.id

};


const r =
await db
.from("sparring_matches")
.insert(row);


if(r.error)

alert(r.error.message);

else{

alert("Hasil sparring tersimpan.");

load();

}

}


(async()=>{

const q =
await db
.from("squads")
.select("*")
.order("id");


squads =
q.data || [];


const s =
await db.auth.getSession();


if(s.data.session)

initApp(
s.data.session.user
);


db.auth.onAuthStateChange(
(_event,session) => {

if(
session &&
!currentUser
)

initApp(
session.user
);

});

})();

</script>

</body>
</html>
