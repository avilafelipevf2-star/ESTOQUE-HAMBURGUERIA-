const CACHE = 'jb-estoque-v1';
const ASSETS = ['/', '/index.html', '/manifest.json', '/icons/icon-192.png', '/icons/icon-512.png'];

self.addEventListener('install', e => {
  e.waitUntil(
    caches.open(CACHE).then(c => c.addAll(ASSETS.filter(a => !a.includes('icon'))))
  );
  self.skipWaiting();
});

self.addEventListener('activate', e => {
  e.waitUntil(
    caches.keys().then(keys =>
      Promise.all(keys.filter(k => k !== CACHE).map(k => caches.delete(k)))
    )
  );
  self.clients.claim();
});

self.addEventListener('fetch', e => {
  e.respondWith(
    caches.match(e.request).then(cached => cached || fetch(e.request).then(res => {
      const clone = res.clone();
      caches.open(CACHE).then(c => c.put(e.request, clone));
      return res;
    }).catch(() => cached))
  );
});
{
  "name": "JB Estoque — Juninho Burgers",
  "short_name": "JB Estoque",
  "description": "Controle de estoque do Juninho Burgers, Frutal MG",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#f5f4f1",
  "theme_color": "#D85A30",
  "orientation": "portrait",
  "lang": "pt-BR",
  "icons": [
    {
      "src": "icons/icon-192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any maskable"
    },
    {
      "src": "icons/icon-512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any maskable"
    }
  ],
  "categories": ["business", "productivity"],
  "shortcuts": [
    {
      "name": "Novo item",
      "short_name": "Novo item",
      "description": "Adicionar item ao estoque",
      "url": "/?action=new"
    },
    {
      "name": "Ver alertas",
      "short_name": "Alertas",
      "description": "Ver itens com estoque baixo",
      "url": "/?tab=alertas"
    }
  ]
}
