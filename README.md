<!DOCTYPE html>
<html lang="es" class="scroll-smooth">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Guía Comercial de Santiago</title>
    
    <script src="https://cdn.tailwindcss.com"></script>
    
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin=""/>
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>
    
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
    
    <style>
        body { 
            font-family: 'Inter', sans-serif; 
            background-color: #F8F8F8; /* Un gris muy claro */
            color: #333; /* Un gris oscuro para el texto principal */
        }
        .font-serif {
            font-family: 'Playfair Display', serif;
        }
        /* Animación de entrada para secciones */
        .section-fade-in {
            opacity: 0;
            transform: translateY(30px);
            transition: opacity 0.8s ease-out, transform 0.8s ease-out;
        }
        .section-fade-in.is-visible {
            opacity: 1;
            transform: translateY(0);
        }
        /* Estilos para el popup de Leaflet */
        .leaflet-popup-content-wrapper { 
            border-radius: 8px; 
            background-color: #fff;
        }
        /* Animaciones para el modal */
        .modal-enter {
            opacity: 0;
            transform: scale(0.95);
        }
        .modal-enter-active {
            opacity: 1;
            transform: scale(1);
            transition: all 300ms cubic-bezier(0.4, 0, 0.2, 1);
        }
        .modal-leave {
            opacity: 1;
            transform: scale(1);
        }
        .modal-leave-active {
            opacity: 0;
            transform: scale(0.95);
            transition: all 300ms cubic-bezier(0.4, 0, 0.2, 1);
        }
        /* Fondo con imagen de Santiago y efecto inicial */
        #hero-landing {
            background-image: linear-gradient(rgba(0, 0, 0, 0.4), rgba(0, 0, 0, 0.6)), url('https://images.unsplash.com/photo-1627918451996-3f0e206b0d91?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwxfHxTYW50aWFnbyUyMENoaWxlfGVufDB8fHx8MTcwNzY5OTgwN3ww&ixlib=rb-4.0.3&q=80&w=1920');
            background-size: cover;
            background-position: center;
            background-attachment: fixed; /* Efecto paralaje sutil */
            opacity: 0; /* Inicia invisible para la animación */
            transform: scale(1.05); /* Inicia ligeramente más grande para el zoom */
            transition: opacity 1.5s ease-out, transform 2s ease-out; /* Animación más larga */
        }
        #hero-landing.is-loaded {
            opacity: 1;
            transform: scale(1);
        }
        /* Patrón de fondo para textura, ajustado a neutros */
        .textured-bg-neutral {
            background-image: url('https://www.transparenttextures.com/patterns/light-paper-fibers.png');
            background-color: #E8E8E8; /* Gris muy claro para secciones de categorías */
        }
    </style>
