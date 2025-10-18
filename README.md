<!doctype html>
<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Logística & Inventario</title>
<style>
:root{
  --red:#c62828;
  --black:#121212;
  --bg:#0f0f0f;
  --text:#fafafa;
  --muted:#bdbdbd;
}
*{box-sizing:border-box;margin:0;padding:0}
body{
  font-family:Inter, system-ui, Arial;
  background:linear-gradient(180deg,var(--bg),#070707);
  color:var(--text);
  min-height:100vh;
}

/* AUTH */
.auth-screen{display:flex;align-items:center;justify-content:center;height:100vh;padding:16px}
.card.auth-card{
  background:linear-gradient(180deg,#1a1a1a,#0f0f0f);
  border-radius:12px;padding:28px;width:100%;max-width:420px;
  box-shadow:0 10px 30px rgba(0,0,0,.6);border:1px solid rgba(255,255,255,0.04);
  text-align:center;
}
.auth-card h1{margin-bottom:6px;font-size:22px}
.muted{color:var(--muted)}
.auth-card input{
  width:100%;padding:10px 12px;margin:8px 0;border-radius:8px;
  border:1px solid rgba(255,255,255,0.06);
  background:rgba(255,255,255,0.02);color:var(--text);
}
.row{display:flex;gap:8px;justify-content:center;flex-wrap:wrap}
button{
  background:var(--red);border:0;padding:10px 14px;border-radius:10px;
  color:white;cursor:pointer;transition:transform .12s, box-shadow .12s;
}
button.alt{background:transparent;border:1px solid rgba(255,255,255,0.08)}
button.google{background:#fff;color:#222}
button:hover{transform:translateY(-3px);box-shadow:0 10px 20px rgba(198,40,40,0.12)}

/* MAIN APP */
.hidden{display:none}
.topbar{display:flex;align-items:center;justify-content:space-between;
  padding:12px 18px;background:linear-gradient(90deg,rgba(0,0,0,0.6),rgba(18,18,18,0.9));
  border-bottom:1px solid rgba(255,255,255,0.03)}
.brand{font-weight:700;color:var(--red)}
.nav a{color:var(--muted);margin:0 8px;text-decoration:none}
.nav a.active{color:var(--text);border-bottom:2px solid var(--red);padding-bottom:6px}
.user-area{display:flex;gap:10px;align-items:center}
.ghost{background:transparent;border:1px solid rgba(255,255,255,0.06);
  padding:8px 10px;border-radius:8px;color:var(--muted)}
.container{padding:20px;display:grid;grid-template-columns:1fr;gap:18px}
.section{display:none}
.active-section{display:block}
.search-card{background:linear-gradient(180deg,#151515,#0f0f0f);padding:16px;
  border-radius:12px;border:1px solid rgba(255,255,255,0.03)}
.search-card input{flex:1;padding:10px;border-radius:8px;
  border:1px solid rgba(255,255,255,0.06);background:transparent;color:var(--text)}
.search-card .row{display:flex;gap:8px;flex-wrap:wrap}
.results{margin-top:12px;background:rgba(255,255,255,0.02);
  padding:10px;border-radius:8px}
.news-card{margin-top:12px;padding:12px;background:linear-gradient(180deg,#1b0f0f,#120909);
  border-radius:12px;border:1px solid rgba(198,40,40,0.08)}
.news-card img{max-width:100%;border-radius:8px;display:block;margin-bottom:8px}
input[type=file]{color:var(--muted);margin:8px 0}
.news-list .item{padding:10px;border-radius:8px;margin:8px 0;background:rgba(255,255,255,0.02)}
.contact-buttons a{display:inline-block;margin:6px 12px 6px 0;
  padding:10px 14px;background:var(--red);border-radius:10px;
  color:#fff;text-decoration:none}
@media(min-width:900px){.container{grid-template-columns:1fr 420px;}.news-card{grid-column:2}}
</style>
<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-auth-compat.js"></script>
</head>
<body>
<div id="app">
  <!-- LOGIN -->
  <div class="auth-screen" id="authScreen">
    <div class="card auth-card">
      <h1>Logística & Inventario</h1>
      <p class="muted">Accede para gestionar inventarios</p>
      <input id="name" placeholder="Nombre (para registrarse)" />
      <input id="email" placeholder="Correo" type="email" />
      <input id="password" placeholder="Contraseña" type="password" />
      <div class="row">
        <button id="btnRegister">Registrarse</button>
        <button id="btnLogin" class="alt">Iniciar sesión</button>
      </div>
      <div class="row">
        <button id="btnGoogle" class="google">Iniciar con Google</button>
      </div>
      <p class="muted small">Los datos se guardarán en el sistema.</p>
    </div>
  </div>

  <!-- PANEL -->
  <div id="mainApp" class="hidden">
    <header class="topbar">
      <div class="brand">Logística & Inventario</div>
      <nav class="nav">
        <a href="#" data-section="home" class="active">Inicio</a>
        <a href="#" data-section="documents">Documentos</a>
        <a href="#" data-section="images">Imágenes</a>
        <a href="#" data-section="news">Noticias</a>
        <a href="#" data-section="contact">Contacto</a>
      </nav>
      <div class="user-area">
        <span id="userEmail"></span>
        <button id="btnLogout" class="ghost">Cerrar sesión</button>
      </div>
    </header>

    <main class="container">
      <!-- HOME -->
      <section id="home" class="section active-section">
        <div class="search-card">
          <h2>Buscar código en inventario</h2>
          <div class="row">
            <input id="searchCode" placeholder="Ingrese código..." />
            <button id="btnSearch">Buscar</button>
          </div>
          <div id="searchResults" class="results"></div>
        </div>
        <div class="news-card" id="mainNews"></div>
      </section>

      <!-- DOCUMENTS -->
      <section id="documents" class="section">
        <h2>Documentos</h2>
        <input type="file" id="docFile" />
        <input id="docName" placeholder="Nombre del archivo (opcional)" />
        <button id="btnUploadDoc">Subir documento</button>
        <div id="docStatus"></div>
      </section>

      <!-- IMAGES -->
      <section id="images" class="section">
        <h2>Imágenes</h2>
        <input type="file" id="imgFile" accept="image/*" />
        <input id="imgName" placeholder="Nombre de la imagen (opcional)" />
        <button id="btnUploadImg">Subir imagen</button>
        <div id="imgStatus"></div>
      </section>

      <!-- NEWS -->
      <section id="news" class="section">
        <h2>Noticias</h2>
        <div id="newsList" class="news-list"></div>
      </section>

      <!-- CONTACT -->
      <section id="contact" class="section">
        <h2>Contacto</h2>
        <div class="contact-buttons">
          <a id="waBtn" target="_blank">WhatsApp</a>
          <a id="mailBtn" href="#">Correo</a>
        </div>
      </section>
    </main>
  </div>
</div>

<script>
/* CONFIGURAR ESTO */
const API_URL = 'https://script.google.com/macros/s/AKfycbxu3vlBmdKoMG-mwZ44sQSVDjyuQfbeUPqz9EbF5t4QeN7mMiFJ8ggZe9Z_K2rV79tb/exec';
const firebaseConfig = {
  apiKey: "AIzaSyDpBoHiKKC7MLQHiWV9psdE3eJ4YuR66GU",
  authDomain: "pagina-dda30.firebaseapp.com",
  projectId: "pagina-dda30",
  appId: "1:14427407275:web:8cba04677fdeef39a088ee"
};
const CONTACT_WHATSAPP = "https://wa.me/521XXXXXXXXXX?text=Hola%20quiero%20información";
const CONTACT_EMAIL = "mailto:tu@correo.com";

/* INIT FIREBASE */
firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();

/* UI ELEMENTS */
const authScreen = document.getElementById('authScreen');
const mainApp = document.getElementById('mainApp');
const userEmail = document.getElementById('userEmail');
const btnRegister = document.getElementById('btnRegister');
const btnLogin = document.getElementById('btnLogin');
const btnGoogle = document.getElementById('btnGoogle');
const btnLogout = document.getElementById('btnLogout');
const nameInput = document.getElementById('name');
const emailInput = document.getElementById('email');
const passInput = document.getElementById('password');

/* NAV */
const sections = document.querySelectorAll('.section');
document.querySelectorAll('.nav a').forEach(a=>{
  a.addEventListener('click', e=>{
    e.preventDefault();
    document.querySelectorAll('.nav a').forEach(n=>n.classList.remove('active'));
    a.classList.add('active');
    const target=a.dataset.section;
    sections.forEach(s=>s.id===target?s.classList.add('active-section'):s.classList.remove('active-section'));
  });
});

/* AUTH */
btnRegister.addEventListener('click', async ()=>{
  const name=nameInput.value.trim(), email=emailInput.value.trim(), pass=passInput.value;
  if(!name||!email||!pass) return alert('Completa todos los campos.');
  try{
    const userCred=await auth.createUserWithEmailAndPassword(email,pass);
    await userCred.user.updateProfile({displayName:name});
    await callApi('registerUser',{user:{name,email,uid:userCred.user.uid}});
    alert('Registrado correctamente');
  }catch(err){alert(err.message);}
});

btnLogin.addEventListener('click', async ()=>{
  const email=emailInput.value.trim(), pass=passInput.value;
  try{await auth.signInWithEmailAndPassword(email,pass);}catch(err){alert(err.message);}
});

btnGoogle.addEventListener('click', async ()=>{
  const provider=new firebase.auth.GoogleAuthProvider();
  try{
    const res=await auth.signInWithPopup(provider);
    const user=res.user;
    await callApi('registerUser',{user:{name:user.displayName,email:user.email,uid:user.uid}});
  }catch(err){alert(err.message);}
});

btnLogout.addEventListener('click',()=>auth.signOut());
auth.onAuthStateChanged(user=>{
  if(user){
    authScreen.classList.add('hidden');
    mainApp.classList.remove('hidden');
    userEmail.textContent=user.email||user.displayName;
    loadNews();
  }else{
    authScreen.classList.remove('hidden');
    mainApp.classList.add('hidden');
  }
});

/* API HELPERS */
async function callApi(action,payload){
  const res=await fetch(API_URL,{method:'POST',headers:{'Content-Type':'application/json'},
    body:JSON.stringify(Object.assign({action},payload))});
  return res.json();
}
async function getApi(action,params={}){
  const q=new URLSearchParams(Object.assign({action},params)).toString();
  const res=await fetch(API_URL+'?'+q);
  return res.json();
}

/* SEARCH */
document.getElementById('btnSearch').addEventListener('click',async()=>{
  const code=document.getElementById('searchCode').value.trim();
  if(!code) return alert('Ingrese un código.');
  const data=await getApi('search',{code});
  const out=document.getElementById('searchResults');
  if(data.results&&data.results.length){
    out.innerHTML=data.results.map(r=>`<div><strong>${r.codigo}</strong> — ${r.nombre} (Cant: ${r.cantidad})<br><small>${r.descripcion||''}</small></div>`).join('');
  }else out.innerHTML='<div class="muted">Sin resultados</div>';
});

/* UPLOAD */
async function uploadFile(file,filenameOpt){
  const reader=new FileReader();
  return new Promise((resolve,reject)=>{
    reader.onload=async e=>{
      const base64=e.target.result.split(',')[1];
      const payload={action:'uploadFile',filename:filenameOpt||file.name,mimeType:file.type,base64};
      try{resolve(await callApi('uploadFile',payload));}catch(err){reject(err);}
    };
    reader.onerror=reject;
    reader.readAsDataURL(file);
  });
}
document.getElementById('btnUploadDoc').addEventListener('click',async()=>{
  const f=document.getElementById('docFile');
  if(!f.files.length) return alert('Selecciona un archivo');
  document.getElementById('docStatus').textContent='Subiendo...';
  try{
    const res=await uploadFile(f.files[0],document.getElementById('docName').value.trim()||f.files[0].name);
    document.getElementById('docStatus').innerHTML='Subido: <a target="_blank" href="'+res.url+'">Abrir</a>';
  }catch(e){document.getElementById('docStatus').textContent='Error al subir';}
});
document.getElementById('btnUploadImg').addEventListener('click',async()=>{
  const f=document.getElementById('imgFile');
  if(!f.files.length) return alert('Selecciona una imagen');
  document.getElementById('imgStatus').textContent='Subiendo...';
  try{
    const res=await uploadFile(f.files[0],document.getElementById('imgName').value.trim()||f.files[0].name);
    document.getElementById('imgStatus').innerHTML='Subido: <a target="_blank" href="'+res.url+'">Abrir</a>';
  }catch(e){document.getElementById('imgStatus').textContent='Error al subir';}
});

/* NEWS */
async function loadNews(){
  const res=await getApi('listNews');
  const main=document.getElementById('mainNews'),list=document.getElementById('newsList');
  if(res.news&&res.news.length){
    const n=res.news[0];
    main.innerHTML=`<h3>${n.titulo}</h3>${n.imagenUrl?'<img src="'+n.imagenUrl+'">':''}<p>${n.contenido}</p>`;
    list.innerHTML=res.news.map(x=>`<div class="item"><strong>${x.titulo}</strong><br><small>${x.fecha}</small></div>`).join('');
  }else{main.innerHTML='<p class="muted">No hay noticias.</p>';list.innerHTML='';}
}

/* CONTACT */
document.getElementById('waBtn').href=CONTACT_WHATSAPP;
document.getElementById('mailBtn').href=CONTACT_EMAIL;
</script>
</body>
</html>
