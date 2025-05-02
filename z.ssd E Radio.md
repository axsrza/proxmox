## Expandir 100% do ssd

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
    <style>
        body {
            background-image: url('https://images.unsplash.com/photo-1515694346937-94d85e41e6f0?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=2070&q=80');
            background-size: cover;
            background-position: center;
            background-attachment: fixed;
            min-height: 100vh;
        }
        
        .bg-semi-dark {
            background-color: rgba(15, 23, 42, 0.85);
        }
        
        .volume-slider {
            -webkit-appearance: none;
            width: 100%;
            height: 4px;
            background: #334155;
            outline: none;
        }
        
        .volume-slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 16px;
            height: 16px;
            border-radius: 50%;
            background: #f8fafc;
            cursor: pointer;
        }
        
        .volume-slider::-moz-range-thumb {
            width: 16px;
            height: 16px;
            border-radius: 50%;
            background: #f8fafc;
            cursor: pointer;
        }
        
        @media (max-width: 640px) {
            .player-container {
                width: 90%;
            }
        }
    </style>
</head>
<body class="font-sans text-gray-100 flex flex-col items-center justify-center p-4">
    <div class="bg-semi-dark rounded-xl shadow-2xl p-8 player-container w-full max-w-md">
        <div class="text-center mb-8">
            <h1 class="text-3xl font-bold mb-2">Rádio</h1>
            <p class="text-gray-300">em contrução</p>
        </div>
        
        <div class="flex flex-col items-center mb-8">
            <button id="playButton" class="bg-black bg-opacity-70 hover:bg-opacity-100 text-white rounded-full w-16 h-16 flex items-center justify-center mb-4 transition-all duration-200">
                <i id="playIcon" class="fas fa-play text-2xl"></i>
            </button>
            
            <div class="w-full max-w-xs">
                <div class="flex items-center space-x-2">
                    <i class="fas fa-volume-down text-gray-300"></i>
                    <input type="range" id="volumeControl" class="volume-slider" min="0" max="1" step="0.01" value="0.1">
                    <i class="fas fa-volume-up text-gray-300"></i>
                </div>
            </div>
        </div>
        
        <div class="bg-black bg-opacity-50 rounded-lg p-4 mb-8">
            <h3 class="font-semibold text-center mb-2">Versículo do dia</h3>
            <p id="bibleVerse" class="text-center italic text-gray-200">"Porque Deus amou o mundo de tal maneira que deu o seu Filho unigênito, para que todo aquele que nele crê não pereça, mas tenha a vida eterna." - João 3:16</p>
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
        <source src="http://radio.azzor1337x.shop/radio.mp3" type="audio/mpeg">
    </audio>
    
    <script>
        // Audio player functionality
        const radioStream = document.getElementById('radioStream');
        const playButton = document.getElementById('playButton');
        const playIcon = document.getElementById('playIcon');
        const volumeControl = document.getElementById('volumeControl');
        
        let isPlaying = false;
        
        // Play/Pause functionality
        playButton.addEventListener('click', function() {
            if (isPlaying) {
                radioStream.pause();
                playIcon.className = 'fas fa-play text-2xl';
            } else {
                radioStream.play().catch(e => console.log('Autoplay prevented:', e));
                playIcon.className = 'fas fa-pause text-2xl';
            }
            isPlaying = !isPlaying;
        });
        
        // Volume control
        volumeControl.addEventListener('input', function() {
            radioStream.volume = this.value;
        });
        
        // Set initial volume
        radioStream.volume = volumeControl.value;
        
        // Bible verse of the day (mock - in a real app you would fetch this from an API)
        const bibleVerses = [
            '"Porque Deus amou o mundo de tal maneira que deu o seu Filho unigênito, para que todo aquele que nele crê não pereça, mas tenha a vida eterna." - João 3:16',
            '"O Senhor é o meu pastor; nada me faltará." - Salmos 23:1',
            '"Tudo posso naquele que me fortalece." - Filipenses 4:13',
            '"Entrega o teu caminho ao Senhor; confia nele, e ele tudo fará." - Salmos 37:5',
            '"Porque eu bem sei os pensamentos que tenho a vosso respeito, diz o Senhor; pensamentos de paz, e não de mal, para vos dar o fim que esperais." - Jeremias 29:11'
        ];
        
        // Get a random verse (or use date to get the same verse each day)
        function getDailyVerse() {
            const today = new Date();
            const dayOfYear = Math.floor((today - new Date(today.getFullYear(), 0, 0)) / (1000 * 60 * 60 * 24));
            return bibleVerses[dayOfYear % bibleVerses.length];
        }
        
        document.getElementById('bibleVerse').textContent = getDailyVerse();
        
        // PWA Service Worker registration
        if ('serviceWorker' in navigator) {
            window.addEventListener('load', () => {
                navigator.serviceWorker.register('/sw.js').then(registration => {
                    console.log('ServiceWorker registration successful');
                }).catch(err => {
                    console.log('ServiceWorker registration failed: ', err);
                });
            });
        }
    </script>
    
    <script>
        // Manifest for PWA
        const manifest = {
            "name": "Rádio",
            "short_name": "Rádio",
            "description": "Rádio",
            "start_url": "/",
            "display": "standalone",
            "background_color": "#0F172A",
            "theme_color": "#0F172A",
            "icons": [
                {
                    "src": "https://via.placeholder.com/192x192.png?text=Rádio",
                    "sizes": "192x192",
                    "type": "image/png"
                },
                {
                    "src": "https://via.placeholder.com/512x512.png?text=Rádio",
                    "sizes": "512x512",
                    "type": "image/png"
                }
            ]
        };
        
        const blob = new Blob([JSON.stringify(manifest)], {type: 'application/json'});
        const manifestURL = URL.createObjectURL(blob);
        document.querySelector('link[rel="manifest"]').href = manifestURL;
    </script>
