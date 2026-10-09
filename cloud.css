(function(){
if(typeof firebase==='undefined'){console.warn('Firebase indisponível — a app funciona só em modo local.');return;}
firebase.initializeApp({apiKey:"AIzaSyCwULDIMegxK5qpk7KN3x4jvwbdwN8HSWw",authDomain:"alojamentovalenca-66035.firebaseapp.com",projectId:"alojamentovalenca-66035",storageBucket:"alojamentovalenca-66035.firebasestorage.app",messagingSenderId:"401234375880",appId:"1:401234375880:web:b41eb60bc1c0cd791ee1fd"});
const auth=firebase.auth(),db=firebase.firestore();
const LS=(k,v)=>v===undefined?localStorage.getItem(k):localStorage.setItem(k,v);
const dev=LS('fb_device')||(LS('fb_device',Math.random().toString(36).slice(2,8)),LS('fb_device'));
let uid=null,applying=false,timer=null,unsub=null,asking=false;
const ref=()=>db.collection('users').doc(uid).collection('data').doc('main');
const rawOf=d=>d.payload||(d.store?JSON.stringify(d.store):null);
const ask=(m,t)=>typeof customConfirm==='function'?customConfirm(m,t):Promise.resolve(confirm(m));
const badge=document.createElement('button');badge.type='button';badge.style.cssText='margin-left:6px;font-size:12px;padding:6px 10px;border-radius:8px;border:0;cursor:pointer;font-weight:600';{const sv=document.getElementById('savePainelBtn');if(sv&&sv.parentNode)sv.parentNode.insertBefore(badge,sv.nextSibling);else{badge.style.cssText+=';position:fixed;bottom:8px;left:8px;z-index:9998';document.body.appendChild(badge);}}
const COL={ok:['#1f8a5b','#fff'],busy:['#d99a1c','#111'],err:['#d24a4a','#fff'],off:['#3a4256','#cfd6e6']};
const setBadge=(t,k)=>{k=k||(/^☁ (Sincronizado|Gravado)/.test(t)?'ok':/^⚠/.test(t)?'err':/(guardar|sincronizar)/i.test(t)?'busy':'off');badge.textContent=t;badge.style.background=COL[k][0];badge.style.color=COL[k][1];badge.title='Nuvem: verde = gravado na Firebase · amarelo = a gravar · vermelho = falhou · cinzento = sem sessão. Clica para opções.';};
const okBadge=()=>{const d=new Date();LS('fb_last_ok',d.toISOString());setBadge('☁ Gravado '+String(d.getHours()).padStart(2,'0')+':'+String(d.getMinutes()).padStart(2,'0'));};setBadge('☁ …','off');
const gate=document.createElement('div');gate.style.cssText='display:none;position:fixed;inset:0;z-index:99999;background:#0a0e17;align-items:center;justify-content:center;font-family:system-ui,sans-serif;color:#e8ecf5';
const inp='width:100%;box-sizing:border-box;padding:10px;margin-bottom:8px;border-radius:8px;border:1px solid #2a3550;background:#0a0e17;color:#fff',bt='width:100%;padding:11px;border:0;border-radius:8px;font-weight:700;cursor:pointer;margin-top:8px';
gate.innerHTML='<div style="max-width:360px;width:90%;padding:28px;border:1px solid #2a3550;border-radius:12px;background:#121826"><h2 style="margin:0 0 8px;font-size:1.2rem">Livro da Casa</h2><p style="margin:0 0 16px;font-size:13px;opacity:.75">Inicia sessão para sincronizar na nuvem.</p><button type="button" id="fbG" style="'+bt+';background:#fff;color:#111;margin-top:0">Entrar com Google</button><input id="fbE" type="email" placeholder="E-mail" style="'+inp+';margin-top:10px"><input id="fbP" type="password" placeholder="Palavra-passe (mín. 6)" style="'+inp+'"><button type="button" id="fbL" style="'+bt+';background:#35D4FF;color:#0a0e17">Entrar</button><button type="button" id="fbC" style="'+bt+';background:transparent;border:1px solid #2a3550;color:#8a94a8;font-weight:500">Criar conta nova</button><p id="fbErr" style="color:#ff6b8a;font-size:12px;margin:10px 0 0;min-height:1.2em"></p><button type="button" id="fbS" style="'+bt+';background:transparent;border:1px solid #2a3550;color:#8a94a8;font-weight:500">Continuar sem nuvem</button></div>';
document.body.appendChild(gate);
const $=id=>gate.querySelector('#'+id);
const err=e=>{
  const c=(e&&e.code)||'';
  const msg=String((e&&e.message)||e||'');
  if(c==='auth/unauthorized-domain'){$('fbErr').textContent='Domínio não autorizado: adiciona este endereço em Authentication → Settings → Authorized domains.';return;}
  if(c==='auth/operation-not-supported-in-this-environment'||location.protocol==='file:'){$('fbErr').textContent='Abre a app por endereço https (GitHub Pages / Firebase Hosting), não como ficheiro local.';return;}
  if(c==='auth/invalid-credential'||c==='auth/wrong-password'||c==='auth/user-not-found'){$('fbErr').textContent='E-mail ou palavra-passe incorretos.';return;}
  if(c==='auth/popup-closed-by-user'||c==='auth/cancelled-popup-request'){$('fbErr').textContent='Login cancelado. Tenta de novo.';return;}
  if(c==='auth/popup-blocked'){$('fbErr').textContent='O browser bloqueou a janela. Permite pop-ups ou usa e-mail/palavra-passe.';return;}
  if(/missing initial state|sessionStorage|storage-partitioned|third-party cookie/i.test(msg)||c==='auth/missing-initial-state'){
    $('fbErr').textContent='O browser perdeu o estado do login (comum no telemóvel). Soluções: 1) Usa e-mail e palavra-passe abaixo. 2) Abre em Chrome/Safari normal (não privado). 3) Fecha separadores desta app e tenta de novo.';
    return;
  }
  $('fbErr').textContent=msg||String(e);
};
auth.getRedirectResult().then(res=>{
  if(res&&res.user){/* onAuthStateChanged trata a sessão */}
}).catch(e=>{
  const msg=String((e&&e.message)||'');
  if(/missing initial state/i.test(msg)){
    try{sessionStorage.clear();}catch(_){}
    console.warn('Firebase redirect state em falta — usa e-mail/palavra-passe ou tenta de novo.',e);
  }
});
function isMobileAuth(){
  return /Android|iPhone|iPad|iPod|Mobile/i.test(navigator.userAgent||'')||(window.matchMedia&&window.matchMedia('(max-width:900px)').matches);
}
$('fbG').onclick=async()=>{
  try{
    $('fbErr').textContent='';
    const provider=new firebase.auth.GoogleAuthProvider();
    provider.setCustomParameters({prompt:'select_account'});
    if(isMobileAuth()){
      await auth.signInWithRedirect(provider);
      return;
    }
    try{
      await auth.signInWithPopup(provider);
    }catch(popErr){
      const c=(popErr&&popErr.code)||'';
      if(c==='auth/popup-blocked'||c==='auth/operation-not-supported-in-this-environment'||/missing initial state|storage/i.test(String(popErr&&popErr.message||''))){
        $('fbErr').textContent='A redirecionar para o Google…';
        await auth.signInWithRedirect(provider);
        return;
      }
      throw popErr;
    }
  }catch(e){err(e);}
};
$('fbL').onclick=async()=>{try{$('fbErr').textContent='';await auth.signInWithEmailAndPassword($('fbE').value.trim(),$('fbP').value);}catch(e){err(e);}};
$('fbC').onclick=async()=>{try{$('fbErr').textContent='';if($('fbP').value.length<6){$('fbErr').textContent='Palavra-passe: mínimo 6 caracteres.';return;}await auth.createUserWithEmailAndPassword($('fbE').value.trim(),$('fbP').value);}catch(e){err(e);}};
$('fbS').onclick=()=>{LS('fb_skip','1');gate.style.display='none';};
async function dailyBackup(raw,rev){try{if(!uid||!raw)return;const day=new Date().toISOString().slice(0,10);if(LS('fb_bk_'+uid)===day)return;const col=db.collection('users').doc(uid).collection('backups');await col.doc(day).set({payload:raw,rev:rev||'',at:new Date().toISOString(),device:dev});LS('fb_bk_'+uid,day);const all=await col.get(),ids=[];all.forEach(d=>ids.push(d.id));ids.sort();while(ids.length>30){await col.doc(ids.shift()).delete();}}catch(e){console.warn('backup nuvem',e);}}
async function restoreCloudBackup(){const col=db.collection('users').doc(uid).collection('backups'),all=await col.get(),ids=[];all.forEach(d=>ids.push(d.id));ids.sort();if(!ids.length){await customAlert('Ainda não há cópias diárias na nuvem.');return;}
  const v=await customPromptDialog('Cópias na nuvem:\n'+ids.slice(-15).join(', ')+'\nData a restaurar (AAAA-MM-DD):',ids[ids.length-1],'Restaurar da nuvem');if(!v)return;const sn=await col.doc(String(v).trim()).get();if(!sn.exists){await customAlert('Cópia não encontrada.');return;}
  if(!await ask('Substituir TODOS os dados (neste e nos outros dispositivos) pela cópia de '+v+'? É guardada antes uma cópia local.','Restaurar'))return;
  const d=sn.data(),rev=Date.now()+'-'+dev;await ref().set({payload:d.payload,rev,updatedAt:new Date().toISOString(),device:dev});await applyRemote({payload:d.payload,rev});}
async function verifyCloud(){const sn=await ref().get();if(!sn.exists){await customAlert('Ainda não há dados na nuvem.');return;}const d=sn.data(),same=LS('fb_seen_'+uid)===(d.rev||'legacy');
  await customAlert('✓ A nuvem (Firebase) tem dados.\nÚltima gravação: '+(d.updatedAt?new Date(d.updatedAt).toLocaleString('pt-PT'):'—')+'\nDispositivo que gravou: '+(d.device||'?')+'\nEste dispositivo está '+(same?'IGUAL à nuvem':'DIFERENTE da nuvem')+'\nTamanho: '+Math.round((rawOf(d)||'').length/1024)+' KB','Verificar nuvem');}
function overdueCount(){const cur=currentRealMonthIndex();let n=0;MONTH_KEYS.forEach((mk,k)=>{if(k>cur)return;state.rooms.forEach(r=>{if(isRentedThisMonth(mk,r.id,k)&&effectiveStatusRenda(mk,r.id)==='Não Pago')n++;});});return n;}
function checkOverdueNotify(){try{if(!('Notification' in window)||Notification.permission!=='granted')return;const day=new Date().toISOString().slice(0,10);if(LS('fb_notif_day')===day)return;const n=overdueCount();if(n>0)new Notification('Livro da Casa',{body:n+' renda(s) em atraso'});LS('fb_notif_day',day);}catch(e){}}
setInterval(checkOverdueNotify,3600000);
async function forcePush(){
  if(!uid){await customAlert('Sem sessao na nuvem.');return;}
  const raw=snapNow()||'';if(!raw){await customAlert('Sem dados locais.');return;}
  const localSize=Math.round(raw.length/1024);
  const cs=await ref().get();
  const cloudSize=cs.exists?Math.round((rawOf(cs.data())||'').length/1024):0;
  const ok=await ask('Forcar ENVIO deste dispositivo -> nuvem?\n\nLocal: '+localSize+' KB\nNuvem: '+cloudSize+' KB\n\nA nuvem passa a ter exactamente os dados deste dispositivo.','Forcar envio');
  if(!ok)return;
  try{await autoSnapshot();}catch(e){}
  setBadge('☁ A forcar envio...');
  const ok2=await push();
  if(ok2)await customAlert('Enviado e confirmado na nuvem ('+localSize+' KB).','Forcar envio');
  else await customAlert('A escrita nao foi confirmada. Verifica a ligacao ou as regras Firestore.','Forcar envio');
}
async function forcePull(){
  if(!uid){await customAlert('Sem sessao na nuvem.');return;}
  const snap=await ref().get();
  if(!snap.exists){await customAlert('A nuvem esta vazia.');return;}
  const d=snap.data();
  const cloudSize=Math.round((rawOf(d)||'').length/1024);
  const ok=await ask('Forcar PUXAR nuvem -> este dispositivo?\n\nNuvem: '+cloudSize+' KB\n\nOs dados deste dispositivo sao substituidos.','Forcar puxar');
  if(!ok)return;
  await applyRemote(d);
  await customAlert('Dados da nuvem aplicados.','Forcar puxar');
}
badge.onclick=async()=>{if(!uid){LS('fb_skip','');gate.style.display='flex';return;}
  const v=await customPromptDialog('1 = Verificar gravação na nuvem\n2 = Restaurar cópia diária da nuvem\n3 = Ativar avisos de rendas em atraso\n4 = Terminar sessão\n5 = FORCAR ENVIO (local para nuvem)\n6 = FORCAR PUXAR (nuvem para local)','1','Nuvem');
  try{if(v==='1')await verifyCloud();else if(v==='2')await restoreCloudBackup();else if(v==='3'){if(!('Notification' in window))await customAlert('Este navegador não suporta notificações.');else if(await Notification.requestPermission()==='granted'){LS('fb_notif_day','');checkOverdueNotify();await customAlert('Avisos ativados: recebes um aviso por dia, com a app aberta, se houver rendas em atraso.');}}else if(v==='4'&&await ask('Terminar sessão na nuvem? Os dados ficam neste dispositivo.','Sair'))auth.signOut();else if(v==='5')await forcePush();else if(v==='6')await forcePull();}catch(e){await customAlert('Erro: '+(e.message||e));}};
async function applyRemote(d){const raw=rawOf(d);try{const j=JSON.parse(raw);if(!j||!j.properties)throw 0;}catch(e){alert('Dados da nuvem inválidos — nada foi alterado.');return false;}
  applying=true;
  try{
    try{await autoSnapshot();}catch(e){}
    await idbSet(IDB_KEY_MAIN,raw);
    try{localStorage.setItem(STORE_KEY,raw);}catch(e){}
    LS('fb_seen_'+uid,d.rev||'legacy');LS('fb_dirty_'+uid,'0');
    if(typeof window.__cloudReload==='function'){
      await window.__cloudReload({payload:raw,rev:d.rev});
    }else{
      location.reload();
    }
    return true;
  }finally{
    applying=false;
  }
}
async function push(){if(!uid||applying)return false;const raw=snapNow();if(!raw)return false;if(raw.length>900000){setBadge('⚠ Dados grandes demais');return false;}
  const rev=Date.now()+'-'+dev,prev=LS('fb_seen_'+uid)||'';LS('fb_seen_'+uid,rev);LS('fb_dirty_'+uid,'1');
  try{
    await ref().set({payload:raw,rev,updatedAt:new Date().toISOString(),device:dev});
    const check=await ref().get();
    if(!check.exists||(check.data()||{}).rev!==rev){
      LS('fb_seen_'+uid,prev);
      setBadge('⚠ Não confirmado');
      console.warn('push: rev nao coincide apos set',rev,check.exists?check.data().rev:'-');
      return false;
    }
    LS('fb_dirty_'+uid,'0');
    okBadge();
    dailyBackup(raw,rev);
    return true;
  }catch(e){
    LS('fb_seen_'+uid,prev);
    setBadge('⚠ Sem ligação');
    console.warn('push',e);
    return false;
  }}
function schedulePush(){if(!uid)return;LS('fb_dirty_'+uid,'1');setBadge('☁ A guardar…');clearTimeout(timer);timer=setTimeout(push,2000);}
async function reconcile(){const snap=await ref().get(),seen=LS('fb_seen_'+uid),dirty=LS('fb_dirty_'+uid)==='1';
  if(!snap.exists){await push();return 'pushed-first';}
  const d=snap.data(),rev=d.rev||'legacy';
  const __craw=rawOf(d)||'',__lraw=snapNow()||'';
  const __mismatch=Math.abs((__lraw.length||0)-(__craw.length||0))>200;
  if(rev===seen){
    if(dirty){await push();return 'pushed-dirty';}
    if(__mismatch){await push();return 'pushed-divergent';}
    return 'in-sync';
  }
  if(seen&&!dirty&&!__mismatch){await applyRemote(d);return 'applied';}
  const cloud=await ask('Há dados na nuvem'+(seen?' mais recentes E alterações locais por enviar':' e também dados neste dispositivo')+'.\nOK = usar os da NUVEM (guarda cópia local antes).\nCancelar = manter ESTE dispositivo e enviar para a nuvem.','Sincronização');
  if(cloud){await applyRemote(d);return 'applied-choice';}await push();return 'pushed-choice';}
function listen(){
  if(unsub){try{unsub();}catch(e){}unsub=null;}
  unsub=ref().onSnapshot(
    s=>{
      if(!s.exists||(s.metadata&&s.metadata.hasPendingWrites)||applying||asking)return;
      const d=s.data();
      if((d.rev||'legacy')===LS('fb_seen_'+uid)||LS('fb_dirty_'+uid)==='1')return;
      asking=true;
      ask('Há dados novos de outro dispositivo. Aplicar agora?','Sincronização').then(async ok=>{
        asking=false;
        if(ok)await applyRemote(d);
      });
    },
    e=>{
      console.warn('listen falhou, retry em 5s',e);
      setBadge('⚠ Listener caiu');
      setTimeout(listen,5000);
    }
  );
}
auth.onAuthStateChanged(async u=>{
  if(!u){uid=null;if(unsub){unsub();unsub=null;}setBadge('☁ Entrar');if(!LS('fb_skip'))gate.style.display='flex';return;}
  uid=u.uid;gate.style.display='none';setBadge('☁ A sincronizar…');
  try{const r=await reconcile();window.__cloudLast=r;if(!applying){okBadge();listen();if(typeof syncTenantInputs==='function')syncTenantInputs().catch(()=>{});if(typeof publishPortal==='function')publishPortal(true);dailyBackup(snapNow(),LS('fb_seen_'+uid));checkOverdueNotify();}}catch(e){setBadge('⚠ Sem ligação');console.warn('reconcile',e);}});
window.__fb={get db(){return db;},get uid(){return uid;}};
document.addEventListener('visibilitychange',()=>{if(document.hidden&&timer){clearTimeout(timer);timer=null;push();}});
const _s=saveState;saveState=async function(...a){const r=await _s.apply(this,a);if(r!==false)schedulePush();return r;};
})();