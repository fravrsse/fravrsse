## ES DE MIEDO O NO??👋
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Guía Comercial Interactiva de Santiago</title>
    
    <!-- Chosen Palette: Warm Neutrals (Alabaster, Almond, Pale Taupe, Liver Chestnut) -->
    <!-- Application Structure Plan: Se ha diseñado una SPA con un layout de dashboard. A la izquierda, una barra lateral con filtros (búsqueda, categoría, tipo, comuna) permite al usuario refinar la búsqueda. El área principal muestra las tiendas en formato de tarjetas interactivas. Al hacer clic en una tarjeta o en un marcador del mapa, un modal muestra los detalles completos de la tienda. Esta estructura fue elegida porque separa claramente las acciones de filtrar, explorar y profundizar, ofreciendo un flujo de usuario intuitivo y eficiente para manejar un gran volumen de datos como es un directorio. -->
    <!-- Visualization & Content Choices: Información del reporte: Lista de tiendas, contactos, direcciones, productos. | Objetivo: Organizar, comparar, informar. | Método de presentación: Tarjetas de tienda (HTML/CSS/JS) y un mapa interactivo (Leaflet.js). | Interacción: Filtros dinámicos que actualizan tanto la lista de tarjetas como los marcadores del mapa en tiempo real. Clics en tarjetas/marcadores abren un modal con detalles. | Justificación: Este enfoque transforma una lista estática en una herramienta de exploración dinámica, permitiendo a los usuarios encontrar información relevante rápidamente. El mapa añade un contexto geográfico crucial. | Librerías: Tailwind CSS, Leaflet.js, Font Awesome. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->

    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin=""/>
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #f3f4f6; }
        .map-container { position: relative; width: 100%; height: 400px; max-height: 50vh; }
        @media (min-width: 768px) { .map-container { height: 100%; max-height: calc(100vh - 120px); } }
        .leaflet-popup-content-wrapper { border-radius: 8px; }
        .filter-btn.active { background-color: #4f46e5; color: white; }
        .filter-btn { transition: all 0.2s ease-in-out; }
    </style>
</head>
<body class="bg-gray-100 text-gray-800">

    <div id="app" class="flex flex-col md:flex-row min-h-screen">
        
        <!-- Sidebar de Filtros -->
        <aside class="w-full md:w-80 lg:w-96 bg-white p-6 border-r border-gray-200 flex-shrink-0">
            <h1 class="text-2xl font-bold text-gray-900 mb-1">Guía Comercial</h1>
            <p class="text-gray-500 mb-6">Encuentra proveedores en Santiago.</p>

            <div class="space-y-6">
                <div>
                    <label for="search" class="block text-sm font-medium text-gray-700 mb-1">Buscar por nombre</label>
                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 flex items-center pl-3">
                            <i class="fa fa-search text-gray-400"></i>
                        </span>
                        <input type="text" id="search" placeholder="Ej: Celline, Supermoda..." class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded-lg focus:ring-indigo-500 focus:border-indigo-500">
                    </div>
                </div>

                <div>
                    <h3 class="text-sm font-medium text-gray-700 mb-2">Tipo de Tienda</h3>
                    <div id="type-filters" class="flex space-x-2">
                        <button data-filter-group="type" data-filter="todos" class="filter-btn flex-1 py-2 px-4 border border-gray-300 rounded-lg text-sm active">Todos</button>
                        <button data-filter-group="type" data-filter="minorista" class="filter-btn flex-1 py-2 px-4 border border-gray-300 rounded-lg text-sm">Minoristas</button>
                        <button data-filter-group="type" data-filter="mayorista" class="filter-btn flex-1 py-2 px-4 border border-gray-300 rounded-lg text-sm">Mayoristas</button>
                    </div>
                </div>

                <div>
                    <h3 class="text-sm font-medium text-gray-700 mb-2">Categorías</h3>
                    <div id="category-filters" class="flex flex-wrap gap-2">
                        <!-- Botones de categoría se insertan aquí -->
                    </div>
                </div>
                
                <div>
                    <h3 class="text-sm font-medium text-gray-700 mb-2">Comunas</h3>
                    <div id="commune-filters" class="flex flex-wrap gap-2">
                         <!-- Botones de comuna se insertan aquí -->
                    </div>
                </div>
            </div>
            
            <div id="results-count" class="mt-8 text-sm text-center text-gray-500"></div>
        </aside>

        <!-- Contenido Principal (Mapa y Tarjetas) -->
        <main class="flex-1 p-6 grid grid-cols-1 lg:grid-cols-2 gap-6">
            <div id="map" class="map-container rounded-lg shadow-md z-0"></div>
            <div class="overflow-y-auto" style="max-height: calc(100vh - 120px);">
                <div id="store-cards" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-1 gap-4">
                    <!-- Tarjetas de tienda se insertan aquí -->
                </div>
            </div>
        </main>
    </div>

    <!-- Modal para Detalles de la Tienda -->
    <div id="store-modal" class="fixed inset-0 bg-black bg-opacity-60 hidden items-center justify-center p-4 z-50">
        <div id="modal-content" class="bg-white rounded-xl shadow-2xl w-full max-w-lg max-h-[90vh] overflow-y-auto transform transition-all duration-300 scale-95 opacity-0">
            <!-- Contenido del modal se inserta aquí -->
        </div>
    </div>


    <script>
    const storesData = [
        {
            id: 1, name: "Romantic Beauty", type: "minorista", categories: ["Cosméticos"],
            address: "Online", lat: null, lon: null,
            contact: { web: "https://www.romanticbeauty.cl" },
            description: "Tienda online especializada en maquillaje y productos de belleza, enfocada en la venta al detalle.",
            images: ["https://source.unsplash.com/random/400x300/?makeup,cosmetics"]
        },
        {
            id: 2, name: "Max Belleza", type: "minorista", categories: ["Cosméticos", "Uñas", "Pestañas"],
            address: "Online", lat: null, lon: null,
            contact: { web: "https://max-belleza.cl/tienda/" },
            description: "Tienda online con una variada oferta en belleza, desde maquillaje hasta insumos para uñas y pestañas.",
            images: ["https://source.unsplash.com/random/400x300/?nail,salon", "https://source.unsplash.com/random/400x300/?eyelash,extension"]
        },
        {
            id: 3, name: "Mei Moda", type: "mayorista", categories: ["Pestañas", "Uñas", "Peluquería"],
            address: "Online", lat: null, lon: null,
            contact: { web: "https://meidemoda.cl/", whatsapp: "+56966966966" },
            description: "Proveedor integral para profesionales de la belleza, con amplia gama de productos para pestañas, uñas y peluquería.",
            images: ["https://source.unsplash.com/random/400x300/?manicure,pedicure"]
        },
        {
            id: 4, name: "Relojeando.com", type: "minorista", categories: ["Accesorios", "Joyería"],
            address: "Av. Libertador Bernardo O'Higgins 2963, local 44, 1ER piso, Santiago Centro", lat: -33.4475, lon: -70.6720,
            commune: "Santiago Centro",
            contact: { instagram: "relojeandocom", whatsapp: "+56975961163" },
            description: "Tienda especializada en relojes y accesorios de joyería, con local físico y presencia en redes.",
            images: ["https://source.unsplash.com/random/400x300/?watch,luxury"]
        },
        {
            id: 5, name: "Elly Fashion", type: "mayorista", categories: ["Cosméticos"],
            address: "Conferencia 281, Santiago Centro", lat: -33.4503, lon: -70.6784,
            commune: "Santiago Centro",
            contact: { instagram: "ellyfashion_281", whatsapp: "+56977888338" },
            description: "Proveedor mayorista de maquillaje y artículos de belleza con local en Santiago Centro.",
            images: ["https://source.unsplash.com/random/400x300/?makeup,palette"]
        },
        {
            id: 6, name: "Maroe Ropa Interior", type: "mayorista", categories: ["Ropa"],
            address: "Av. Buzeta 3558, Estación Central", lat: -33.4729, lon: -70.6924,
            commune: "Estación Central",
            contact: { instagram: "Maroeoficial", whatsapp: "+56920893387" },
            description: "Proveedor de ropa interior al por mayor ubicado en Estación Central.",
            images: ["https://source.unsplash.com/random/400x300/?lingerie,fashion"]
        },
        {
            id: 7, name: "Girls Tops", type: "mayorista", categories: ["Ropa"],
            address: "Patronato #232, Recoleta", lat: -33.4284, lon: -70.6425,
            commune: "Recoleta",
            contact: { instagram: "Girlstop.cl", whatsapp: "+56930605860" },
            description: "Tienda especializada en tops femeninos al por mayor en el corazón del barrio Patronato.",
            images: ["https://source.unsplash.com/random/400x300/?fashion,top"]
        },
        {
            id: 8, name: "Diabolo Jeans", type: "mayorista", categories: ["Ropa"],
            address: "Av. Libertador Bernardo O'Higgins #2702, local 23, Santiago", lat: -33.4474, lon: -70.6721,
            commune: "Santiago Centro",
            contact: { instagram: "diabolojeans", whatsapp: "+56920625810" },
            description: "Venta al por mayor de jeans para mujer, con local físico.",
            images: ["https://source.unsplash.com/random/400x300/?jeans,denim"]
        },
        {
            id: 9, name: "CELLINE", type: "mayorista", categories: ["Calzado"],
            address: "Avenida Grajales 2952, Santiago Centro", lat: -33.4533, lon: -70.6775,
            commune: "Santiago Centro",
            contact: {},
            description: "Mayorista de calzado que opera exclusivamente con compra presencial. No tienen contacto por WhatsApp ni realizan envíos a regiones. Requieren una compra mínima de 12 pares por color.",
            images: ["https://source.unsplash.com/random/400x300/?shoes,heels", "https://source.unsplash.com/random/400x300/?sandals,fashion"]
        },
        {
            id: 10, name: "CHICBOMB", type: "mayorista", categories: ["Calzado"],
            address: "Avenida Libertad 22, Local 4, Santiago Centro", lat: -33.4520, lon: -70.6760,
            commune: "Santiago Centro",
            contact: { whatsapp: "+56933160240" },
            description: "Mayorista de calzado con una amplia variedad de estilos, incluyendo botines, sandalias y zapatos más casuales. Ofrecen precios al por mayor.",
            images: ["https://source.unsplash.com/random/400x300/?boots,fashion", "https://source.unsplash.com/random/400x300/?loafers,shoes"]
        },
        {
            id: 11, name: "SUPERMODA", type: "mayorista", categories: ["Calzado"],
            address: "Avenida Libertador Bernardo O'Higgins 2963, Local 56, Santiago Centro", lat: -33.4476, lon: -70.6722,
            commune: "Santiago Centro",
            contact: { whatsapp: "+56999680210" },
            description: "Amplia variedad de calzado al por mayor, con ofertas por la compra de múltiples unidades. Ofrecen estilos para diferentes ocasiones.",
            images: ["https://source.unsplash.com/random/400x300/?high,heels", "https://source.unsplash.com/random/400x300/?sneakers,style"]
        },
        {
            id: 12, name: "GANZY", type: "mayorista", categories: ["Ropa"],
            address: "Manzano 323, Recoleta", lat: -33.4258, lon: -70.6433,
            commune: "Recoleta",
            contact: { whatsapp: "+56932016040" },
            description: "Nuevo proveedor especializado en vestuario femenino, ideal para boutiques que buscan vestidos de fiesta y ropa de mujer.",
            images: ["https://source.unsplash.com/random/400x300/?party,dress"]
        },
        {
            id: 13, name: "Munditel Chile 2020", type: "minorista", categories: ["Tecnología"],
            address: "San Alfonso 536, Santiago Centro", lat: -33.4566, lon: -70.6766,
            commune: "Santiago Centro",
            contact: { instagram: "munditelchile2020", whatsapp: "+56940493498" },
            description: "Tienda enfocada en productos tecnológicos, ideal para encontrar accesorios y gadgets para celulares.",
            images: ["https://source.unsplash.com/random/400x300/?tech,gadgets"]
        },
        {
            id: 14, name: "Joyería New (Blanchardley)", type: "minorista", categories: ["Joyería"],
            address: "Av. Providencia 2348, local 39, Providencia", lat: -33.4206, lon: -70.6090,
            commune: "Providencia",
            contact: { instagram: "blanchardley_materiales", whatsapp: "+56940811422" },
            description: "Ofrecen materiales para la creación de joyas y posiblemente piezas terminadas. Con locales en Providencia y Ñuñoa.",
            images: ["https://source.unsplash.com/random/400x300/?jewelry,craft"]
        },
        {
            id: 15, name: "Dancy_shoes", type: "mayorista", categories: ["Calzado"],
            address: "Libertad 22, Santiago Centro", lat: -33.4521, lon: -70.6761,
            commune: "Santiago Centro",
            contact: { instagram: "dancy_shoes", whatsapp: "+56962102281" },
            description: "Proveedor mayorista de calzado con una amplia variedad y contacto directo por redes sociales.",
            images: ["https://source.unsplash.com/random/400x300/?shoes,store"]
        },
        {
            id: 16, name: "Calzados FREEDOMM", type: "mayorista", categories: ["Calzado"],
            address: "San Antonio #422, Santiago Centro", lat: -33.4385, lon: -70.6489,
            commune: "Santiago Centro",
            contact: { instagram: "tiendafreedom.cl", whatsapp: "+56930962432" },
            description: "Tienda de calzado al por mayor con local físico en el centro de Santiago.",
            images: ["https://source.unsplash.com/random/400x300/?footwear,shop"]
        },
        {
            id: 17, name: "OH BAGS", type: "mayorista", categories: ["Accesorios"],
            address: "Alameda 2963-2971, Mall Asia Pacífico local L19-23, Santiago Centro", lat: -33.4473, lon: -70.6723,
            commune: "Santiago Centro",
            contact: { whatsapp: "+56999968888" },
            description: "Venta al por mayor de carteras, con presencia en redes y local físico en el Mall Asia Pacífico.",
            images: ["https://source.unsplash.com/random/400x300/?handbags,collection"]
        }
    ];

    document.addEventListener('DOMContentLoaded', () => {
        const map = L.map('map').setView([-33.44, -70.65], 13);
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(map);

        const storeCardsContainer = document.getElementById('store-cards');
        const categoryFiltersContainer = document.getElementById('category-filters');
        const communeFiltersContainer = document.getElementById('commune-filters');
        const typeFiltersContainer = document.getElementById('type-filters');
        const searchInput = document.getElementById('search');
        const resultsCount = document.getElementById('results-count');
        
        const modal = document.getElementById('store-modal');
        const modalContent = document.getElementById('modal-content');

        let markersLayer = L.layerGroup().addTo(map);
        let activeFilters = {
            type: 'todos',
            category: 'todos',
            commune: 'todos',
            search: ''
        };

        const getUniqueValues = (key) => {
            const values = storesData.map(store => store[key]).flat().filter(Boolean);
            return ['todos', ...new Set(values)];
        };

        const createFilterButtons = (container, values, groupName) => {
            container.innerHTML = values.map(value => `
                <button 
                    data-filter-group="${groupName}" 
                    data-filter="${value}" 
                    class="filter-btn capitalize py-1.5 px-3 border border-gray-300 rounded-full text-xs ${value === 'todos' ? 'active' : ''}">
                    ${value.replace(/_/g, ' ')}
                </button>
            `).join('');
        };
        
        const renderStoreCards = (stores) => {
            storeCardsContainer.innerHTML = stores.length ? stores.map(store => `
                <div class="bg-white rounded-lg shadow-sm p-4 flex flex-col hover:shadow-lg transition-shadow duration-300 cursor-pointer" onclick="showStoreDetails(${store.id})">
                    <h3 class="font-bold text-lg text-gray-900">${store.name}</h3>
                    <p class="text-sm text-gray-500 mb-3">${store.address}</p>
                    <div class="flex flex-wrap gap-2 mb-4">
                        ${store.categories.map(cat => `<span class="bg-indigo-100 text-indigo-700 text-xs font-medium px-2.5 py-1 rounded-full">${cat}</span>`).join('')}
                    </div>
                    <div class="mt-auto pt-2 border-t border-gray-200 text-right">
                        <span class="text-indigo-600 font-semibold text-sm">Ver Detalles &rarr;</span>
                    </div>
                </div>
            `).join('') : '<p class="text-gray-500 col-span-full text-center">No se encontraron tiendas con los filtros seleccionados.</p>';
            resultsCount.textContent = `${stores.length} resultado(s) encontrado(s).`;
        };
        
        const renderMapMarkers = (stores) => {
            markersLayer.clearLayers();
            stores.forEach(store => {
                if (store.lat && store.lon) {
                    const marker = L.marker([store.lat, store.lon])
                        .addTo(markersLayer)
                        .bindPopup(`<b>${store.name}</b><br><button class="text-indigo-600" onclick="showStoreDetails(${store.id})">Ver detalles</button>`);
                    marker.on('click', () => {
                       showStoreDetails(store.id);
                    });
                }
            });
        };

        window.showStoreDetails = (id) => {
            const store = storesData.find(s => s.id === id);
            if (!store) return;
            
            modalContent.innerHTML = `
                <div class="p-6">
                    <div class="flex justify-between items-start">
                        <h2 class="text-2xl font-bold text-gray-900 mb-2">${store.name}</h2>
                        <button onclick="closeModal()" class="text-gray-400 hover:text-gray-600">&times;</button>
                    </div>
                    <p class="text-sm text-gray-500 mb-4">${store.address}</p>
                    <div class="flex flex-wrap gap-2 mb-4">
                       <span class="bg-gray-200 text-gray-800 text-xs font-medium px-2.5 py-1 rounded-full capitalize">${store.type}</span>
                       ${store.categories.map(cat => `<span class="bg-indigo-100 text-indigo-700 text-xs font-medium px-2.5 py-1 rounded-full">${cat}</span>`).join('')}
                    </div>
                    <p class="text-gray-600 mb-6">${store.description}</p>
                    
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-6">
                       ${store.images.map(img => `<img src="${img}" alt="${store.name}" class="rounded-lg object-cover w-full h-32">`).join('')}
                    </div>
                    
                    <div class="border-t border-gray-200 pt-4">
                        <h4 class="font-semibold text-gray-800 mb-2">Contacto</h4>
                        <div class="flex items-center space-x-4">
                            ${store.contact.whatsapp ? `<a href="https://wa.me/${store.contact.whatsapp.replace(/\+/g, '')}" target="_blank" class="flex items-center space-x-2 text-gray-600 hover:text-green-500"><i class="fab fa-whatsapp"></i><span>WhatsApp</span></a>` : ''}
                            ${store.contact.instagram ? `<a href="https://instagram.com/${store.contact.instagram}" target="_blank" class="flex items-center space-x-2 text-gray-600 hover:text-pink-500"><i class="fab fa-instagram"></i><span>Instagram</span></a>` : ''}
                            ${store.contact.web ? `<a href="${store.contact.web}" target="_blank" class="flex items-center space-x-2 text-gray-600 hover:text-indigo-500"><i class="fas fa-globe"></i><span>Sitio Web</span></a>` : ''}
                        </div>
                    </div>
                     ${store.address !== 'Online' ? `
                     <div class="mt-4">
                         <a href="https://www.google.com/maps/search/?api=1&query=${encodeURIComponent(store.address)}" target="_blank" class="inline-block w-full text-center bg-indigo-600 text-white font-bold py-2 px-4 rounded-lg hover:bg-indigo-700 transition-colors">
                            Ver en Google Maps
                         </a>
                    </div>` : ''}
                </div>`;

            modal.classList.remove('hidden');
            modal.classList.add('flex');
            setTimeout(() => {
              modalContent.classList.remove('scale-95', 'opacity-0');
            }, 10);
        };

        window.closeModal = () => {
            modalContent.classList.add('scale-95', 'opacity-0');
            setTimeout(() => {
              modal.classList.add('hidden');
              modal.classList.remove('flex');
            }, 300);
        };

        modal.addEventListener('click', (e) => {
            if (e.target === modal) {
                closeModal();
            }
        });

        const applyFilters = () => {
            let filteredStores = storesData;

            if (activeFilters.type !== 'todos') {
                filteredStores = filteredStores.filter(store => store.type === activeFilters.type);
            }
            if (activeFilters.category !== 'todos') {
                filteredStores = filteredStores.filter(store => store.categories.includes(activeFilters.category));
            }
            if (activeFilters.commune !== 'todos') {
                filteredStores = filteredStores.filter(store => store.commune === activeFilters.commune);
            }
            if (activeFilters.search) {
                filteredStores = filteredStores.filter(store => store.name.toLowerCase().includes(activeFilters.search));
            }

            renderStoreCards(filteredStores);
            renderMapMarkers(filteredStores);
        };

        const handleFilterClick = (e) => {
            const button = e.target.closest('button');
            if (!button) return;

            const group = button.dataset.filterGroup;
            const value = button.dataset.filter;
            
            activeFilters[group] = value;
            
            document.querySelectorAll(`[data-filter-group="${group}"]`).forEach(btn => btn.classList.remove('active'));
            button.classList.add('active');
            
            applyFilters();
        };
        
        createFilterButtons(categoryFiltersContainer, getUniqueValues('categories'), 'category');
        createFilterButtons(communeFiltersContainer, getUniqueValues('commune'), 'commune');

        categoryFiltersContainer.addEventListener('click', handleFilterClick);
        communeFiltersContainer.addEventListener('click', handleFilterClick);
        typeFiltersContainer.addEventListener('click', handleFilterClick);
        
        searchInput.addEventListener('input', (e) => {
            activeFilters.search = e.target.value.toLowerCase();
            applyFilters();
        });
        
        applyFilters();
    });
    </script>
</body>
</html>
