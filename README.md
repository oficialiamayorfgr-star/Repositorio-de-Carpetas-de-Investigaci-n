<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Fiscalía General de la República - Rol Play</title>
<style>
* {margin:0; padding:0; box-sizing:border-box;}
body {
  font-family: Arial, sans-serif; 
  background: linear-gradient(135deg, #003366, #004d99, #e6e6e6);
  min-height:100vh;
  overflow-x:hidden; 
  position:relative;
}
.header {
  background: #003366; 
  color:white; 
  text-align:center; 
  padding:20px 0; 
  box-shadow:0 4px 10px rgba(0,0,0,0.3); 
  position:relative; 
  overflow:hidden;
}
.header img {width:100px; height:auto; animation:logoFade 2s ease;}
.header h1 {margin-top:10px; font-size:28px; animation:fadeIn 2s ease;}
.header h2 {margin-top:5px; font-size:18px; font-weight:normal; animation:fadeIn 2s ease;}
.header .clock {margin-top:5px; font-size:14px; font-weight:bold;}
.footer {
  background-color:#003366; 
  color:white; 
  text-align:center; 
  padding:15px 0; 
  margin-top:40px; 
  font-size:14px;
}
.footer a {color:white; text-decoration:none; font-weight:bold;}
.footer a:hover {text-decoration:underline;}
.container {
  display:flex; 
  justify-content:center; 
  flex-wrap:wrap; 
  gap:20px; 
  padding:30px 20px; 
  position: relative; 
  z-index:1;
}
.card {
  background:#fff; 
  padding:30px 25px; 
  border-radius:20px; 
  box-shadow:0 8px 25px rgba(0,0,0,0.2); 
  text-align:center; 
  width:340px; 
  transition: transform 0.3s ease;
}
.card:hover {transform: translateY(-5px);}
input, select, button {
  display:block; 
  margin:15px auto; 
  padding:12px; 
  width:90%; 
  border:1px solid #ccc; 
  border-radius:10px; 
  font-size:15px;
}
button {
  background:#0066cc; 
  color:white; 
  border:none; 
  cursor:pointer; 
  transition:0.3s; 
  font-weight:bold;
}
button:hover {background:#004d99;}
pre {
  text-align:left; 
  max-height:400px; 
  overflow:auto; 
  background:#f0f0f0; 
  padding:10px; 
  border-radius:8px; 
  white-space: pre-wrap;
}
#processing {
  position: fixed; 
  top:20px; 
  right:20px; 
  background: rgba(0,102,204,0.9); 
  color:white; 
  padding:10px 20px; 
  border-radius:10px; 
  display:none; 
  align-items:center; 
  gap:10px; 
  font-weight:bold; 
  z-index:1000;
}
#processing .spinner {
  border:4px solid #f3f3f3; 
  border-top:4px solid white; 
  border-radius:50%; 
  width:20px; 
  height:20px; 
  animation:spin 1s linear infinite;
}
.square {
  position:absolute; 
  width:60px; 
  height:60px; 
  opacity:0.2; 
  border-radius:8px; 
  pointer-events:none; 
  animation:floatSquare linear infinite;
}
#folderMessage {
  margin-top:15px;
  font-weight:bold;
  color:#003366;
}
@keyframes fadeIn {0% {opacity:0;} 100% {opacity:1;}}
@keyframes logoFade {0% {opacity:0; transform: scale(0.5);} 100% {opacity:1; transform: scale(1);}}
@keyframes spin {0% { transform: rotate(0deg);} 100% { transform: rotate(360deg);}}
@keyframes floatSquare {
  0% { transform: translateY(0px);}
  50% { transform: translateY(-80px);}
  100% { transform: translateY(0px);}
}
@media(max-width:600px){ .card{width:90%;} }
</style>
</head>
<body>

<div id="floatingSquares"></div>
<div id="processing"><div class="spinner"></div><div>Procesando...</div></div>

<div class="header">
  <img src="logo.png" alt="Logo FGR">
  <h1>Fiscalía General de la República</h1>
  <h2>Repositorio de Carpetas de Investigación</h2>
  <div class="clock" id="clock"></div>
</div>

<div class="container">