</head>
<body class="text-gray-800">

    <section id="hero-landing" class="min-h-screen flex flex-col items-center justify-center text-center p-8 text-white cursor-pointer transition-opacity duration-1000 ease-out">
        <div class="mb-6">
            <svg class="w-24 h-24 text-gray-300" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM4.332 8.944a5.59 5.59 0 00-.584 3.954 5.59 5.59 0 003.58 3.58 5.59 5.59 0 003.954-.584l-5.32-5.32a.75.75 0 00-1.63.674v.001zM10 2a7.962 7.962 0 014.238 1.152l-5.39 5.39a.75.75 0 01-.674 1.63L2.848 5.762A7.962 7.962 0 0110 2zM14.238 14.848L8.848 9.458a.75.75 0 011.63-.674l5.39 5.39A7.962 7.962 0 0110 18a7.962 7.962 0 014.238-3.152z" clip-rule="evenodd"></path></svg>
        </div>
        <h1 class="font-serif text-5xl md:text-7xl font-bold text-white mb-4 drop-shadow-lg">¿No sabes dónde encontrar lo que buscas?</h1>
        <p class="mt-4 text-lg text-gray-200 max-w-2xl drop-shadow">Haz clic para explorar nuestra guía completa de proveedores en Santiago.</p>
        <i class="fas fa-arrow-circle-right text-6xl text-gray-300 mt-12 animate-pulse drop-shadow-lg"></i>
    </section>

    <div id="main-app-content" class="hidden opacity-0 transition-opacity duration-1000 ease-out">
        <section id="map-section" class="py-16 sm:py-24 bg-white section-fade-in shadow-inner">
            <div class="container mx-auto px-6">
                <h2 class="font-serif text-4xl font-bold text-center mb-4 text-gray-900">Ubícalos en el Mapa</h2>
                <p class="text-center text-gray-700 max-w-3xl mx-auto mb-8">Explora las ubicaciones de las tiendas físicas. Haz clic en un marcador para ver más detalles.</p>
                <div id="map" class="h-[500px] w-full rounded-2xl shadow-xl border-4 border-gray-100 z-0"></div>
            </div>
        </section>

        <section id="categories-section" class="py-16 sm:py-24 textured-bg-neutral section-fade-in">
            <div class="container mx-auto px-6">
                <h2 class="font-serif text-4xl font-bold text-center mb-4 text-gray-900">Explora por Categoría</h2>
                <p class="text-center text-gray-700 max-w-3xl mx-auto mb-12">Encuentra exactamente lo que buscas navegando por nuestras categorías de productos.</p>
                <div id="categories-container" class="space-y-16">
                    </div>
            </div>
        </section>
        
        <footer class="bg-gray-100 py-8">
            <div class="text-center text-gray-600">
                <p>© 2024 Guía Comercial Santiago. Todos los derechos reservados.</p>
            </div>
        </footer>
    </div>

    <div id="store-modal" class="fixed inset-0 bg-black bg-opacity-60 hidden items-center justify-center p-4 z-50">
        <div id="modal-content" class="bg-white rounded-2xl shadow-2xl w-full max-w-lg max-h-[90vh] overflow-y-auto transform modal-enter">
            </div>
    </div>

    <script>
    const storesData = [
        { id: 1, name: "Romantic Beauty", type: "minorista", categories: ["Cosméticos"], address: "Online", lat: null, lon: null, contact: { web: "https://www.romanticbeauty.cl" }, description: "Tienda online especializada en maquillaje y productos de belleza, enfocada en la venta al detalle.", images: ["https://images.unsplash.com/photo-1558509420-a61f221434c4?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwxfHxjb3NtZXRpY3MlMjBzaG9wfGVufDB8fHx8MTcwNzY5NDM0MHww&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1572133599478-896d722a1657?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwyfHxjb3NtZXRpY3MlMjBzaG9wfGVufDB8fHx8MTcwNzY5NDM0MHww&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 2, name: "Max Belleza", type: "minorista", categories: ["Cosméticos", "Uñas", "Pestañas"], address: "Online", lat: null, lon: null, contact: { web: "https://max-belleza.cl/tienda/" }, description: "Tienda online con una variada oferta en belleza, desde maquillaje hasta insumos para uñas y pestañas.", images: ["https://images.unsplash.com/photo-1596701021435-ff682b130b9d?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwyfHxuYWlsJTIwc2Fsb258ZW58MHx8fHwxNzA3Njk0Mzk3fDA&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1502235942484-bb9e0000d663?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwzfHxlY2VsYXNoJTIwZXh0ZW5zaW9ufGVufDB8fHx8MTcwNzY5NDQyMnww&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1615499485147-11734984130f?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwyfHxuYWlsJTIwcHJvZHVjdHxlbnwwfHx8fDE3MDc2OTUzNzh8MA&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 3, name: "Mei Moda", type: "mayorista", categories: ["Pestañas", "Uñas", "Peluquería"], address: "Online", lat: null, lon: null, contact: { web: "https://meidemoda.cl/", whatsapp: "+56966966966" }, description: "Proveedor integral para profesionales de la belleza, con amplia gama de productos para pestañas, uñas y peluquería.", images: ["https://images.unsplash.com/photo-1620641788220-410a7114b036?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHw1fHxtYW5pY3VyZSUyMHBlZGljdXJlfGVufDB8fHx8MTcwNzY5NDQ1MHww&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1597944768619-26c731b0516b?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwyfHxwZWlyJTIwcHJvZHVjdHxlbnwwfHx8fDE3MDc2OTUzOTZ8MA&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 4, name: "Relojeando.com", type: "minorista", categories: ["Accesorios", "Joyería"], address: "Av. Libertador Bernardo O'Higgins 2963, local 44, 1ER piso, Santiago Centro", lat: -33.4475, lon: -70.6720, commune: "Santiago Centro", contact: { instagram: "relojeandocom", whatsapp: "+56975961163" }, description: "Tienda especializada en relojes y accesorios de joyería, con local físico y presencia en redes.", images: ["https://images.unsplash.com/photo-1622668516139-44f2c9c73b06?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHx3YXRjaCUyMGx1eHVyeXxlbnwwfHx8fDE3MDc2OTQ0NzV8MA&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1585314062759-944175943976?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwzfHxqZXdlbHJ5JTIwc2hvcHxlbnwwfHx8fDE3MDc2OTU0NDV8MA&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 5, name: "Elly Fashion", type: "mayorista", categories: ["Cosméticos"], address: "Conferencia 281, Santiago Centro", lat: -33.4503, lon: -70.6784, commune: "Santiago Centro", contact: { instagram: "ellyfashion_281", whatsapp: "+56977888338" }, description: "Proveedor mayorista de maquillaje y artículos de belleza con local en Santiago Centro.", images: ["https://images.unsplash.com/photo-1571765275038-0ed71261a86a?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwxfHxtYWtldXAlMjBhcnRpc3R8ZW58MHx8fHwxNzA3Njk0NTE5fDA&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1595854999822-115341d24093?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHw1fHxtYWtldXAlMjBzaG9wfGVufDB8fHx8MTcwNzY5NTQ3M3ww&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 6, name: "Maroe Ropa Interior", type: "mayorista", categories: ["Ropa"], address: "Av. Buzeta 3558, Estación Central", lat: -33.4729, lon: -70.6924, commune: "Estación Central", contact: { instagram: "Maroeoficial", whatsapp: "+56920893387" }, description: "Proveedor de ropa interior al por mayor ubicado en Estación Central.", images: ["https://images.unsplash.com/photo-1582299092823-388a183dc3a1?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxsaW5nZXJpZSUyMHNob3B8ZW58MHx8fHwxNzA3Njk0NTM2fDA&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1613105664977-d38893b03342?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwyfHxsaW5nZXJpZSUyMHNob3B8ZW58MHx8fHwxNzA3Njk1NTAxMDA&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 7, name: "Girls Tops", type: "mayorista", categories: ["Ropa"], address: "Patronato #232, Recoleta", lat: -33.4284, lon: -70.6425, commune: "Recoleta", contact: { instagram: "Girlstop.cl", whatsapp: "+56930605860" }, description: "Tienda especializada en tops femeninos al por mayor en el corazón del barrio Patronato.", images: ["https://images.unsplash.com/photo-1594966699313-0599a09c2b4c?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwxfHxmYXNoaW9uJTIwYm91dGlxdWV8ZW58MHx8fHwxNzA3Njk0NTY0fDA&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1585487000169-69951893c401?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwyfHxmYXNoaW9uJTIwc2hvcHxlbnwwfHx8fDE3MDc2OTU1MjYwMA&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 8, name: "Diabolo Jeans", type: "mayorista", categories: ["Ropa"], address: "Av. Libertador Bernardo O'Higgins #2702, local 23, Santiago", lat: -33.4474, lon: -70.6721, commune: "Santiago Centro", contact: { instagram: "diabolojeans", whatsapp: "+56920625810" }, description: "Venta al por mayor de jeans para mujer, con local físico.", images: ["https://images.unsplash.com/photo-1541099645167-bb7860183416?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxkZW5pbSUyMGZhc2hpb258ZW58MHx8fHwxNzA3Njk0NTkzfDA&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1589848184672-a73440d39953?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwyfHxkZW5pbSUyMGZhc2hpb258ZW58MHx8fHwxNzA3Njk1NTU0fDA&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 9, name: "CELLINE", type: "mayorista", categories: ["Calzado"], address: "Avenida Grajales 2952, Santiago Centro", lat: -33.4533, lon: -70.6775, commune: "Santiago Centro", contact: {}, description: "Mayorista de calzado que opera exclusivamente con compra presencial. No tienen contacto por WhatsApp ni realizan envíos a regiones. Requieren una compra mínima de 12 pares por color.", images: ["https://images.unsplash.com/photo-1594966699313-0599a09c2b4c?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxmYXNoaW9uJTIwYm91dGlxdWV8ZW58MHx8fHwxNzA3Njk0NTY0fDA&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1533667634358-00a8a6669c3a?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxmZW1hbGUlMjBzaG9lcyUyMHN0b3JlfGVufDB8fHx8MTcwNzY5NDYyNHww&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1600267175161-cfa409754177?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwyfHxwZW5nyUyMHNob2VzfGVufDB8fHx8MTcwNzY5NTYxN3ww&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 10, name: "CHICBOMB", type: "mayorista", categories: ["Calzado"], address: "Avenida Libertad 22, Local 4, Santiago Centro", lat: -33.4520, lon: -70.6760, commune: "Santiago Centro", contact: { whatsapp: "+56933160240" }, description: "Mayorista de calzado con una amplia variedad de estilos, incluyendo botines, sandalias y zapatos más casuales. Ofrecen precios al por mayor.", images: ["https://images.unsplash.com/photo-1582299092823-388a183dc3a1?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxzaG9lcyUyMHN0b3JlfGVufDB8fHx8MTcwNzY5NDY1MHww&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1558509420-a61f221434c4?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwyfHxzaG9lcyUyMHN0b3JlfGVufDB8fHx8MTcwNzY5NDc0NXww&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1546868871-7041f2a55e4d?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHw0fHxzYW5kYWxzJTIwc2hvcHxlbnwwfHx8fDE3MDc2OTU2NjN8MA&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 11, name: "SUPERMODA", type: "mayorista", categories: ["Calzado"], address: "Avenida Libertador Bernardo O'Higgins 2963, Local 56, Santiago Centro", lat: -33.4476, lon: -70.6722, commune: "Santiago Centro", contact: { whatsapp: "+56999680210" }, description: "Amplia variedad de calzado al por mayor, con ofertas por la compra de múltiples unidades. Ofrecen estilos para diferentes ocasiones.", images: ["https://images.unsplash.com/photo-1543163777-6e4761dc1378?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxzaG9lcyUyMHNob3B8ZW58MHx8fDE3MDc2OTQ3NjF8MA&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1514989914725-fb355a242f2b?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxzaG9lcyUyMHN0b3JlfGVufDB8fHx8MTcwNzY5NDc3OHww&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1586315062953-108c78be1660?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxiYWxsZXJpbmElMjBzaG9lc3xlbnwwfHx8fDE3MDc2OTU2OTN8MA&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 12, name: "GANZY", type: "mayorista", categories: ["Ropa"], address: "Manzano 323, Recoleta", lat: -33.4258, lon: -70.6433, commune: "Recoleta", contact: { whatsapp: "+56932016040" }, description: "Nuevo proveedor especializado en vestuario femenino, ideal para boutiques que buscan vestidos de fiesta y ropa de mujer.", images: ["https://images.unsplash.com/photo-1551000628-a53d10200889?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxmb3JtYWwlMjBkcmVzc3xlbnwwfHx8fDE3MDc2OTU3MTUfMA&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1580958743876-17c75840c2f3?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwyfHxmYXNoaW9uJTIwZHJlc3Nlc3xlbnwwfHx8fDE3MDc2OTU3MTh8MA&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 13, name: "Munditel Chile 2020", type: "minorista", categories: ["Tecnología"], address: "San Alfonso 536, Santiago Centro", lat: -33.4566, lon: -70.6766, commune: "Santiago Centro", contact: { instagram: "munditelchile2020", whatsapp: "+56940493498" }, description: "Tienda enfocada en productos tecnológicos, ideal para encontrar accesorios y gadgets para celulares.", images: ["https://images.unsplash.com/photo-1620641788220-410a7114b036?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxlbGVjdHJvbmljcyUyMHN0b3JlfGVufDB8fHx8MTcwNzY5NDgzNHww&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1517054597858-636c13414322?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwyfHxlbGVjdHJvbmljcyUyMHN0b3JlfGVufDB8fHx8MTcwNzY5NTc0N3ww&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 14, name: "Joyería New (Blanchardley)", type: "minorista", categories: ["Joyería"], address: "Av. Providencia 2348, local 39, Providencia", lat: -33.4206, lon: -70.6090, commune: "Providencia", contact: { instagram: "blanchardley_materiales", whatsapp: "+56940811422" }, description: "Ofrecen materiales para la creación de joyas y posiblemente piezas terminadas. Con locales en Providencia y Ñuñoa.", images: ["https://images.unsplash.com/photo-1628172948633-cf584a7536d5?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxqZXdlbHJ5JTIwc2hvcHxlbnwwfHx8fDE3MDc2OTQ4OTJ8MA&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1523275335684-37898b6baf30?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwyfHxqZXdlbHJ5JTIwc2hvcHxlbnwwfHx8fDE3MDc2OTU3NzR8MA&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 15, name: "Dancy_shoes", type: "mayorista", categories: ["Calzado"], address: "Libertad 22, Santiago Centro", lat: -33.4521, lon: -70.6761, commune: "Santiago Centro", contact: { instagram: "dancy_shoes", whatsapp: "+56962102281" }, description: "Proveedor mayorista de calzado con una amplia variedad y contacto directo por redes sociales.", images: ["https://images.unsplash.com/photo-1596701021435-ff682b130b9d?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxzaG9lcyUyMHNob3B8ZW58MHx8fDE3MDc2OTQzOTd8MA&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1547592166-23ac45744052?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwzfHxmZW1hbGUlMjBzaG9lcyUyMHN0b3JlfGVufDB8fHx8MTcwNzY5NTgwN3ww&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 16, name: "Calzados FREEDOMM", type: "mayorista", categories: ["Calzado"], address: "San Antonio #422, Santiago Centro", lat: -33.4385, lon: -70.6489, commune: "Santiago Centro", contact: { instagram: "tiendafreedom.cl", whatsapp: "+56930962432" }, description: "Tienda de calzado al por mayor con local físico en el centro de Santiago.", images: ["https://images.unsplash.com/photo-1556910609-bc361b7b0553?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxmb290d2VhciUyMHN0b3JlfGVufDB8fHx8MTcwNzY5NDk3NHww&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1576766527323-f389710a921c?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxmb290d2VhciUyMHN0b3JlfGVufDB8fHx8MTcwNzY5NTgzM3ww&ixlib=rb-4.0.3&q=80&w=400"] },
        { id: 17, name: "OH BAGS", type: "mayorista", categories: ["Accesorios"], address: "Alameda 2963-2971, Mall Asia Pacífico local L19-23, Santiago Centro", lat: -33.4473, lon: -70.6723, commune: "Santiago Centro", contact: { whatsapp: "+56999968888" }, description: "Venta al por mayor de carteras, con presencia en redes y local físico en el Mall Asia Pacífico.", images: ["https://images.unsplash.com/photo-1596489370617-640a3237a3c3?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxjdXRlJTIwaGFuZGJhZ3N8ZW58MHx8fHwxNzA3Njk1ODg3fDA&ixlib=rb-4.0.3&q=80&w=400", "https://images.unsplash.com/photo-1571402869073-386879514182?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHxzaG9lcyUyMHNob3B8ZW58MHx8fDE3MDc2OTU4NjF8MA&ixlib=rb-4.0.3&q=80&w=400"] }
    ];

    document.addEventListener('DOMContentLoaded', () => {
        let mapInitialized = false; 
        let mapInstance; 
        let markersLayer; 

        const heroLanding = document.getElementById('hero-landing');
        const mainAppContent = document.getElementById('main-app-content');
        const categoriesContainer = document.getElementById('categories-container');
        const modal = document.getElementById('store-modal');
        const modalContent = document.getElementById('modal-content');

        // Aplicar la animación de entrada al fondo de Santiago cuando la página carga
        heroLanding.classList.add('is-loaded');

        // Íconos de Font Awesome para cada categoría
        const categoryIcons = {
            'Cosméticos': 'fa-solid fa-gem',
            'Pestañas': 'fa-solid fa-eye',
            'Uñas': 'fa-solid fa-hand-sparkles',
            'Peluquería': 'fa-solid fa-scissors',
            'Ropa': 'fa-solid fa-shirt',
            'Calzado': 'fa-solid fa-shoe-prints',
            'Accesorios': 'fa-solid fa-hat-wizard',
            'Joyería': 'fa-solid fa-ring',
            'Tecnología': 'fa-solid fa-mobile-screen-button'
        };

        // Función para inicializar y renderizar el mapa
        const initMapAndRender = () => {
            if (!mapInitialized) {
                mapInstance = L.map('map').setView([-33.44, -70.65], 13);
                L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png', {
                    attribution: '© <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors © <a href="https://carto.com/attributions">CARTO</a>'
                }).addTo(mapInstance);
                markersLayer = L.layerGroup().addTo(mapInstance);
                renderMapMarkers();
                mapInitialized = true;
            } else {
                mapInstance.invalidateSize();
            }
        };

        // Agrupa las tiendas por categoría
        const groupStoresByCategory = () => {
            const grouped = {};
            storesData.forEach(store => {
                store.categories.forEach(category => {
                    if (!grouped[category]) {
                        grouped[category] = [];
                    }
                    grouped[category].push(store);
                });
            });
            return grouped;
        };

        // Renderiza las secciones de categorías y tiendas
        const renderCategoriesAndStores = () => {
            const groupedStores = groupStoresByCategory();
            const categoryOrder = ['Cosméticos', 'Pestañas', 'Uñas', 'Peluquería', 'Ropa', 'Calzado', 'Accesorios', 'Joyería', 'Tecnología'];
            
            const sortedCategories = Object.keys(groupedStores).sort((a, b) => {
                const indexA = categoryOrder.indexOf(a);
                const indexB = categoryOrder.indexOf(b);
                if (indexA === -1) return 1; 
                if (indexB === -1) return -1;
                return indexA - indexB;
            });

            categoriesContainer.innerHTML = ''; 
            sortedCategories.forEach(category => {
                const stores = groupedStores[category];
                const categorySection = document.createElement('div');
                categorySection.className = 'section-fade-in'; 
                const iconClass = categoryIcons[category] || 'fa-solid fa-store'; 

                categorySection.innerHTML = `
                    <h3 class="font-serif text-3xl font-bold mb-8 text-gray-900 text-center flex items-center justify-center gap-4">
                        <i class="${iconClass} text-gray-500"></i>
                        <span>${category}</span>
                    </h3>
                    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-8">
                        ${stores.map(store => `
                            <div class="bg-white rounded-xl shadow-md flex flex-col group overflow-hidden cursor-pointer h-full border border-gray-100 hover:shadow-lg transition-shadow duration-300" onclick="showStoreDetails(${store.id})">
                                <div class="relative w-full h-48 overflow-hidden rounded-t-xl">
                                    <img src="${store.images[0] || 'https://images.unsplash.com/photo-1549247754-072a3922718e?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w1NzIyNTZ8MHwxfHNlYXJjaHwxfHxnZW5lcmFsJTIwc2hvcHxlbnwwfHx8fDE3MDc2OTU2MzF8MA&ixlib=rb-4.0.3&q=80&w=400'}" 
                                         alt="${store.name}" 
                                         class="object-cover w-full h-full transform group-hover:scale-105 transition-transform duration-300 ease-in-out">
                                    <div class="absolute inset-0 bg-gradient-to-t from-black/60 to-transparent flex items-end p-4 opacity-0 group-hover:opacity-100 transition-opacity duration-300">
                                        <p class="text-white text-lg font-bold">${store.name}</p>
                                    </div>
                                </div>
                                <div class="p-6 flex flex-col flex-grow">
                                    <h4 class="font-bold text-xl text-gray-800">${store.name}</h4>
                                    <p class="text-sm text-gray-500 mb-3 flex items-center mt-1">
                                        <i class="fas fa-map-marker-alt mr-2"></i>
                                        ${store.address}
                                    </p>
                                    <div class="mt-auto pt-4 border-t border-gray-100 text-right">
                                        <span class="text-blue-600 font-semibold text-sm hover:text-blue-800">
                                            Ver Detalles <i class="fas fa-arrow-right ml-1"></i>
                                        </span>
                                    </div>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                `;
                categoriesContainer.appendChild(categorySection);
            });
        };
        
        // Renderiza los marcadores en el mapa
        const renderMapMarkers = () => {
            markersLayer.clearLayers();
            storesData.forEach(store => {
                if (store.lat && store.lon) {
                    const icon = L.divIcon({
                        html: '<i class="fas fa-map-marker-alt text-4xl text-blue-500 drop-shadow-md"></i>',
                        className: 'bg-transparent border-0',
                        iconSize: [40, 40],
                        iconAnchor: [20, 40]
                    });
                    const marker = L.marker([store.lat, store.lon], { icon: icon })
                        .addTo(markersLayer)
                        .bindPopup(`
                            <b class="text-gray-800 text-lg">${store.name}</b><br>
                            <span class="text-gray-600">${store.address}</span><br>
                            <button class="text-blue-600 hover:text-blue-800 mt-2 text-sm" onclick="showStoreDetails(${store.id})">Ver detalles</button>
                        `);
                    
                    marker.on('click', () => showStoreDetails(store.id));
                }
            });
        };

        // Muestra los detalles de la tienda en el modal
        window.showStoreDetails = (id) => {
            const store = storesData.find(s => s.id === id);
            if (!store) return;
            
            modalContent.innerHTML = `
                <div class="p-6">
                    <div class="flex justify-between items-start mb-4">
                        <h2 class="font-serif text-3xl font-bold text-gray-900">${store.name}</h2>
                        <button onclick="closeModal()" class="text-gray-400 hover:text-gray-600 text-3xl leading-none">×</button>
                    </div>
                    <p class="text-sm text-gray-500 mb-4 flex items-center"><i class="fas fa-map-marker-alt mr-2"></i>${store.address}</p>
                    <div class="flex flex-wrap gap-2 mb-6">
                       <span class="bg-gray-100 text-gray-800 text-xs font-medium px-2.5 py-1 rounded-full capitalize">${store.type}</span>
                       ${store.categories.map(cat => `<span class="bg-gray-100 text-gray-800 text-xs font-medium px-2.5 py-1 rounded-full">${cat}</span>`).join('')}
                    </div>
                    
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-6">
                        ${store.images.map(img => `<img src="${img}" alt="${store.name}" class="rounded-lg object-cover w-full h-48 sm:h-40">`).join('')}
                    </div>

                    <p class="text-gray-700 mb-6 text-base">${store.description}</p>
                    
                    <div class="border-t border-gray-100 pt-4">
                        <h4 class="font-semibold text-gray-800 mb-3">Contacto</h4>
                        <div class="flex flex-wrap gap-x-6 gap-y-2">
                            ${store.contact.whatsapp ? `<a href="https://wa.me/${store.contact.whatsapp.replace(/\+/g, '')}" target="_blank" class="flex items-center space-x-2 text-blue-600 hover:text-blue-800 transition-colors"><i class="fab fa-whatsapp fa-lg"></i><span>WhatsApp</span></a>` : ''}
                            ${store.contact.instagram ? `<a href="https://instagram.com/${store.contact.instagram}" target="_blank" class="flex items-center space-x-2 text-blue-600 hover:text-blue-800 transition-colors"><i class="fab fa-instagram fa-lg"></i><span>Instagram</span></a>` : ''}
                            ${store.contact.web ? `<a href="${store.contact.web}" target="_blank" class="flex items-center space-x-2 text-blue-600 hover:text-blue-800 transition-colors"><i class="fas fa-globe fa-lg"></i><span>Sitio Web</span></a>` : ''}
                        </div>
                    </div>
                    ${store.address !== 'Online' && store.lat && store.lon ? `
                    <div class="mt-6">
                        <a href="https://www.google.com/maps/search/?api=1&query=${encodeURIComponent(store.address)}" target="_blank" class="inline-block w-full text-center bg-blue-500 text-white font-bold py-3 px-4 rounded-lg hover:bg-blue-600 transition-colors shadow-md">
                            Ver en Google Maps
                        </a>
                    </div>` : ''}
                </div>`;

            modal.classList.remove('hidden');
            modal.classList.add('flex');
            requestAnimationFrame(() => {
                modalContent.classList.remove('modal-enter');
                modalContent.classList.add('modal-enter-active');
            });
        };

        // Cierra el modal
        window.closeModal = () => {
            modalContent.classList.remove('modal-enter-active');
            modalContent.classList.add('modal-leave-active');
            setTimeout(() => {
                modal.classList.add('hidden');
                modal.classList.remove('flex');
                modalContent.classList.remove('modal-leave-active');
                modalContent.classList.add('modal-enter'); 
            }, 300); 
        };

        // Cerrar modal al hacer clic fuera del contenido
        modal.addEventListener('click', (e) => {
            if (e.target === modal) closeModal();
        });
        
        // --- Animación de secciones al hacer scroll ---
        const observer = new IntersectionObserver((entries, observer) => {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    entry.target.classList.add('is-visible');
                    observer.unobserve(entry.target);
                }
            });
        }, { threshold: 0.1 });

        // Manejador del clic en la sección Hero para revelar el contenido principal
        heroLanding.addEventListener('click', () => {
            heroLanding.classList.remove('is-loaded'); // Opcional: quitar la clase de carga si quieres animaciones inversas
            heroLanding.classList.add('opacity-0'); 
            setTimeout(() => {
                heroLanding.classList.add('hidden'); 
                mainAppContent.classList.remove('hidden'); 
                
                requestAnimationFrame(() => {
                    mainAppContent.classList.add('opacity-100'); 
                });

                initMapAndRender(); 
                renderCategoriesAndStores(); 

                document.querySelectorAll('#main-app-content .section-fade-in').forEach(section => {
                    observer.observe(section);
                });

                document.getElementById('map-section').scrollIntoView({ behavior: 'smooth' });

            }, 1000); 
        });
    });
    </script>
</body>
</html>
