
---

```bash
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Rádio - em construção">
    <meta name="theme-color" content="#1E293B">
    <title>Rádio</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="manifest" href="/manifest.json">
</head>
<body class="font-sans text-gray-100 flex flex-col items-center justify-center p-4" style="background-color: #000000; min-height: 100vh;">
    <div class="bg-[rgba(15,23,42,0.85)] rounded-xl shadow-2xl p-8 player-container w-full max-w-md">
        <div class="text-center mb-8">
            <h1 class="text-3xl font-bold mb-2">Rádio</h1>
            <p class="text-gray-300">em construção</p>
        </div>

        <div class="flex flex-col items-center mb-8 space-y-4">
            <button id="playButton" class="bg-black bg-opacity-70 hover:bg-opacity-100 text-white rounded-full w-16 h-16 flex items-center justify-center mb-4 transition-all duration-200">
                <i id="playIcon" class="fas fa-play text-2xl"></i>
            </button>

            <div class="w-full max-w-xs">
                <div class="flex items-center space-x-2">
                    <i class="fas fa-volume-down text-gray-300"></i>
                    <input type="range" id="volumeControl" class="w-full h-1 bg-gray-700 outline-none" min="0" max="1" step="0.01" value="0.1">
                    <i class="fas fa-volume-up text-gray-300"></i>
                </div>
            </div>
        </div>

        <div class="bg-black bg-opacity-50 rounded-lg p-4 mb-8">
            <h3 class="font-semibold text-center mb-2">Versículo do dia</h3>
            <p id="bibleVerse" class="text-center italic text-gray-200">Carregando versículo...</p>
        </div>

        <div class="text-xs text-gray-400 text-center mb-4">
            <p>Esta rádio é um projeto pessoal, sem fins lucrativos.  Os louvores são usados apenas com o propósito de tocar no coração de amigos, familiares e quem mais ouvir. Caso você seja o artista ou detentor dos direitos e deseje que o conteúdo seja removido, entre em contato e será retirado imediatamente com respeito e gratidão.</p>
        </div>

        <div class="flex justify-center space-x-4">
            <a href="https://wa.me/" target="_blank" class="text-gray-300 hover:text-white transition-colors duration-200">
                <i class="fab fa-whatsapp text-xl"></i>
            </a>
            <a href="https://instagram.com/" target="_blank" class="text-gray-300 hover:text-white transition-colors duration-200">
                <i class="fab fa-instagram text-xl"></i>
            </a>
        </div>
    </div>

    <audio id="radioStream" preload="none">
        <source src="https://radio.azzor1337x.shop/radio.mp3" type="audio/mpeg">
    </audio>

    <script>
        const radioStream = document.getElementById('radioStream');
        const playButton = document.getElementById('playButton');
        const playIcon = document.getElementById('playIcon');
        const volumeControl = document.getElementById('volumeControl');

        let isPlaying = false;

        // Função para tentar reproduzir automaticamente
        window.addEventListener('load', () => {
            // Tentar iniciar o áudio automaticamente
            radioStream.play().then(() => {
                console.log("Áudio iniciado automaticamente");
                playIcon.className = 'fas fa-pause text-2xl';  // Atualiza o ícone para 'pause'
                isPlaying = true;  // Marca como tocando
            }).catch((e) => {
                console.log("Autoplay bloqueado:", e);
            });
        });

        // Evento de clique para alternar entre play e pause
        playButton.addEventListener('click', function () {
            if (isPlaying) {
                radioStream.pause();
                playIcon.className = 'fas fa-play text-2xl';
            } else {
                radioStream.play().catch(e => console.log('Autoplay prevented:', e));
                playIcon.className = 'fas fa-pause text-2xl';
            }
            isPlaying = !isPlaying;
        });

        // Controle de volume
        volumeControl.addEventListener('input', function () {
            radioStream.volume = this.value;
        });

        radioStream.volume = volumeControl.value;

        // Versículo do dia
        const bibleVerses = [
            '"Porque Deus amou o mundo de tal maneira que deu o seu Filho unigênito..." - João 3:16',
            '"O Senhor é o meu pastor; nada me faltará." - Salmos 23:1',
            '"Tudo posso naquele que me fortalece." - Filipenses 4:13',
            '"Entrega o teu caminho ao Senhor..." - Salmos 37:5',
            '"Pensamentos de paz... para vos dar o fim que esperais." - Jeremias 29:11'
        ];

        function getDailyVerse() {
            const today = new Date();
            const dayOfYear = Math.floor((today - new Date(today.getFullYear(), 0, 0)) / 86400000);
            return bibleVerses[dayOfYear % bibleVerses.length];
        }

        document.getElementById('bibleVerse').textContent = getDailyVerse();

        // Registrar Service Worker
        if ('serviceWorker' in navigator) {
            window.addEventListener('load', () => {
                navigator.serviceWorker.register('/sw.js')
                    .then(reg => console.log('Service Worker registrado'))
                    .catch(err => console.log('Erro ao registrar SW:', err));
            });
        }
    </script>
</body>
</html>
```

---

```bash
{
  "name": "Rádio",
  "short_name": "Rádio",
  "description": "Rádio",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#0F172A",
  "theme_color": "#0F172A",
  "icons": [
    {
      "src": "/icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ],
  "screenshots": [
    {
      "src": "/screenshot-mobile.png",
      "sizes": "1080x1920",
      "type": "image/png"
    },
    {
      "src": "/screenshot-desktop.png",
      "sizes": "1440x900",
      "type": "image/png"
    }
  ]
}
```

---

```bash
const CACHE_NAME = 'radio-cache-v1';
const URLS_TO_CACHE = [
  '/',
  '/index.html',
  '/manifest.json',
  '/icons/icon-192.png',
  '/icons/icon-512.png'
];

// Instala e adiciona arquivos ao cache
self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME).then(cache => {
      return cache.addAll(URLS_TO_CACHE);
    })
  );
});

// Limpa caches antigos ao ativar
self.addEventListener('activate', event => {
  event.waitUntil(
    caches.keys().then(keys =>
      Promise.all(
        keys.map(key => {
          if (key !== CACHE_NAME) {
            return caches.delete(key);
          }
        })
      )
    )
  );
});

// Responde com cache ou rede
self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request).then(response =>
      response || fetch(event.request)
    )
  );
});
```





---

```bash

```

