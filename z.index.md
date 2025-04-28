## rosa

```bash
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Encantare Nails | Alongamento de Unhas | Joinville/SC</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;700&family=Poppins:wght@300;400;600&display=swap');
        
        body {
            font-family: 'Poppins', sans-serif;
            background-color: #fff9fb;
            color: #333;
        }
        
        .title-font {
            font-family: 'Playfair Display', serif;
        }
        
        .hero-gradient {
            background: linear-gradient(135deg, #f9e3ff 0%, #ffd6e7 100%);
        }
        
        .nail-polish {
            animation: float 6s ease-in-out infinite;
        }
        
        @keyframes float {
            0% { transform: translateY(0px); }
            50% { transform: translateY(-20px); }
            100% { transform: translateY(0px); }
        }
        
        .fade-in {
            animation: fadeIn 1.5s ease-in;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        
        .service-card:hover {
            transform: translateY(-10px);
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
        }
        
        .service-card {
            transition: all 0.3s ease;
        }
    </style>
</head>
<body class="overflow-x-hidden">
    <!-- Header/Navigation -->
    <header class="bg-white shadow-sm sticky top-0 z-50">
        <div class="container mx-auto px-6 py-4">
            <div class="flex items-center justify-between">
                <div class="flex items-center">
                    <div class="w-12 h-12 rounded-full bg-pink-100 flex items-center justify-center">
                        <i class="fas fa-spa text-pink-500 text-xl"></i>
                    </div>
                    <a href="#" class="title-font text-xl ml-3 text-pink-600">Encantare Nails</a>
                </div>
                <nav class="hidden md:flex space-x-8">
                    <a href="#home" class="text-pink-600 hover:text-pink-800 font-medium">Início</a>
                    <a href="#services" class="text-gray-600 hover:text-pink-600 font-medium">Serviços</a>
                    <a href="#about" class="text-gray-600 hover:text-pink-600 font-medium">Sobre</a>
                    <a href="#contact" class="text-gray-600 hover:text-pink-600 font-medium">Contato</a>
                </nav>
                <div class="md:hidden">
                    <button class="text-gray-600 focus:outline-none">
                        <i class="fas fa-bars text-2xl"></i>
                    </button>
                </div>
            </div>
        </div>
    </header>

    <!-- Hero Section -->
    <section id="home" class="hero-gradient py-20 md:py-32 fade-in">
        <div class="container mx-auto px-6 flex flex-col md:flex-row items-center">
            <div class="md:w-1/2 mb-12 md:mb-0">
                <h1 class="title-font text-4xl md:text-5xl lg:text-6xl font-bold text-gray-800 leading-tight">
                    Unhas que <span class="text-pink-600">encantam</span><br>
                    Momentos que <span class="text-pink-600">marcam</span>
                </h1>
                <p class="mt-6 text-lg text-gray-600 max-w-lg">
                    Mais do que unhas bonitas, uma injeção de autoestima! Cada detalhe é pensado para encantar e refletir sua personalidade.
                </p>
                <div class="mt-8 flex space-x-4">
                    <a href="https://www.instagram.com/encantare_nails/" target="_blank" class="bg-pink-600 hover:bg-pink-700 text-white px-6 py-3 rounded-full font-medium flex items-center">
                        <i class="fab fa-instagram mr-2"></i> Instagram
                    </a>
                    <a href="https://wa.me/5547999999999" target="_blank" class="bg-green-500 hover:bg-green-600 text-white px-6 py-3 rounded-full font-medium flex items-center">
                        <i class="fab fa-whatsapp mr-2"></i> WhatsApp
                    </a>
                </div>
            </div>
            <div class="md:w-1/2 flex justify-center">
                <div class="relative w-64 h-64 md:w-80 md:h-80">
                    <div class="absolute inset-0 bg-pink-200 rounded-full opacity-20"></div>
                    <div class="absolute inset-0 flex items-center justify-center">
                        <img src="https://images.unsplash.com/photo-1596704017250-3f8b11e3c1a2?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=800&q=80" alt="Nail Art" class="nail-polish w-48 h-48 md:w-64 md:h-64 object-cover rounded-full border-8 border-white shadow-xl">
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Services Section -->
    <section id="services" class="py-20 bg-white">
        <div class="container mx-auto px-6">
            <div class="text-center mb-16">
                <h2 class="title-font text-3xl md:text-4xl font-bold text-gray-800 mb-4">Nossos <span class="text-pink-600">Serviços</span></h2>
                <div class="w-20 h-1 bg-pink-500 mx-auto"></div>
                <p class="mt-6 text-gray-600 max-w-2xl mx-auto">
                    Técnicas exclusivas para unhas que combinam beleza, durabilidade e saúde.
                </p>
            </div>
            
            <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
                <!-- Service 1 -->
                <div class="service-card bg-white p-8 rounded-xl shadow-md hover:shadow-xl">
                    <div class="w-16 h-16 bg-pink-100 rounded-full flex items-center justify-center mb-6">
                        <i class="fas fa-gem text-pink-500 text-2xl"></i>
                    </div>
                    <h3 class="title-font text-xl font-bold text-gray-800 mb-3">Mold F1</h3>
                    <p class="text-gray-600">
                        Alongamento de unhas com técnica Mold F1 para um resultado natural e resistente.
                    </p>
                </div>
                
                <!-- Service 2 -->
                <div class="service-card bg-white p-8 rounded-xl shadow-md hover:shadow-xl">
                    <div class="w-16 h-16 bg-pink-100 rounded-full flex items-center justify-center mb-6">
                        <i class="fas fa-spa text-pink-500 text-2xl"></i>
                    </div>
                    <h3 class="title-font text-xl font-bold text-gray-800 mb-3">Banho em Gel</h3>
                    <p class="text-gray-600">
                        Esmaltação em gel que proporciona brilho intenso e durabilidade prolongada.
                    </p>
                </div>
                
                <!-- Service 3 -->
                <div class="service-card bg-white p-8 rounded-xl shadow-md hover:shadow-xl">
                    <div class="w-16 h-16 bg-pink-100 rounded-full flex items-center justify-center mb-6">
                        <i class="fas fa-palette text-pink-500 text-2xl"></i>
                    </div>
                    <h3 class="title-font text-xl font-bold text-gray-800 mb-3">Nail Art</h3>
                    <p class="text-gray-600">
                        Designs exclusivos e personalizados para expressar sua personalidade.
                    </p>
                </div>
            </div>
        </div>
    </section>

    <!-- About Section -->
    <section id="about" class="py-20 bg-gray-50">
        <div class="container mx-auto px-6">
            <div class="flex flex-col md:flex-row items-center">
                <div class="md:w-1/2 mb-12 md:mb-0 md:pr-12">
                    <img src="https://images.unsplash.com/photo-1599351431202-1e0f0137899a?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=800&q=80" alt="Suelen Briesemeister" class="rounded-xl shadow-lg w-full h-auto">
                </div>
                <div class="md:w-1/2">
                    <h2 class="title-font text-3xl md:text-4xl font-bold text-gray-800 mb-6">Sobre <span class="text-pink-600">mim</span></h2>
                    <p class="text-gray-600 mb-6">
                        ✨ Bem-vinda ao universo Encantare Nails. Sou a Suelen, dona da Encantare Nails. ✨️
                    </p>
                    <p class="text-gray-600 mb-6">
                        Canceriana ♋, natural de Joinville/SC, minha busca é por consciência e conexão com meu propósito. Aqui, cada detalhe é pensado para encantar. Mais do que unhas, criamos experiências que refletem sua personalidade com estilo, cuidado e um toque de magia.
                    </p>
                    <p class="text-gray-600 mb-8">
                        Você merece se sentir única — e eu sei como fazer isso acontecer! Com técnicas exclusivas e atendimento personalizado, transformo suas unhas em verdadeiras obras de arte.
                    </p>
                    <div class="flex space-x-4">
                        <div class="flex items-center">
                            <div class="w-12 h-12 rounded-full bg-pink-100 flex items-center justify-center">
                                <i class="fas fa-heart text-pink-500"></i>
                            </div>
                            <div class="ml-4">
                                <h4 class="font-bold text-gray-800">Paixão</h4>
                                <p class="text-sm text-gray-600">Pelo que faço</p>
                            </div>
                        </div>
                        <div class="flex items-center">
                            <div class="w-12 h-12 rounded-full bg-pink-100 flex items-center justify-center">
                                <i class="fas fa-award text-pink-500"></i>
                            </div>
                            <div class="ml-4">
                                <h4 class="font-bold text-gray-800">Qualidade</h4>
                                <p class="text-sm text-gray-600">Materiais premium</p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Testimonials -->
    <section class="py-20 bg-white">
        <div class="container mx-auto px-6">
            <div class="text-center mb-16">
                <h2 class="title-font text-3xl md:text-4xl font-bold text-gray-800 mb-4">Depoimentos</h2>
                <div class="w-20 h-1 bg-pink-500 mx-auto"></div>
                <p class="mt-6 text-gray-600 max-w-2xl mx-auto">
                    O que minhas clientes dizem sobre a experiência Encantare Nails
                </p>
            </div>
            
            <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
                <!-- Testimonial 1 -->
                <div class="bg-gray-50 p-8 rounded-xl">
                    <div class="flex items-center mb-4">
                        <div class="text-yellow-400">
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                        </div>
                    </div>
                    <p class="text-gray-600 mb-6">
                        "As unhas que a Suelen faz são incríveis! Sempre recebo muitos elogios. O atendimento é maravilhoso e as unhas duram semanas perfeitas."
                    </p>
                    <div class="flex items-center">
                        <div class="w-12 h-12 rounded-full bg-pink-200"></div>
                        <div class="ml-4">
                            <h4 class="font-bold text-gray-800">Ana Carolina</h4>
                            <p class="text-sm text-gray-600">Cliente há 2 anos</p>
                        </div>
                    </div>
                </div>
                
                <!-- Testimonial 2 -->
                <div class="bg-gray-50 p-8 rounded-xl">
                    <div class="flex items-center mb-4">
                        <div class="text-yellow-400">
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                        </div>
                    </div>
                    <p class="text-gray-600 mb-6">
                        "A Suelen tem um cuidado incrível com cada detalhe. Minhas unhas nunca ficaram tão bonitas! Super recomendo o trabalho dela."
                    </p>
                    <div class="flex items-center">
                        <div class="w-12 h-12 rounded-full bg-pink-200"></div>
                        <div class="ml-4">
                            <h4 class="font-bold text-gray-800">Juliana</h4>
                            <p class="text-sm text-gray-600">Cliente há 1 ano</p>
                        </div>
                    </div>
                </div>
                
                <!-- Testimonial 3 -->
                <div class="bg-gray-50 p-8 rounded-xl">
                    <div class="flex items-center mb-4">
                        <div class="text-yellow-400">
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                        </div>
                    </div>
                    <p class="text-gray-600 mb-6">
                        "Mais do que uma profissional, a Suelen é uma artista! Transforma minhas unhas em verdadeiras obras de arte. Amo cada detalhe!"
                    </p>
                    <div class="flex items-center">
                        <div class="w-12 h-12 rounded-full bg-pink-200"></div>
                        <div class="ml-4">
                            <h4 class="font-bold text-gray-800">Fernanda</h4>
                            <p class="text-sm text-gray-600">Cliente há 3 anos</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Gallery -->
    <section class="py-12 bg-gray-50">
        <div class="container mx-auto px-6">
            <div class="text-center mb-12">
                <h2 class="title-font text-3xl md:text-4xl font-bold text-gray-800 mb-4">Galeria</h2>
                <div class="w-20 h-1 bg-pink-500 mx-auto"></div>
                <p class="mt-6 text-gray-600 max-w-2xl mx-auto">
                    Alguns dos nossos trabalhos mais recentes
                </p>
            </div>
            
            <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                <div class="overflow-hidden rounded-lg shadow-md hover:shadow-xl transition duration-300">
                    <img src="https://images.unsplash.com/photo-1522337360788-8b13dee7a37e?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=800&q=80" alt="Nail Art" class="w-full h-48 object-cover hover:scale-105 transition duration-300">
                </div>
                <div class="overflow-hidden rounded-lg shadow-md hover:shadow-xl transition duration-300">
                    <img src="https://images.unsplash.com/photo-1596704017250-3f8b11e3c1a2?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=800&q=80" alt="Nail Art" class="w-full h-48 object-cover hover:scale-105 transition duration-300">
                </div>
                <div class="overflow-hidden rounded-lg shadow-md hover:shadow-xl transition duration-300">
                    <img src="https://images.unsplash.com/photo-1604658243842-8258f7a3acb8?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=800&q=80" alt="Nail Art" class="w-full h-48 object-cover hover:scale-105 transition duration-300">
                </div>
                <div class="overflow-hidden rounded-lg shadow-md hover:shadow-xl transition duration-300">
                    <img src="https://images.unsplash.com/photo-1608889825101-22bdc1638b2e?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=800&q=80" alt="Nail Art" class="w-full h-48 object-cover hover:scale-105 transition duration-300">
                </div>
            </div>
            
            <div class="text-center mt-8">
                <a href="https://www.instagram.com/encantare_nails/" target="_blank" class="inline-flex items-center text-pink-600 hover:text-pink-800 font-medium">
                    Veja mais no Instagram <i class="fas fa-arrow-right ml-2"></i>
                </a>
            </div>
        </div>
    </section>

    <!-- Contact Section -->
    <section id="contact" class="py-20 bg-white">
        <div class="container mx-auto px-6">
            <div class="text-center mb-16">
                <h2 class="title-font text-3xl md:text-4xl font-bold text-gray-800 mb-4">Agende seu <span class="text-pink-600">horário</span></h2>
                <div class="w-20 h-1 bg-pink-500 mx-auto"></div>
                <p class="mt-6 text-gray-600 max-w-2xl mx-auto">
                    Entre em contato para marcar sua avaliação ou agendar seu horário.
                </p>
            </div>
            
            <div class="max-w-4xl mx-auto bg-gray-50 rounded-xl shadow-md overflow-hidden">
                <div class="md:flex">
                    <div class="md:w-1/2 bg-pink-600 text-white p-12">
                        <h3 class="title-font text-2xl font-bold mb-6">Informações de Contato</h3>
                        <div class="mb-6 flex items-start">
                            <div class="mr-4 mt-1">
                                <i class="fas fa-map-marker-alt"></i>
                            </div>
                            <div>
                                <h4 class="font-bold">Endereço</h4>
                                <p>Joinville, Santa Catarina</p>
                            </div>
                        </div>
                        <div class="mb-6 flex items-start">
                            <div class="mr-4 mt-1">
                                <i class="fas fa-envelope"></i>
                            </div>
                            <div>
                                <h4 class="font-bold">Email</h4>
                                <p>contato@encantarenails.com</p>
                            </div>
                        </div>
                        <div class="flex items-start">
                            <div class="mr-4 mt-1">
                                <i class="fas fa-phone-alt"></i>
                            </div>
                            <div>
                                <h4 class="font-bold">Telefone</h4>
                                <p>(47) 99999-9999</p>
                            </div>
                        </div>
                        
                        <div class="mt-12">
                            <h4 class="font-bold mb-4">Redes Sociais</h4>
                            <div class="flex space-x-4">
                                <a href="https://www.instagram.com/encantare_nails/" target="_blank" class="w-10 h-10 rounded-full bg-pink-700 hover:bg-pink-800 flex items-center justify-center">
                                    <i class="fab fa-instagram"></i>
                                </a>
                                <a href="https://wa.me/5547999999999" target="_blank" class="w-10 h-10 rounded-full bg-green-500 hover:bg-green-600 flex items-center justify-center">
                                    <i class="fab fa-whatsapp"></i>
                                </a>
                            </div>
                        </div>
                    </div>
                    
                    <div class="md:w-1/2 p-12">
                        <form>
                            <div class="mb-6">
                                <label for="name" class="block text-gray-700 font-medium mb-2">Nome</label>
                                <input type="text" id="name" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-pink-500">
                            </div>
                            <div class="mb-6">
                                <label for="email" class="block text-gray-700 font-medium mb-2">Email</label>
                                <input type="email" id="email" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-pink-500">
                            </div>
                            <div class="mb-6">
                                <label for="message" class="block text-gray-700 font-medium mb-2">Mensagem</label>
                                <textarea id="message" rows="4" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-pink-500"></textarea>
                            </div>
                            <button type="submit" class="w-full bg-pink-600 hover:bg-pink-700 text-white font-medium py-3 px-6 rounded-lg transition duration-300">
                                Enviar Mensagem
                            </button>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Footer -->
    <footer class="bg-gray-900 text-white py-12">
        <div class="container mx-auto px-6">
            <div class="flex flex-col md:flex-row justify-between items-center">
                <div class="mb-6 md:mb-0">
                    <div class="flex items-center">
                        <div class="w-10 h-10 rounded-full bg-pink-600 flex items-center justify-center">
                            <i class="fas fa-spa"></i>
                        </div>
                        <span class="title-font text-xl ml-3">Encantare Nails</span>
                    </div>
                    <p class="mt-4 text-gray-400 max-w-md">
                        Mais do que unhas bonitas, uma injeção de autoestima! Unhas que encantam. Momentos que marcam.
                    </p>
                </div>
                
                <div class="flex space-x-6">
                    <a href="https://www.instagram.com/encantare_nails/" target="_blank" class="text-gray-400 hover:text-pink-500">
                        <i class="fab fa-instagram text-2xl"></i>
                    </a>
                    <a href="https://wa.me/5547999999999" target="_blank" class="text-gray-400 hover:text-green-400">
                        <i class="fab fa-whatsapp text-2xl"></i>
                    </a>
                </div>
            </div>
            
            <div class="border-t border-gray-800 mt-8 pt-8 flex flex-col md:flex-row justify-between items-center">
                <p class="text-gray-400 text-sm mb-4 md:mb-0">
                    &copy; 2023 Encantare Nails. Todos os direitos reservados.
                </p>
                <div class="flex space-x-6">
                    <a href="#home" class="text-gray-400 hover:text-white text-sm">Início</a>
                    <a href="#services" class="text-gray-400 hover:text-white text-sm">Serviços</a>
                    <a href="#about" class="text-gray-400 hover:text-white text-sm">Sobre</a>
                    <a href="#contact" class="text-gray-400 hover:text-white text-sm">Contato</a>
                </div>
            </div>
        </div>
    </footer>

    <!-- Floating WhatsApp Button -->
    <a href="https://wa.me/5547999999999" target="_blank" class="fixed bottom-6 right-6 w-16 h-16 bg-green-500 hover:bg-green-600 rounded-full flex items-center justify-center shadow-lg z-50 animate-bounce">
        <i class="fab fa-whatsapp text-white text-2xl"></i>
    </a>

    <script>
        // Simple form submission handler
        document.querySelector('form').addEventListener('submit', function(e) {
            e.preventDefault();
            alert('Mensagem enviada com sucesso! Entraremos em contato em breve.');
            this.reset();
        });
        
        // Smooth scrolling for anchor links
        document.querySelectorAll('a[href^="#"]').forEach(anchor => {
            anchor.addEventListener('click', function (e) {
                e.preventDefault();
                
                document.querySelector(this.getAttribute('href')).scrollIntoView({
                    behavior: 'smooth'
                });
            });
        });
    </script>
</body>
</html>

```