<!-- Login -->
<div id="loginDiv" class="card">
  <form id="loginForm">
    <label>Usuario:</label>
    <input type="text" id="username" required>
    <label>Contraseña:</label>
    <input type="password" id="password" required>
    <button type="submit">Ingresar</button>
  </form>
  <p id="error" style="color:red;"></p>
</div>

<!-- Menú usuario -->
<div id="menuDiv" class="card" style="display:none;">
  <p>Bienvenido, <span id="userName"></span></p>
  <label>Selecciona una carpeta:</label>
  <select id="carpeta"></select>
  <button onclick="abrirLink()">Abrir Carpeta</button>
  <p id="carpetaEstado"></p>
  <p id="folderMessage"></p>
  <button id="btnDeleteFolder" style="display:none;" onclick="eliminarCarpeta()">Eliminar Carpeta</button>
  <button id="btnAddFolder" style="display:none;" onclick="mostrarAddFolderForm()">Agregar Carpeta</button>
  <div id="addFolderForm" class="card" style="display:none;">
    <label>Número de Carpeta:</label>
    <input type="text" id="newFolderNumber" placeholder="Ej: 11">
    <label>Link de Carpeta:</label>
    <input type="text" id="newFolderLink" placeholder="https://...">
    <button onclick="agregarCarpeta()">Agregar</button>
  </div>
  <select id="estadoSelect" style="display:none;" onchange="cambiarEstado()">
    <option value="Abierta">Abierta</option>
    <option value="En investigación">En investigación</option>
    <option value="Cerrada">Cerrada</option>
  </select>
  <button id="btnLogs" style="display:none;" onclick="mostrarLogsView()">Ver Logs</button>

  <!-- Nuevo apartado para otorgar poderes de ADMIN -->
  <div id="adminPowersDiv" class="card" style="display:none;">
    <h3>Otorgar poderes de Administrador</h3>
    <label>Selecciona un usuario:</label>
    <select id="userToAdmin"></select>
    <button onclick="darPoderesAdmin()">Otorgar Poderes</button>
  </div>

  <button onclick="logout()">Cerrar sesión</button>
</div>

<!-- Logs -->
<div id="logsDiv" class="card" style="display:none; width: 90%; max-width:800px;">
  <h3>Logs de Actividad</h3>
  <pre id="logsContent"></pre>
  <button onclick="exportLogs()">Exportar Logs CSV</button>
  <button onclick="cerrarLogsView()">Volver</button>
</div>

</div>

<div class="footer">
  Visita nuestra página: 
  <a href="https://sites.google.com/view/fgr-tmxrp-02/p%C3%A1gina-principal" target="_blank">
    https://sites.google.com/view/fgr-tmxrp-02/página-principal
  </a>
</div>

<script>
// Usuarios
const users = {
  Darvin:{pass:"0497"},
  Yahir:{pass:"3612"},
  Emma:{pass:"4185"},
  Iker:{pass:"5193"},
  Ct:{pass:"2907"},
  Nicolas:{pass:"7359"},
  Gaspar:{pass:"6847"},
  Emmanuel:{pass:"3495"},
  Lego:{pass:"4697"},
  Jacob:{pass:"6520"},
  Facundo:{pass:"0183"},
  Angel:{pass:"3164"},
  Paski:{pass:"4327"},
  ADMIN:{pass:"045689"}
};

// Lista de admins
let admins = ["ADMIN","Darvin","Ct"];
let logs=[]; let currentUser=null;