</body>
</html>
```



## Expandir 100% do ssd

```bash
lsblk
lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
resize2fs /dev/ubuntu-vg/ubuntu-lv
sudo reboot

```

## radio html

```bash
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="theme-color" content="#111827">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <title>Rádio Azzor</title>
    <link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text y=%22.9em%22 font-size=%2290%22>🎵</text></svg>">
    <link rel="manifest" href="data:application/json,{&quot;name&quot;:&quot;Rádio Azzor&quot;,&quot;short_name&quot;:&quot;Rádio Azzor&quot;,&quot;start_url&quot;:&quot;/&quot;,&quot;display&quot;:&quot;standalone&quot;,&quot;background_color&quot;:&quot;#111827&quot;,&quot;theme_color&quot;:&quot;#111827&quot;,&quot;icons&quot;:[{&quot;src&quot;:&quot;data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCA1MTIgNTEyIj48cGF0aCBmaWxsPSJ3aGl0ZSIgZD0iTTI1NiA4QzExOSA4IDggMTE5IDggMjU2czExMSAyNDggMjQ4IDI0OCAyNDgtMTExIDI0OC0yNDhTMzkzIDggMjU2IDh6bTAgNDQ4Yy0xMTAuNSAwLTIwMC04OS41LTIwMC0yMDBTMTQ1LjUgNTYgMjU2IDU2czIwMCA4OS41IDIwMCAyMDAtODkuNSAyMDAtMjAwIDIwMHptNjguOC0xNDguOUwtMTk0IDIyNi4xYy0zLjgtMy44LTMuOC0xMCAwLTEzLjhsMzAuOS0zMC45YzMuOC0zLjggMTAuMS0zLjggMTMuOSAwbDIxNS41IDIxNS41YzMuOCAzLjggMy44IDEwLjEgMCAxMy45TDMzOC43IDQxNmMtMy44IDMuOC0xMC4xIDMuOC0xMy45IDB6Ii8+PC9zdmc+&quot;,&quot;sizes&quot;:&quot;512x512&quot;,&quot;type&quot;:&quot;image/svg+xml&quot;}]}">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://kit.fontawesome.com/a076d05399.js" crossorigin="anonymous"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400;600&display=swap');

        body {
            font-family: 'Montserrat', sans-serif;
            background-image: url('https://images.unsplash.com/photo-1534796636912-3b95b3ab5986?ixlib=rb-1.2.1&auto=format&fit=crop&w=1950&q=80');
            background-size: cover;
            background-position: center;
            background-attachment: fixed;
            min-height: 100vh;
            position: relative;
        }

        .bg-overlay {
            background-color: rgba(17, 24, 39, 0.85);
        }

        .play-btn {
            transition: all 0.3s ease;
            box-shadow: 0 0 0 0 rgba(59, 130, 246, 0.7);
        }

        .play-btn:hover {
            transform: scale(1.05);
        }

        .play-btn.playing {
            animation: pulse 1.5s infinite;
        }

        @keyframes pulse {
            0% {
                box-shadow: 0 0 0 0 rgba(59, 130, 246, 0.7);
            }
            70% {
                box-shadow: 0 0 0 15px rgba(59, 130, 246, 0);
            }
            100% {
                box-shadow: 0 0 0 0 rgba(59, 130, 246, 0);
            }
        }

        .volume-slider {
            -webkit-appearance: none;
            width: 100%;
            height: 6px;
            border-radius: 3px;
            background: #4b5563;
            outline: none;
        }

        .volume-slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 18px;
            height: 18px;
            border-radius: 50%;
            background: #3b82f6;
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .volume-slider::-webkit-slider-thumb:hover {
            transform: scale(1.2);
        }
    </style>