## preto

```bash

<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Encantare Nails | Alongamento de Unhas | Joinville/SC</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@400;700&family=Montserrat:wght@300;400;600&display=swap');
        
        body {
            font-family: 'Montserrat', sans-serif;
            background-color: #0a0a0a;
            color: #e5e5e5;
        }
        
        .title-font {
            font-family: 'Cinzel', serif;
        }
        
        .hero-gradient {
            background: linear-gradient(135deg, #1a1a1a 0%, #2a1a1a 100%);
        }
        
        .nail-polish {
            animation: float 6s ease-in-out infinite;
            box-shadow: 0 25px 50px -12px rgba(212, 175, 55, 0.25);
        }
        
        @keyframes float {
            0% { transform: translateY(0px); }
            50% { transform: translateY(-20px); }
            100% { transform: translateY(0px); }
        }
        
        .fade-in {
            animation: fadeIn 1.5s ease-in;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        
        .service-card:hover {
            transform: translateY(-10px);
            box-shadow: 0 25px 50px -12px rgba(212, 175, 55, 0.25);
        }
        
        .service-card {
            transition: all 0.3s ease;
            background: linear-gradient(145deg, #1a1a1a 0%, #252525 100%);
            border: 1px solid #333;
        }
        
        .gold-text {
            color: #d4af37;
        }
        
        .gold-border {
            border-color: #d4af37;
        }
        
        .gold-bg {
            background-color: #d4af37;
        }
        
        .gold-bg:hover {
            background-color: #c9a227;
        }
        
        .section-divider {
            width: 80px;
            height: 2px;
            background: linear-gradient(90deg, #d4af37, #f4e5c2);
            margin: 0 auto;
        }
        
        .testimonial-card {
            background: linear-gradient(145deg, #1a1a1a 0%, #252525 100%);
            border: 1px solid #333;
            position: relative;
        }
        
        .testimonial-card::before {
            content: """;
            position: absolute;
            top: 20px;
            left: 20px;
            font-family: 'Cinzel', serif;
            font-size: 80px;
            color: rgba(212, 175, 55, 0.1);
            line-height: 1;
        }
        
        .gallery-item {
            position: relative;
            overflow: hidden;
        }
        
        .gallery-item::after {
            content: "";
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0,0,0,0.3);
            transition: all 0.3s ease;
        }
        
        .gallery-item:hover::after {
            background: rgba(212, 175, 55, 0.2);
        }
        
        .gallery-item img {
            transition: transform 0.5s ease;
        }
        
        .gallery-item:hover img {
            transform: scale(1.1);
        }
        
        .contact-left {
            background: linear-gradient(145deg, #1a1a1a 0%, #252525 100%);
            border-right: 1px solid #333;
        }
        
        input, textarea {
            background-color: #1a1a1a;
            border: 1px solid #333;
            color: #e5e5e5;
        }
        
        input:focus, textarea:focus {
            border-color: #d4af37;
            outline: none;
            box-shadow: 0 0 0 3px rgba(212, 175, 55, 0.1);
        }
    </style>
</head>
<body class="overflow-x-hidden">
    <!-- Header/Navigation -->
    <header class="bg-black bg-opacity-90 shadow-sm sticky top-0 z-50 backdrop-blur-sm">
        <div class="container mx-auto px-6 py-4">
            <div class="flex items-center justify-between">
                <div class="flex items-center">
                    <div class="w-12 h-12 rounded-full bg-black flex items-center justify-center border gold-border">
                        <i class="fas fa-spa gold-text text-xl"></i>
                    </div>
                    <a href="#" class="title-font text-xl ml-3 gold-text">Encantare Nails</a>
                </div>
                <nav class="hidden md:flex space-x-8">
                    <a href="#home" class="gold-text hover:text-white font-medium">Início</a>
                    <a href="#services" class="text-gray-400 hover:gold-text font-medium">Serviços</a>
                    <a href="#about" class="text-gray-400 hover:gold-text font-medium">Sobre</a>
                    <a href="#contact" class="text-gray-400 hover:gold-text font-medium">Contato</a>
                </nav>
                <div class="md:hidden">
                    <button class="text-gray-400 hover:gold-text focus:outline-none">
                        <i class="fas fa-bars text-2xl"></i>
                    </button>
                </div>
            </div>
        </div>
    </header>

    <!-- Hero Section -->
    <section id="home" class="hero-gradient py-20 md:py-32 fade-in relative overflow-hidden">
        <div class="absolute inset-0 opacity-10" style="background-image: url('https://www.transparenttextures.com/patterns/diamond-upholstery.png');"></div>
        <div class="container mx-auto px-6 flex flex-col md:flex-row items-center relative z-10">
            <div class="md:w-1/2 mb-12 md:mb-0">
                <h1 class="title-font text-4xl md:text-5xl lg:text-6xl font-bold text-white leading-tight">
                    Unhas que <span class="gold-text">encantam</span><br>
                    Momentos que <span class="gold-text">marcam</span>
                </h1>
                <p class="mt-6 text-lg text-gray-300 max-w-lg">
                    Mais do que unhas bonitas, uma injeção de autoestima! Cada detalhe é pensado para encantar e refletir sua personalidade.
                </p>
                <div class="mt-8 flex space-x-4">
                    <a href="https://www.instagram.com/encantare_nails/" target="_blank" class="gold-bg hover:bg-yellow-600 text-black px-6 py-3 rounded-full font-medium flex items-center">
                        <i class="fab fa-instagram mr-2"></i> Instagram
                    </a>
                    <a href="https://wa.me/5547999999999" target="_blank" class="bg-green-600 hover:bg-green-700 text-white px-6 py-3 rounded-full font-medium flex items-center">
                        <i class="fab fa-whatsapp mr-2"></i> WhatsApp
                    </a>
                </div>
            </div>
            <div class="md:w-1/2 flex justify-center">
                <div class="relative w-64 h-64 md:w-80 md:h-80">
                    <div class="absolute inset-0 bg-black rounded-full opacity-20 border gold-border"></div>
                    <div class="absolute inset-0 flex items-center justify-center">
                        <img src="https://images.unsplash.com/photo-1596704017250-3f8b11e3c1a2?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=800&q=80" alt="Nail Art" class="nail-polish w-48 h-48 md:w-64 md:h-64 object-cover rounded-full border-4 border-white">
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Services Section -->
    <section id="services" class="py-20 bg-black">
        <div class="container mx-auto px-6">
            <div class="text-center mb-16">
                <h2 class="title-font text-3xl md:text-4xl font-bold text-white mb-4">Nossos <span class="gold-text">Serviços</span></h2>
                <div class="section-divider"></div>
                <p class="mt-6 text-gray-300 max-w-2xl mx-auto">
                    Técnicas exclusivas para unhas que combinam beleza, durabilidade e saúde.
                </p>
            </div>
            
            <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
                <!-- Service 1 -->
                <div class="service-card p-8 rounded-xl">
                    <div class="w-16 h-16 bg-black rounded-full flex items-center justify-center mb-6 border gold-border">
                        <i class="fas fa-gem gold-text text-2xl"></i>
                    </div>
                    <h3 class="title-font text-xl font-bold text-white mb-3">Mold F1</h3>
                    <p class="text-gray-300">
                        Alongamento de unhas com técnica Mold F1 para um resultado natural e resistente.
                    </p>
                </div>
                
                <!-- Service 2 -->
                <div class="service-card p-8 rounded-xl">
                    <div class="w-16 h-16 bg-black rounded-full flex items-center justify-center mb-6 border gold-border">
                        <i class="fas fa-spa gold-text text-2xl"></i>
                    </div>
                    <h3 class="title-font text-xl font-bold text-white mb-3">Banho em Gel</h3>
                    <p class="text-gray-300">
                        Esmaltação em gel que proporciona brilho intenso e durabilidade prolongada.
                    </p>
                </div>
                
                <!-- Service 3 -->
                <div class="service-card p-8 rounded-xl">
                    <div class="w-16 h-16 bg-black rounded-full flex items-center justify-center mb-6 border gold-border">
                        <i class="fas fa-palette gold-text text-2xl"></i>
                    </div>
                    <h3 class="title-font text-xl font-bold text-white mb-3">Nail Art</h3>
                    <p class="text-gray-300">
                        Designs exclusivos e personalizados para expressar sua personalidade.
                    </p>
                </div>
            </div>
        </div>
    </section>

    <!-- About Section -->
    <section id="about" class="py-20 bg-black bg-opacity-90 relative">
        <div class="absolute inset-0 opacity-10" style="background-image: url('https://www.transparenttextures.com/patterns/diamond-upholstery.png');"></div>
        <div class="container mx-auto px-6 relative z-10">
            <div class="flex flex-col md:flex-row items-center">
                <div class="md:w-1/2 mb-12 md:mb-0 md:pr-12">
                    <div class="relative">
                        <img src="https://images.unsplash.com/photo-1599351431202-1e0f0137899a?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=800&q=80" alt="Suelen Briesemeister" class="rounded-xl shadow-lg w-full h-auto border gold-border">
                        <div class="absolute -bottom-4 -right-4 w-24 h-24 bg-black rounded-full border-2 gold-border flex items-center justify-center">
                            <span class="title-font gold-text text-xl">Suelen</span>
                        </div>
                    </div>
                </div>
                <div class="md:w-1/2">
                    <h2 class="title-font text-3xl md:text-4xl font-bold text-white mb-6">Sobre <span class="gold-text">mim</span></h2>
                    <p class="text-gray-300 mb-6">
                        ✨ Bem-vinda ao universo Encantare Nails. Sou a Suelen, dona da Encantare Nails. ✨️
                    </p>
                    <p class="text-gray-300 mb-6">
                        Canceriana ♋, natural de Joinville/SC, minha busca é por consciência e conexão com meu propósito. Aqui, cada detalhe é pensado para encantar. Mais do que unhas, criamos experiências que refletem sua personalidade com estilo, cuidado e um toque de magia.
                    </p>
                    <p class="text-gray-300 mb-8">
                        Você merece se sentir única — e eu sei como fazer isso acontecer! Com técnicas exclusivas e atendimento personalizado, transformo suas unhas em verdadeiras obras de arte.
                    </p>
                    <div class="flex space-x-4">
                        <div class="flex items-center">
                            <div class="w-12 h-12 rounded-full bg-black flex items-center justify-center border gold-border">
                                <i class="fas fa-heart gold-text"></i>
                            </div>
                            <div class="ml-4">
                                <h4 class="font-bold text-white">Paixão</h4>
                                <p class="text-sm text-gray-400">Pelo que faço</p>
                            </div>
                        </div>
                        <div class="flex items-center">
                            <div class="w-12 h-12 rounded-full bg-black flex items-center justify-center border gold-border">
                                <i class="fas fa-award gold-text"></i>
                            </div>
                            <div class="ml-4">
                                <h4 class="font-bold text-white">Qualidade</h4>
                                <p class="text-sm text-gray-400">Materiais premium</p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Testimonials -->
    <section class="py-20 bg-black">
        <div class="container mx-auto px-6">
            <div class="text-center mb-16">
                <h2 class="title-font text-3xl md:text-4xl font-bold text-white mb-4">Depoimentos</h2>
                <div class="section-divider"></div>
                <p class="mt-6 text-gray-300 max-w-2xl mx-auto">
                    O que minhas clientes dizem sobre a experiência Encantare Nails
                </p>
            </div>
            
            <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
                <!-- Testimonial 1 -->
                <div class="testimonial-card p-8 rounded-xl">
                    <div class="flex items-center mb-4">
                        <div class="text-yellow-400">
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                        </div>
                    </div>
                    <p class="text-gray-300 mb-6 italic">
                        "As unhas que a Suelen faz são incríveis! Sempre recebo muitos elogios. O atendimento é maravilhoso e as unhas duram semanas perfeitas."
                    </p>
                    <div class="flex items-center">
                        <div class="w-12 h-12 rounded-full bg-gray-800 border gold-border"></div>
                        <div class="ml-4">
                            <h4 class="font-bold text-white">Ana Carolina</h4>
                            <p class="text-sm text-gray-400">Cliente há 2 anos</p>
                        </div>
                    </div>
                </div>
                
                <!-- Testimonial 2 -->
                <div class="testimonial-card p-8 rounded-xl">
                    <div class="flex items-center mb-4">
                        <div class="text-yellow-400">
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                        </div>
                    </div>
                    <p class="text-gray-300 mb-6 italic">
                        "A Suelen tem um cuidado incrível com cada detalhe. Minhas unhas nunca ficaram tão bonitas! Super recomendo o trabalho dela."
                    </p>
                    <div class="flex items-center">
                        <div class="w-12 h-12 rounded-full bg-gray-800 border gold-border"></div>
                        <div class="ml-4">
                            <h4 class="font-bold text-white">Juliana</h4>
                            <p class="text-sm text-gray-400">Cliente há 1 ano</p>
                        </div>
                    </div>
                </div>
                
                <!-- Testimonial 3 -->
                <div class="testimonial-card p-8 rounded-xl">
                    <div class="flex items-center mb-4">
                        <div class="text-yellow-400">
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                            <i class="fas fa-star"></i>
                        </div>
                    </div>
                    <p class="text-gray-300 mb-6 italic">
                        "Mais do que uma profissional, a Suelen é uma artista! Transforma minhas unhas em verdadeiras obras de arte. Amo cada detalhe!"
                    </p>
                    <div class="flex items-center">
                        <div class="w-12 h-12 rounded-full bg-gray-800 border gold-border"></div>
                        <div class="ml-4">
                            <h4 class="font-bold text-white">Fernanda</h4>
                            <p class="text-sm text-gray-400">Cliente há 3 anos</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Gallery -->
    <section class="py-12 bg-black bg-opacity-90">
        <div class="container mx-auto px-6">
            <div class="text-center mb-12">
                <h2 class="title-font text-3xl md:text-4xl font-bold text-white mb-4">Galeria</h2>
                <div class="section-divider"></div>
                <p class="mt-6 text-gray-300 max-w-2xl mx-auto">
                    Alguns dos nossos trabalhos mais recentes
                </p>
            </div>
            
            <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                <div class="gallery-item rounded-lg overflow-hidden">
                    <img src="https://images.unsplash.com/photo-1522337360788-8b13dee7a37e?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=800&q=80" alt="Nail Art" class="w-full h-48 md:h-64 object-cover">
                </div>
                <div class="gallery-item rounded-lg overflow-hidden">
                    <img src="https://images.unsplash.com/photo-1596704017250-3f8b11e3c1a2?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=800&q=80" alt="Nail Art" class="w-full h-48 md:h-64 object-cover">
                </div>
                <div class="gallery-item rounded-lg overflow-hidden">
                    <img src="https://images.unsplash.com/photo-1604658243842-8258f7a3acb8?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=800&q=80" alt="Nail Art" class="w-full h-48 md:h-64 object-cover">
                </div>
                <div class="gallery-item rounded-lg overflow-hidden">
                    <img src="https://images.unsplash.com/photo-1608889825101-22bdc1638b2e?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=800&q=80" alt="Nail Art" class="w-full h-48 md:h-64 object-cover">
                </div>
            </div>
            
            <div class="text-center mt-8">
                <a href="https://www.instagram.com/encantare_nails/" target="_blank" class="inline-flex items-center gold-text hover:text-white font-medium">
                    Veja mais no Instagram <i class="fas fa-arrow-right ml-2"></i>
                </a>
            </div>
        </div>
    </section>

    <!-- Contact Section -->
    <section id="contact" class="py-20 bg-black">
        <div class="container mx-auto px-6">
            <div class="text-center mb-16">
                <h2 class="title-font text-3xl md:text-4xl font-bold text-white mb-4">Agende seu <span class="gold-text">horário</span></h2>
                <div class="section-divider"></div>
                <p class="mt-6 text-gray-300 max-w-2xl mx-auto">
                    Entre em contato para marcar sua avaliação ou agendar seu horário.
                </p>
            </div>
            
            <div class="max-w-4xl mx-auto rounded-xl shadow-xl overflow-hidden border gold-border">
                <div class="md:flex">
                    <div class="contact-left md:w-1/2 p-12">
                        <h3 class="title-font text-2xl font-bold text-white mb-6">Informações de Contato</h3>
                        <div class="mb-6 flex items-start">
                            <div class="mr-4 mt-1 gold-text">
                                <i class="fas fa-map-marker-alt"></i>
                            </div>
                            <div>
                                <h4 class="font-bold text-white">Endereço</h4>
                                <p class="text-gray-300">Joinville, Santa Catarina</p>
                            </div>
                        </div>
                        <div class="mb-6 flex items-start">
                            <div class="mr-4 mt-1 gold-text">
                                <i class="fas fa-envelope"></i>
                            </div>
                            <div>
                                <h4 class="font-bold text-white">Email</h4>
                                <p class="text-gray-300">contato@encantarenails.com</p>
                            </div>
                        </div>
                        <div class="flex items-start">
                            <div class="mr-4 mt-1 gold-text">
                                <i class="fas fa-phone-alt"></i>
                            </div>
                            <div>
                                <h4 class="font-bold text-white">Telefone</h4>
                                <p class="text-gray-300">(47) 99999-9999</p>
                            </div>
                        </div>
                        
                        <div class="mt-12">
                            <h4 class="font-bold text-white mb-4">Redes Sociais</h4>
                            <div class="flex space-x-4">
                                <a href="https://www.instagram.com/encantare_nails/" target="_blank" class="w-10 h-10 rounded-full bg-black hover:bg-gray-900 flex items-center justify-center border gold-border">
                                    <i class="fab fa-instagram gold-text"></i>
                                </a>
                                <a href="https://wa.me/5547999999999" target="_blank" class="w-10 h-10 rounded-full bg-green-600 hover:bg-green-700 flex items-center justify-center">
                                    <i class="fab fa-whatsapp text-white"></i>
                                </a>
                            </div>
                        </div>
                    </div>
                    
                    <div class="md:w-1/2 p-12 bg-black">
                        <form>
                            <div class="mb-6">
                                <label for="name" class="block text-white font-medium mb-2">Nome</label>
                                <input type="text" id="name" class="w-full px-4 py-2 rounded-lg focus:outline-none">
                            </div>
                            <div class="mb-6">
                                <label for="email" class="block text-white font-medium mb-2">Email</label>
                                <input type="email" id="email" class="w-full px-4 py-2 rounded-lg focus:outline-none">
                            </div>
                            <div class="mb-6">
                                <label for="message" class="block text-white font-medium mb-2">Mensagem</label>
                                <textarea id="message" rows="4" class="w-full px-4 py-2 rounded-lg focus:outline-none"></textarea>
                            </div>
                            <button type="submit" class="w-full gold-bg hover:bg-yellow-600 text-black font-medium py-3 px-6 rounded-lg transition duration-300">
                                Enviar Mensagem
                            </button>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Footer -->
    <footer class="bg-black py-12 border-t border-gray-800">
        <div class="container mx-auto px-6">
            <div class="flex flex-col md:flex-row justify-between items-center">
                <div class="mb-6 md:mb-0">
                    <div class="flex items-center">
                        <div class="w-10 h-10 rounded-full bg-black flex items-center justify-center border gold-border">
                            <i class="fas fa-spa gold-text"></i>
                        </div>
                        <span class="title-font text-xl ml-3 gold-text">Encantare Nails</span>
                    </div>
                    <p class="mt-4 text-gray-400 max-w-md">
                        Mais do que unhas bonitas, uma injeção de autoestima! Unhas que encantam. Momentos que marcam.
                    </p>
                </div>
                
                <div class="flex space-x-6">
                    <a href="https://www.instagram.com/encantare_nails/" target="_blank" class="text-gray-400 hover:gold-text">
                        <i class="fab fa-instagram text-2xl"></i>
                    </a>
                    <a href="https://wa.me/5547999999999" target="_blank" class="text-gray-400 hover:text-green-400">
                        <i class="fab fa-whatsapp text-2xl"></i>
                    </a>
                </div>
            </div>
            
            <div class="border-t border-gray-800 mt-8 pt-8 flex flex-col md:flex-row justify-between items-center">
                <p class="text-gray-400 text-sm mb-4 md:mb-0">
                    &copy; 2023 Encantare Nails. Todos os direitos reservados.
                </p>
                <div class="flex space-x-6">
                    <a href="#home" class="text-gray-400 hover:gold-text text-sm">Início</a>
                    <a href="#services" class="text-gray-400 hover:gold-text text-sm">Serviços</a>
                    <a href="#about" class="text-gray-400 hover:gold-text text-sm">Sobre</a>
                    <a href="#contact" class="text-gray-400 hover:gold-text text-sm">Contato</a>
                </div>
            </div>
        </div>
    </footer>

    <!-- Floating WhatsApp Button -->
    <a href="https://wa.me/5547999999999" target="_blank" class="fixed bottom-6 right-6 w-16 h-16 bg-green-600 hover:bg-green-700 rounded-full flex items-center justify-center shadow-lg z-50 animate-bounce">
        <i class="fab fa-whatsapp text-white text-2xl"></i>
    </a>

    <script>
        // Simple form submission handler
        document.querySelector('form').addEventListener('submit', function(e) {
            e.preventDefault();
            alert('Mensagem enviada com sucesso! Entraremos em contato em breve.');
            this.reset();
        });
        
        // Smooth scrolling for anchor links
        document.querySelectorAll('a[href^="#"]').forEach(anchor => {
            anchor.addEventListener('click', function (e) {
                e.preventDefault();
                
                document.querySelector(this.getAttribute('href')).scrollIntoView({
                    behavior: 'smooth'
                });
            });
        });
    </script>
</body>
</html>

```