// Carpetas y estados
let carpetas = {
"10":"https://docs.google.com/document/d/1LD1wDT1NMyfIQeXePjodydjTTv2IB7udgWpFAmk9N18/edit?tab=t.0",
"9":"https://docs.google.com/document/d/1Siksrj3AXj2WzOxxBX61tDx-gZR7oyq5othBS_B00wU/edit?tab=t.0",
"8":"https://docs.google.com/document/d/1z9jBNQzKw_fUztyTCL7Pq5UJ4wYq4eQ5DmFfAFa3iJg/edit?tab=t.0",
"7":"https://docs.google.com/document/d/1bMgdOrrWVRU6-G9bfdEcnwOw7rFolMVipHpaoTbsldY/edit?tab=t.0",
"6":"https://docs.google.com/document/d/1GPi5JBPBIyVfeCNySi3siYb2qazS0FNcdoepFimD6Hs/edit?tab=t.0",
"5":"https://docs.google.com/document/d/10nMixGzwzXZKyJoP3VnUi4uQPTAXl553JDnzyaJwb1I/edit?tab=t.0",
"4":"https://docs.google.com/document/d/19JPCwH1F6rhGXZEX3HJcjTguNnFks_tf1MX6RvY6MAI/edit?tab=t.0",
"3":"https://docs.google.com/document/d/1570nP2JbjTybnk36ZXfQSMs9c6Z-qEUkIhz3O4mUUbo/edit?tab=t.0",
"2":"https://docs.google.com/document/d/1tTyK9IekXgobvfouf-LCfTZcfZx9x4fpCSBcnndWWhc/edit?tab=t.0"
};
let estados = {};
Object.keys(carpetas).forEach(k=>estados[k]="Abierta");

// Logs
function logAction(user,action){logs.push({user,action,date:new Date().toLocaleString()});}
function logFailed(user){logs.push({user,action:"Intento de acceso fallido",date:new Date().toLocaleString()});}

// Procesando
function showProcessing(duration=2000,message="Procesando..."){
  const proc=document.getElementById("processing"); proc.style.display="flex";
  proc.querySelector("div:last-child").innerText=message;
  return new Promise(resolve=>setTimeout(()=>{proc.style.display="none"; resolve();},duration));
}

// Clock
function updateClock(){document.getElementById("clock").innerText=new Date().toLocaleString();}
setInterval(updateClock,1000); updateClock();

// Login
document.getElementById("loginForm").addEventListener("submit",async function(e){
  e.preventDefault(); 
  const user=document.getElementById("username").value; 
  const pass=document.getElementById("password").value;
  if(users[user] && users[user].pass===pass){
    await showProcessing(1500);
    currentUser=user; logAction(user,"Inicio de sesión");
    document.getElementById("loginDiv").style.display="none"; showMenu();
  } else { logFailed(user); document.getElementById("error").innerText="Usuario o contraseña incorrectos";}
});

// Menú
function showMenu(){
  document.getElementById("menuDiv").style.display="block";
  document.getElementById("userName").innerText=currentUser;
  cargarCarpetas();
  if(admins.includes(currentUser)){
    document.getElementById("btnLogs").style.display="block";
    document.getElementById("btnAddFolder").style.display="block";
    document.getElementById("btnDeleteFolder").style.display="block";
    document.getElementById("estadoSelect").style.display="block";
    document.getElementById("adminPowersDiv").style.display="block";
    actualizarUsuariosParaAdmin();
  }
}

// Abrir carpeta
function cargarCarpetas(){
  const select=document.getElementById("carpeta"); select.innerHTML="";
  Object.keys(carpetas).sort((a,b)=>b-a).forEach(k=>{
    const opt=document.createElement("option"); opt.value=k; opt.text="Carpeta "+k; select.add(opt);
  });
  mostrarEstado();
}
async function abrirLink(){
  const carpetaNum=document.getElementById("carpeta").value; 
  const estado=estados[carpetaNum];
  const link=carpetas[carpetaNum];
  const message=document.getElementById("folderMessage");
  message.innerText="";
  if(estado==="Abierta"){
    logAction(currentUser,"Accedió a Carpeta "+carpetaNum);
    await showProcessing(2000,"Abriendo carpeta...");
    window.open(link,"_blank");
  } else if(estado==="Cerrada"){
    message.innerText="Carpeta cerrada (sin acceso / archivada)";
    logAction(currentUser,"Intentó acceder a Carpeta "+carpetaNum+" [CERRADA]");
  } else if(estado==="En investigación"){
    message.innerText="Carpeta bajo investigación, acceso restringido.";
    logAction(currentUser,"Intentó acceder a Carpeta "+carpetaNum+" [INVESTIGACIÓN]");
  }
  mostrarEstado();
}