</head>
<body class="text-gray-100">
    <div class="bg-overlay min-h-screen flex flex-col">
        <header class="py-6 px-4 text-center">
            <h1 class="text-3xl font-light tracking-wider">Rádio</h1>
            <p class="text-blue-300 mt-1">em construção</p>
        </header>

        <main class="flex-1 flex flex-col items-center justify-center px-4">
            <div class="w-full max-w-md text-center">
                <!-- Bible Verse -->
                <div class="bg-gray-800 bg-opacity-70 rounded-lg p-4 mb-8">
                    <h3 class="text-blue-300 text-sm uppercase tracking-wider mb-2">Versículo do Dia</h3>
                    <p id="bible-verse" class="italic">"Porque Deus tanto amou o mundo que deu o seu Filho Unigênito, para que todo o que nele crer não pereça, mas tenha a vida eterna."</p>
                    <p id="bible-reference" class="text-gray-400 mt-2 text-sm">João 3:16</p>
                </div>

                <!-- Play Button with icon -->
                <button id="play-btn" class="play-btn bg-blue-600 hover:bg-blue-500 text-white rounded-full w-24 h-24 flex items-center justify-center mx-auto mb-6 focus:outline-none">
                    <i class="fas fa-play text-3xl"></i>
                </button>

                <!-- Volume Control -->
                <div class="w-full max-w-xs mx-auto">
                    <div class="flex items-center justify-center space-x-3">
                        <i class="fas fa-volume-down text-gray-400"></i>
                        <input type="range" id="volume-slider" class="volume-slider" min="0" max="1" step="0.01" value="0.1">
                        <i class="fas fa-volume-up text-gray-400"></i>
                    </div>
                </div>
            </div>
        </main>

        <footer class="py-6 px-4 text-center text-gray-400 text-sm">
            <div class="max-w-2xl mx-auto">
                <p>Esta rádio é um projeto pessoal, sem fins lucrativos. O objetivo é que os louvores aqui tocados alcancem o coração de amigos, familiares e quem mais ouvir. Caso você seja o artista ou detentor dos direitos e deseje que o conteúdo seja removido, entre em contato e será retirado imediatamente com respeito e gratidão.</p>
            </div>
        </footer>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function () {
            const audio = new Audio('https://radio.azzor1337x.shop/radio.mp3');
            const playBtn = document.getElementById('play-btn');
            const volumeSlider = document.getElementById('volume-slider');
            const bibleVerse = document.getElementById('bible-verse');
            const bibleReference = document.getElementById('bible-reference');
            let isPlaying = false;

            // Set initial volume
            audio.volume = volumeSlider.value;

            // Try autoplay
            audio.play().then(() => {
                playBtn.classList.add('playing');
                playBtn.innerHTML = '<i class="fas fa-pause text-3xl"></i>';
                isPlaying = true;
            }).catch((err) => {
                console.warn("Autoplay bloqueado:", err);
            });

            // Toggle play/pause
            playBtn.addEventListener('click', function () {
                if (isPlaying) {
                    audio.pause();
                    playBtn.classList.remove('playing');
                    playBtn.innerHTML = '<i class="fas fa-play text-3xl"></i>';
                } else {
                    audio.play();
                    playBtn.classList.add('playing');
                    playBtn.innerHTML = '<i class="fas fa-pause text-3xl"></i>';
                }
                isPlaying = !isPlaying;
            });

            // Volume
            volumeSlider.addEventListener('input', function () {
                audio.volume = this.value;
            });

            // Bible verse
            fetchDailyVerse();

            async function fetchDailyVerse() {
                try {
                    const response = await fetch('https://www.bibliaonline.com.br/api/verses/nvi/random');
                    if (response.ok) {
                        const data = await response.json();
                        bibleVerse.textContent = `"${data.text}"`;
                        bibleReference.textContent = `${data.book.name} ${data.chapter}:${data.number}`;
                    } else {
                        useFallbackVerse();
                    }
                } catch {
                    useFallbackVerse();
                }
            }

            function useFallbackVerse() {
                const fallbackVerses = [
                    {
                        text: "O Senhor é o meu pastor; de nada terei falta.",
                        reference: "Salmos 23:1"
                    },
                    {
                        text: "Tudo posso naquele que me fortalece.",
                        reference: "Filipenses 4:13"
                    },
                    {
                        text: "Porque eu sei os planos que tenho para vocês, diz o Senhor...",
                        reference: "Jeremias 29:11"
                    },
                    {
                        text: "Confie no Senhor de todo o seu coração...",
                        reference: "Provérbios 3:5"
                    },
                    {
                        text: "Mas os que esperam no Senhor renovam as suas forças...",
                        reference: "Isaías 40:31"
                    }
                ];

                const randomVerse = fallbackVerses[Math.floor(Math.random() * fallbackVerses.length)];
                bibleVerse.textContent = `"${randomVerse.text}"`;
                bibleReference.textContent = randomVerse.reference;
            }

            // PWA install prompt
            let deferredPrompt;

            window.addEventListener('beforeinstallprompt', (e) => {
                e.preventDefault();
                deferredPrompt = e;
                setTimeout(() => {
                    if (confirm('Deseja adicionar a Rádio Azzor à sua tela inicial para fácil acesso?')) {
                        deferredPrompt.prompt();
                        deferredPrompt.userChoice.then((choiceResult) => {
                            deferredPrompt = null;
                        });
                    }
                }, 5000);
            });
        });
    </script>
</body>
</html>
```
