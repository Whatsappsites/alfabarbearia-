// Alfa Barbearia V4.4 — Service Worker (FCM)
self.addEventListener('install',e=>e.waitUntil(self.skipWaiting()));
self.addEventListener('activate',e=>e.waitUntil(self.clients.claim()));
self.addEventListener('fetch',()=>{});
// Tratar o clique antes de importar o SDK do FCM. Restringir à pasta do próprio app.
self.addEventListener('notificationclick',e=>{
  e.stopImmediatePropagation();e.notification.close();
  const home=new URL('./#admin',self.registration.scope).href;
  e.waitUntil((async()=>{
    const windows=await self.clients.matchAll({type:'window',includeUncontrolled:true});
    const target=windows.find(c=>c.url.startsWith(self.registration.scope));
    if(target)return target.focus();
    return self.clients.openWindow(home);
  })());
});
let alfaMessagingReady=false;
try{
  importScripts('https://www.gstatic.com/firebasejs/10.13.2/firebase-app-compat.js','https://www.gstatic.com/firebasejs/10.13.2/firebase-messaging-compat.js');
  firebase.initializeApp({"apiKey":"AIzaSyD623E_Rl5UbtYolJOYywFnDQygoQUId04","authDomain":"alfabarbearia.firebaseapp.com","databaseURL":"https://alfabarbearia-default-rtdb.firebaseio.com","projectId":"alfabarbearia","storageBucket":"alfabarbearia.firebasestorage.app","messagingSenderId":"491650036687","appId":"1:491650036687:web:30cb655edb43946d38d8ba"});
  firebase.messaging(); // O SDK exibe automaticamente a notificação webpush do Worker.
  alfaMessagingReady=true;
}catch(e){console.warn('Alfa: Firebase Messaging indisponível.');}
self.addEventListener('message',e=>{
  if(e.data?.alfaPush==='check'&&e.ports[0])e.ports[0].postMessage({alfaPush:'4.4',ready:alfaMessagingReady});
});
