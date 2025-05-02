## Expandir 100% do ssd

```bash
Crie um site de uma rádio cristã chamada "Azzor1337x Rádio". O visual deve ser minimalista e responsivo, com fundo estático de céu (céu azul com nuvens ou entardecer, algo tranquilo). Todo o estilo do site (botões, texto, menus) deve ser em preto ou tons escuros, com um toque elegante.

O site deve ter apenas:

Um botão de play/pause simples (sem animações), tocando o streaming em https://radio.azzor1337x.shop/radio.mp3

Um controle de volume funcional

Um pequeno espaço para exibir um versículo bíblico por dia, puxado do site https://www.bibliaonline.com.br/ ou um mock placeholder no estilo “Versículo do dia: João 3:16 - Porque Deus amou o mundo...”

Uma nota discreta dizendo: "Esta rádio é sem fins lucrativos. Os louvores são usados apenas com o propósito de tocar corações e aproximar amigos, familiares e ouvintes de Deus. Caso algum artista deseje a remoção de um conteúdo, entre em contato."

Ícones ou links pequenos para WhatsApp e Instagram no rodapé.

O site deve ser leve, rápido e adaptável para ser instalado como app via navegador (PWA - Progressive Web App) em celulares Android. O áudio deve tocar direto no navegador (sem pop-ups), e compatível com HTTPS ou HTTP se possível.
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
