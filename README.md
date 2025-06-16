<!DOCTYPE html>
<html lang="es" class="scroll-smooth">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Guía Comercial de Santiago - Edición Rosa</title>
    
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
            background-color: #FFF1F2; /* rose-50 */
        }
        .font-serif {
            font-family: 'Playfair Display', serif;
        }
        .section-fade-in {
            opacity: 0;
            transform: translateY(30px);
            transition: opacity 0.8s ease-out, transform 0.8s ease-out;
        }
        .section-fade-in.is-visible {
            opacity: 1;
            transform: translateY(0);
        }
        .leaflet-popup-content-wrapper { 
            border-radius: 8px; 
            background-color: #fff;
        }
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
        /* Added background pattern for more visual texture */
        .textured-bg {
            background-image: url('https://www.transparenttextures.com/patterns/light-paper-fibers.png');
        }
    </style>
</head>
<body class="text-rose-800">

    <!-- Hero Section -->
    <section class="min-h-screen flex flex-col items-center justify-center text-center p-8 bg-rose-50 textured-bg">
        <!-- SVG Logo -->
        <div class="mb-6">
            <svg class="w-24 h-24 text-rose-400" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM4.332 8.944a5.59 5.59 0 00-.584 3.954 5.59 5.59 0 003.58 3.58 5.59 5.59 0 003.954-.584l-5.32-5.32a.75.75 0 00-1.63.674v.001zM10 2a7.962 7.962 0 014.238 1.152l-5.39 5.39a.75.75 0 01-.674 1.63L2.848 5.762A7.962 7.962 0 0110 2zM14.238 14.848L8.848 9.458a.75.75 0 011.63-.674l5.39 5.39A7.962 7.962 0 0110 18a7.962 7.962 0 014.238-3.152z" clip-rule="evenodd"></path></svg>
        </div>
        <h1 class="font-serif text-5xl md:text-7xl font-bold text-rose-900">¿No sabes dónde ir a comprar EN LA CAPITALSSS?</h1>
        <p class="mt-4 text-lg text-rose-700 max-w-2xl">Hemos recopilado los mejores datos de proveedores en Santiago para que encuentres todo lo que necesitas.</p>
        <a href="#map-section" class="mt-12 animate-bounce">
            <i class="fas fa-chevron-down text-4xl text-rose-400"></i>
        </a>
    </section>

    <!-- Map Section -->
    <section id="map-section" class="py-16 sm:py-24 bg-white section-fade-in">
        <div class="container mx-auto px-6">
            <h2 class="font-serif text-4xl font-bold text-center mb-4 text-rose-900">Ubícalos en el Mapa</h2>
            <p class="text-center text-rose-700 max-w-3xl mx-auto mb-8">Explora las ubicaciones de las tiendas físicas. Haz clic en un marcador para ver más detalles.</p>
            <div id="map" class="h-[500px] w-full rounded-2xl shadow-lg border-4 border-white z-0"></div>
        </div>
    </section>

    <!-- Categories Section -->
    <section id="categories-section" class="py-16 sm:py-24 bg-rose-50 textured-bg">
        <div class="container mx-auto px-6">
             <h2 class="font-serif text-4xl font-bold text-center mb-4 text-rose-900">Explora por Categoría</h2>
             <p class="text-center text-rose-700 max-w-3xl mx-auto mb-12">Encuentra exactamente lo que buscas navegando por nuestras categorías de productos.</p>
            <div id="categories-container" class="space-y-16">
                <!-- Las categorías y tiendas se insertan aquí dinámicamente -->
            </div>
        </div>
    </section>
    
    <!-- Footer -->
    <footer class="bg-white py-8">
        <div class="text-center text-rose-600">
            <p>&copy; 2024 Guía Comercial Santiago. Todos los derechos reservados.</p>
        </div>
    </footer>


    <!-- Modal para Detalles de la Tienda -->
    <div id="store-modal" class="fixed inset-0 bg-black bg-opacity-60 hidden items-center justify-center p-4 z-50">
        <div id="modal-content" class="bg-white rounded-2xl shadow-2xl w-full max-w-lg max-h-[90vh] overflow-y-auto transform modal-enter">
            <!-- Contenido del modal se inserta aquí -->
        </div>
    </div>


    <script>
    const storesData = [
        { id: 1, name: "Romantic Beauty", type: "minorista", categories: ["Cosméticos"], address: "Online", lat: null, lon: null, contact: { web: "https://www.romanticbeauty.cl" }, description: "Tienda online especializada en maquillaje y productos de belleza, enfocada en la venta al detalle.", images: ["https://source.unsplash.com/random/400x300/?makeup,cosmetics"] },
        { id: 2, name: "Max Belleza", type: "minorista", categories: ["Cosméticos", "Uñas", "Pestañas"], address: "Online", lat: null, lon: null, contact: { web: "https://max-belleza.cl/tienda/" }, description: "Tienda online con una variada oferta en belleza, desde maquillaje hasta insumos para uñas y pestañas.", images: ["https://source.unsplash.com/random/400x300/?nail,salon", "https://source.unsplash.com/random/400x300/?eyelash,extension"] },
        { id: 3, name: "Mei Moda", type: "mayorista", categories: ["Pestañas", "Uñas", "Peluquería"], address: "Online", lat: null, lon: null, contact: { web: "https://meidemoda.cl/", whatsapp: "+56966966966" }, description: "Proveedor integral para profesionales de la belleza, con amplia gama de productos para pestañas, uñas y peluquería.", images: ["https://source.unsplash.com/random/400x300/?manicure,pedicure"] },
        { id: 4, name: "Relojeando.com", type: "minorista", categories: ["Accesorios", "Joyería"], address: "Av. Libertador Bernardo O'Higgins 2963, local 44, 1ER piso, Santiago Centro", lat: -33.4475, lon: -70.6720, commune: "Santiago Centro", contact: { instagram: "relojeandocom", whatsapp: "+56975961163" }, description: "Tienda especializada en relojes y accesorios de joyería, con local físico y presencia en redes.", images: ["https://source.unsplash.com/random/400x300/?watch,luxury"] },
        { id: 5, name: "Elly Fashion", type: "mayorista", categories: ["Cosméticos"], address: "Conferencia 281, Santiago Centro", lat: -33.4503, lon: -70.6784, commune: "Santiago Centro", contact: { instagram: "ellyfashion_281", whatsapp: "+56977888338" }, description: "Proveedor mayorista de maquillaje y artículos de belleza con local en Santiago Centro.", images: ["https://source.unsplash.com/random/400x300/?makeup,palette"] },
        { id: 6, name: "Maroe Ropa Interior", type: "mayorista", categories: ["Ropa"], address: "Av. Buzeta 3558, Estación Central", lat: -33.4729, lon: -70.6924, commune: "Estación Central", contact: { instagram: "Maroeoficial", whatsapp: "+56920893387" }, description: "Proveedor de ropa interior al por mayor ubicado en Estación Central.", images: ["https://source.unsplash.com/random/400x300/?lingerie,fashion"] },
        { id: 7, name: "Girls Tops", type: "mayorista", categories: ["Ropa"], address: "Patronato #232, Recoleta", lat: -33.4284, lon: -70.6425, commune: "Recoleta", contact: { instagram: "Girlstop.cl", whatsapp: "+56930605860" }, description: "Tienda especializada en tops femeninos al por mayor en el corazón del barrio Patronato.", images: ["https://source.unsplash.com/random/400x300/?fashion,top"] },
        { id: 8, name: "Diabolo Jeans", type: "mayorista", categories: ["Ropa"], address: "Av. Libertador Bernardo O'Higgins #2702, local 23, Santiago", lat: -33.4474, lon: -70.6721, commune: "Santiago Centro", contact: { instagram: "diabolojeans", whatsapp: "+56920625810" }, description: "Venta al por mayor de jeans para mujer, con local físico.", images: ["https://source.unsplash.com/random/400x300/?jeans,denim"] },
        { id: 9, name: "CELLINE", type: "mayorista", categories: ["Calzado"], address: "Avenida Grajales 2952, Santiago Centro", lat: -33.4533, lon: -70.6775, commune: "Santiago Centro", contact: {}, description: "Mayorista de calzado que opera exclusivamente con compra presencial. No tienen contacto por WhatsApp ni realizan envíos a regiones. Requieren una compra mínima de 12 pares por color.", images: ["https://source.unsplash.com/random/400x300/?shoes,heels", "https://source.unsplash.com/random/400x300/?sandals,fashion"] },
        { id: 10, name: "CHICBOMB", type: "mayorista", categories: ["Calzado"], address: "Avenida Libertad 22, Local 4, Santiago Centro", lat: -33.4520, lon: -70.6760, commune: "Santiago Centro", contact: { whatsapp: "+56933160240" }, description: "Mayorista de calzado con una amplia variedad de estilos, incluyendo botines, sandalias y zapatos más casuales. Ofrecen precios al por mayor.", images: ["https://source.unsplash.com/random/400x300/?boots,fashion", "https://source.unsplash.com/random/400x300/?loafers,shoes"] },
        { id: 11, name: "SUPERMODA", type: "mayorista", categories: ["Calzado"], address: "Avenida Libertador Bernardo O'Higgins 2963, Local 56, Santiago Centro", lat: -33.4476, lon: -70.6722, commune: "Santiago Centro", contact: { whatsapp: "+56999680210" }, description: "Amplia variedad de calzado al por mayor, con ofertas por la compra de múltiples unidades. Ofrecen estilos para diferentes ocasiones.", images: ["https://source.unsplash.com/random/400x300/?high,heels", "https://source.unsplash.com/random/400x300/?sneakers,style"] },
        { id: 12, name: "GANZY", type: "mayorista", categories: ["Ropa"], address: "Manzano 323, Recoleta", lat: -33.4258, lon: -70.6433, commune: "Recoleta", contact: { whatsapp: "+56932016040" }, description: "Nuevo proveedor especializado en vestuario femenino, ideal para boutiques que buscan vestidos de fiesta y ropa de mujer.", images: ["https://source.unsplash.com/random/400x300/?party,dress"] },
        { id: 13, name: "Munditel Chile 2020", type: "minorista", categories: ["Tecnología"], address: "San Alfonso 536, Santiago Centro", lat: -33.4566, lon: -70.6766, commune: "Santiago Centro", contact: { instagram: "munditelchile2020", whatsapp: "+56940493498" }, description: "Tienda enfocada en productos tecnológicos, ideal para encontrar accesorios y gadgets para celulares.", images: ["https://source.unsplash.com/random/400x300/?tech,gadgets"] },
        { id: 14, name: "Joyería New (Blanchardley)", type: "minorista", categories: ["Joyería"], address: "Av. Providencia 2348, local 39, Providencia", lat: -33.4206, lon: -70.6090, commune: "Providencia", contact: { instagram: "blanchardley_materiales", whatsapp: "+56940811422" }, description: "Ofrecen materiales para la creación de joyas y posiblemente piezas terminadas. Con locales en Providencia y Ñuñoa.", images: ["https://source.unsplash.com/random/400x300/?jewelry,craft"] },
        { id: 15, name: "Dancy_shoes", type: "mayorista", categories: ["Calzado"], address: "Libertad 22, Santiago Centro", lat: -33.4521, lon: -70.6761, commune: "Santiago Centro", contact: { instagram: "dancy_shoes", whatsapp: "+56962102281" }, description: "Proveedor mayorista de calzado con una amplia variedad y contacto directo por redes sociales.", images: ["https://source.unsplash.com/random/400x300/?shoes,store"] },
        { id: 16, name: "Calzados FREEDOMM", type: "mayorista", categories: ["Calzado"], address: "San Antonio #422, Santiago Centro", lat: -33.4385, lon: -70.6489, commune: "Santiago Centro", contact: { instagram: "tiendafreedom.cl", whatsapp: "+56930962432" }, description: "Tienda de calzado al por mayor con local físico en el centro de Santiago.", images: ["https://source.unsplash.com/random/400x300/?footwear,shop"] },
        { id: 17, name: "OH BAGS", type: "mayorista", categories: ["Accesorios"], address: "Alameda 2963-2971, Mall Asia Pacífico local L19-23, Santiago Centro", lat: -33.4473, lon: -70.6723, commune: "Santiago Centro", contact: { whatsapp: "+56999968888" }, description: "Venta al por mayor de carteras, con presencia en redes y local físico en el Mall Asia Pacífico.", images: ["https://source.unsplash.com/random/400x300/?handbags,collection"] }
    ];

    document.addEventListener('DOMContentLoaded', () => {
        const map = L.map('map').setView([-33.44, -70.65], 13);
        L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors &copy; <a href="https://carto.com/attributions">CARTO</a>'
        }).addTo(map);

        const categoriesContainer = document.getElementById('categories-container');
        const modal = document.getElementById('store-modal');
        const modalContent = document.getElementById('modal-content');
        let markersLayer = L.layerGroup().addTo(map);

        // Map categories to Font Awesome icons
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

            sortedCategories.forEach(category => {
                const stores = groupedStores[category];
                const categorySection = document.createElement('div');
                categorySection.className = 'section-fade-in';
                const iconClass = categoryIcons[category] || 'fa-solid fa-store'; // Fallback icon

                categorySection.innerHTML = `
                    <h3 class="font-serif text-3xl font-bold mb-8 text-rose-900 text-center flex items-center justify-center gap-4">
                        <i class="${iconClass} text-rose-400"></i>
                        <span>${category}</span>
                    </h3>
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
                        ${stores.map(store => `
                            <div class="bg-white rounded-xl shadow-md p-6 flex flex-col hover:shadow-xl hover:-translate-y-1 transition-all duration-300 cursor-pointer" onclick="showStoreDetails(${store.id})">
                                <h4 class="font-bold text-xl text-rose-800">${store.name}</h4>
                                <p class="text-sm text-rose-500 mb-3 flex items-center mt-1">
                                    <i class="fas fa-map-marker-alt mr-2"></i>
                                    ${store.address}
                                </p>
                                <div class="mt-auto pt-4 border-t border-rose-100 text-right">
                                    <span class="text-rose-600 font-semibold text-sm hover:text-rose-800">
                                        Ver Detalles <i class="fas fa-arrow-right ml-1"></i>
                                    </span>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                `;
                categoriesContainer.appendChild(categorySection);
            });
        };
        
        const renderMapMarkers = () => {
            markersLayer.clearLayers();
            storesData.forEach(store => {
                if (store.lat && store.lon) {
                    const icon = L.divIcon({
                        html: '<i class="fas fa-map-marker-alt text-3xl text-rose-500"></i>',
                        className: 'bg-transparent border-0',
                        iconSize: [30, 30],
                        iconAnchor: [15, 30]
                    });
                    const marker = L.marker([store.lat, store.lon], { icon: icon })
                        .addTo(markersLayer)
                        .bindPopup(`<b class="text-rose-800">${store.name}</b><br><button class="text-rose-600" onclick="showStoreDetails(${store.id})">Ver detalles</button>`);
                    
                    marker.on('click', () => showStoreDetails(store.id));
                }
            });
        };

        window.showStoreDetails = (id) => {
            const store = storesData.find(s => s.id === id);
            if (!store) return;
            
            modalContent.innerHTML = `
                <div class="p-6">
                    <div class="flex justify-between items-start">
                        <h2 class="font-serif text-3xl font-bold text-rose-900 mb-2">${store.name}</h2>
                        <button onclick="closeModal()" class="text-rose-400 hover:text-rose-600 text-3xl">&times;</button>
                    </div>
                    <p class="text-sm text-rose-500 mb-4 flex items-center"><i class="fas fa-map-marker-alt mr-2"></i>${store.address}</p>
                    <div class="flex flex-wrap gap-2 mb-4">
                       <span class="bg-rose-100 text-rose-800 text-xs font-medium px-2.5 py-1 rounded-full capitalize">${store.type}</span>
                       ${store.categories.map(cat => `<span class="bg-rose-100 text-rose-800 text-xs font-medium px-2.5 py-1 rounded-full">${cat}</span>`).join('')}
                    </div>
                    <p class="text-rose-700 mb-6">${store.description}</p>
                    
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-6">
                       ${store.images.map(img => `<img src="${img}" alt="${store.name}" class="rounded-lg object-cover w-full h-40">`).join('')}
                    </div>
                    
                    <div class="border-t border-rose-100 pt-4">
                        <h4 class="font-semibold text-rose-800 mb-3">Contacto</h4>
                        <div class="flex flex-wrap gap-x-6 gap-y-2">
                            ${store.contact.whatsapp ? `<a href="https://wa.me/${store.contact.whatsapp.replace(/\+/g, '')}" target="_blank" class="flex items-center space-x-2 text-rose-600 hover:text-green-500"><i class="fab fa-whatsapp fa-lg"></i><span>WhatsApp</span></a>` : ''}
                            ${store.contact.instagram ? `<a href="https://instagram.com/${store.contact.instagram}" target="_blank" class="flex items-center space-x-2 text-rose-600 hover:text-pink-500"><i class="fab fa-instagram fa-lg"></i><span>Instagram</span></a>` : ''}
                            ${store.contact.web ? `<a href="${store.contact.web}" target="_blank" class="flex items-center space-x-2 text-rose-600 hover:text-blue-500"><i class="fas fa-globe fa-lg"></i><span>Sitio Web</span></a>` : ''}
                        </div>
                    </div>
                     ${store.address !== 'Online' ? `
                     <div class="mt-6">
                         <a href="https://www.google.com/maps/search/?api=1&query=${encodeURIComponent(store.address)}" target="_blank" class="inline-block w-full text-center bg-rose-500 text-white font-bold py-3 px-4 rounded-lg hover:bg-rose-600 transition-colors">
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

        window.closeModal = () => {
            modalContent.classList.remove('modal-enter-active');
            modalContent.classList.add('modal-leave-active');
            setTimeout(() => {
                modal.classList.add('hidden');
                modal.classList.remove('flex');
                modalContent.classList.remove('modal-leave-active');
                modalContent.classList.add('modal-enter'); // Reset for next open
            }, 300);
        };

        modal.addEventListener('click', (e) => {
            if (e.target === modal) closeModal();
        });
        
        // --- Animation on Scroll ---
        const observer = new IntersectionObserver((entries, observer) => {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    entry.target.classList.add('is-visible');
                    observer.unobserve(entry.target);
                }
            });
        }, { threshold: 0.1 });

        // Initial Calls
        renderCategoriesAndStores();
        renderMapMarkers();
        
        // Observe all sections for animation
        document.querySelectorAll('.section-fade-in').forEach(section => {
            observer.observe(section);
        });
    });
    </script>
</body>
</html>