// Mostrar estado
function mostrarEstado(){
  const sel=document.getElementById("carpeta").value;
  document.getElementById("carpetaEstado").innerText="Estado: "+(estados[sel]||"Abierta");
  if(admins.includes(currentUser)){ document.getElementById("estadoSelect").value=estados[sel]; }
}

// Cambiar estado
function cambiarEstado(){
  if(!admins.includes(currentUser)) return;
  const sel=document.getElementById("carpeta").value;
  const nuevo=document.getElementById("estadoSelect").value;
  estados[sel]=nuevo;
  logAction(currentUser,"Cambiado estado Carpeta "+sel+" a "+nuevo);
  mostrarEstado();
}

// Agregar/eliminar carpeta
function mostrarAddFolderForm(){document.getElementById("addFolderForm").style.display="block";}
function agregarCarpeta(){
  if(!admins.includes(currentUser)) return;
  const num=document.getElementById("newFolderNumber").value.trim();
  const link=document.getElementById("newFolderLink").value.trim();
  if(num && link){carpetas[num]=link; estados[num]="Abierta"; cargarCarpetas(); logAction(currentUser,"Agregó Carpeta "+num); document.getElementById("newFolderNumber").value=""; document.getElementById("newFolderLink").value=""; alert("Carpeta agregada correctamente");}
}
function eliminarCarpeta(){
  if(!admins.includes(currentUser)) return;
  const num=document.getElementById("carpeta").value;
  if(num && confirm("¿Eliminar Carpeta "+num+"?")){delete carpetas[num]; delete estados[num]; cargarCarpetas(); logAction(currentUser,"Eliminó Carpeta "+num);}
}

// Logs
function mostrarLogsView(){
  if(admins.includes(currentUser)){
    document.getElementById("menuDiv").style.display="none";
    document.getElementById("logsDiv").style.display="block";
    const logsText=logs.map(log=>`${log.date} - ${log.user}: ${log.action}`).join("\n");
    document.getElementById("logsContent").innerText=logsText;
  }
}
function cerrarLogsView(){document.getElementById("logsDiv").style.display="none"; document.getElementById("menuDiv").style.display="block";}
function exportLogs(){
  if(admins.includes(currentUser)){
    const csvContent="data:text/csv;charset=utf-8,"+logs.map(l=>`${l.date},${l.user},${l.action}`).join("\n");
    const encodedUri=encodeURI(csvContent); const link=document.createElement("a");
    link.setAttribute("href",encodedUri); link.setAttribute("download","logs.csv"); document.body.appendChild(link); link.click(); document.body.removeChild(link);
  }
}

// Logout
function logout(){logAction(currentUser,"Cierre de sesión"); currentUser=null; document.getElementById("menuDiv").style.display="none"; document.getElementById("loginDiv").style.display="block";}

// Dar poderes de admin
function actualizarUsuariosParaAdmin(){
  const select = document.getElementById("userToAdmin"); select.innerHTML="";
  Object.keys(users).forEach(u => {
    if(!admins.includes(u)){ const opt=document.createElement("option"); opt.value=u; opt.text=u; select.add(opt);}
  });
}
function darPoderesAdmin(){
  const selectedUser=document.getElementById("userToAdmin").value;
  if(selectedUser){admins.push(selectedUser); logAction(currentUser,"Otorgó poderes de ADMIN a "+selectedUser); alert("Usuario "+selectedUser+" ahora es administrador."); actualizarUsuariosParaAdmin();}
}

// Fondos flotantes
function createFloatingSquares(){
  const container=document.getElementById("floatingSquares");
  for(let i=0;i<15;i++){
    const sq=document.createElement("div"); sq.classList.add("square");
    sq.style.top=Math.random()*100+"%"; sq.style.left=Math.random()*100+"%";
    sq.style.backgroundColor=["#ffffff","#cccccc","#99ccff"][Math.floor(Math.random()*3)];
    sq.style.animationDuration=(15+Math.random()*10)+"s"; container.appendChild(sq);
  }
}
  // Guardar logs en localStorage
localStorage.setItem("logs", JSON.stringify(logs));

// Recuperar logs al cargar la página
logs = JSON.parse(localStorage.getItem("logs")) || [];

createFloatingSquares();
</script>

</body>
</html>
