<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bon de commande CLUBS/ASSOCIATIONS</title>
    <script src="https://cdn.tailwindcss.com"></script>
<script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: { sans: ['Inter', 'sans-serif'] }
        }
      }
    }
</script>
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.8.2/jspdf.plugin.autotable.min.js"></script>
    <style>
        body { font-family: 'Inter', sans-serif; }
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&display=swap');
        .modal { display: none; }
        .modal.is-open { display: flex; }
        input[type="text"], input[type="number"], input[type="date"], select, textarea, input[type="password"] {
            background-color: #f3f4f6;
            border-color: #9ca3af;
        }
        input[type="text"]:focus, input[type="number"]:focus, input[type="date"]:focus, select:focus, textarea:focus, input[type="password"]:focus {
            --tw-ring-color: #4f46e5;
            border-color: #4f46e5;
        }
        .toast {
            transition: opacity 0.5s, transform 0.5s;
            transform: translateX(100%);
            opacity: 0;
        }
        .toast.show {
            transform: translateX(0);
            opacity: 1;
        }
        .stock-info {
            font-size: 0.75rem;
            color: #4b5563;
        }
        .highlight-section {
            box-shadow: 0 0 0 3px rgba(79, 70, 229, 0.4), 0 4px 6px -1px rgba(0, 0, 0, 0.1);
            border-radius: 0.75rem;
            transition: box-shadow 0.3s ease-in-out;
        }
    </style>
</head>
<body class="bg-gray-50">

<div id="public-shop-view" class="hidden min-h-screen bg-gray-100 font-sans pb-20">

    <div id="public-cart-modal" class="fixed inset-0 bg-black/50 z-[60] hidden flex items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white rounded-xl shadow-2xl w-full max-w-md flex flex-col max-h-[80vh] animate-fade-in">
            <div class="p-4 border-b flex justify-between items-center bg-indigo-50 rounded-t-xl">
                <h3 class="font-bold text-lg text-indigo-900 flex items-center gap-2">
                    🛒 Votre Panier
                </h3>
                <button onclick="closePublicCart()" class="text-gray-400 hover:text-gray-600 bg-white rounded-full w-8 h-8 flex items-center justify-center font-bold shadow-sm">&times;</button>
            </div>

            <div id="public-cart-list" class="p-4 overflow-y-auto space-y-3 bg-gray-50 flex-1">
                </div>

            <div class="p-4 border-t bg-white rounded-b-xl space-y-3">
                <div class="flex justify-between items-center text-sm font-medium text-gray-600">
                    <span>Total articles :</span>
                    <span id="public-cart-total-qty" class="font-bold text-indigo-600 text-lg">0</span>
                </div>
                <button onclick="closePublicCart()" class="w-full py-3 bg-gray-200 hover:bg-gray-300 text-gray-800 font-bold rounded-lg transition">
                    Continuer mes achats
                </button>
                <button onclick="closePublicCart(); submitPublicOrder()" class="w-full py-3 bg-green-600 hover:bg-green-700 text-white font-bold rounded-lg shadow-md transition flex justify-center items-center gap-2">
                    ✅ Valider la commande
                </button>
            </div>
        </div>
    </div>
    <header class="bg-white shadow-sm sticky top-0 z-20">        <div class="max-w-3xl mx-auto px-4 py-4 flex justify-between items-center">
            <div>
                <h1 id="shop-club-name" class="text-xl font-extrabold text-indigo-900">Nom du Club</h1>
                <p class="text-xs text-gray-500">Boutique éphémère</p>
            </div>
            <div class="text-right">
                <button onclick="openPublicCart()" id="shop-cart-count" class="bg-indigo-600 hover:bg-indigo-700 text-white text-xs font-bold px-3 py-1.5 rounded-full shadow transition transform active:scale-95 cursor-pointer">
    🛒 0 article(s)
</button>
            </div>
        </div>
    </header>

    <div class="max-w-3xl mx-auto px-4 py-6 space-y-6">
        <div class="bg-blue-50 border-l-4 border-blue-500 p-4 rounded text-sm text-blue-800">
            <p class="font-bold">👋 Bonjour !</p>
            <p class="mt-1">Veuillez sélectionner vos articles ci-dessous pour la prochaine commande groupée.</p>
            <p id="shop-deadline-display" class="mt-2 text-xs font-bold text-red-600"></p>
        </div>

        <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-200">
            <h3 class="font-bold text-gray-800 mb-3">1. Vos Coordonnées</h3>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-3">
               <input type="text" id="shop-member-name" list="shop-licensee-list" placeholder="Commencez à taper votre nom..." class="w-full border-gray-300 rounded-md shadow-sm text-sm p-3 border">
<datalist id="shop-licensee-list"></datalist>
                <input type="email" id="shop-member-email" placeholder="Votre Email (Facultatif)" class="w-full border-gray-300 rounded-md shadow-sm text-sm p-3 border">
            </div>
        </div>

        <div>
            <h3 class="font-bold text-gray-800 mb-3 px-1">2. Sélectionnez vos articles</h3>
            <div id="shop-products-grid" class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                </div>
        </div>

        <div class="fixed bottom-0 left-0 right-0 p-4 bg-white border-t shadow-lg md:static md:bg-transparent md:border-0 md:shadow-none md:p-0">
            <button onclick="submitPublicOrder()" class="w-full bg-green-600 hover:bg-green-700 text-white font-bold py-4 rounded-xl shadow-lg text-lg flex justify-center items-center gap-2">
                <span>✅ Valider mes choix</span>
            </button>
        </div>
    </div>
</div>
<div id="login-overlay" class="fixed inset-0 z-[9999] bg-gradient-to-br from-slate-900 to-slate-800 flex flex-col items-center justify-center text-white">
    <div class="bg-white text-slate-900 p-8 rounded-2xl shadow-2xl max-w-md w-full text-center">
        <div class="mb-6">
            <span class="text-4xl">🔐</span>
        </div>
        <h1 class="text-2xl font-extrabold mb-2">Espace Club</h1>
        <p class="text-gray-500 mb-6 text-sm">Veuillez saisir votre code club pour accéder à votre bon de commande.</p>
        
        <input type="text" id="start-club-code" placeholder="EX: PORNIC44" 
               class="w-full text-center text-xl font-bold border-2 border-gray-300 rounded-lg p-3 mb-4 uppercase focus:border-blue-600 focus:ring-blue-600 text-slate-800"
               onkeypress="if(event.key === 'Enter') document.getElementById('btn-start-login').click()">
        
        <button id="btn-start-login" onclick="startAppLogin()" 
                class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 rounded-lg shadow-lg transition-transform transform active:scale-95">
            ENTRER
        </button>
        
        <div class="mt-6 border-t border-gray-200 pt-4">
            <p class="text-xs text-gray-400">Accès sécurisé & Synchronisé Cloud</p>
            <button onclick="bypassLogin()" class="text-[10px] text-gray-300 mt-2 hover:text-gray-500 underline">Accès Admin / Démo (Sans code)</button>
        </div>
    </div>
</div>
   <div id="main-app-view" class="hidden">
        <input type="file" id="load-order-input" class="hidden" accept=".json">
        <input type="file" id="import-licensees-input" class="hidden" accept=".xlsx, .xls">
        <input type="file" id="import-stock-input" class="hidden" accept=".json">
        <input type="file" id="import-club-range-input" class="hidden" accept=".json">
        <input type="file" id="load-all-data-input" class="hidden" accept=".json">
        <div id="toast-container" class="fixed top-5 right-5 z-[100] space-y-3 w-80"></div>
        <div id="main-modal" class="modal fixed inset-0 bg-black bg-opacity-50 z-50 justify-center items-center p-4">
            <div class="bg-white rounded-lg shadow-xl p-6 w-full max-w-md relative">
                <button id="main-modal-close-btn" class="absolute top-3 right-3 text-gray-400 hover:text-gray-600">
                    <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path></svg>
                </button>
                <h3 id="main-modal-title" class="text-xl font-bold text-gray-800 mb-4">Titre du Modal</h3>
                <div id="main-modal-body" class="text-gray-600 mb-6 max-h-[60vh] overflow-y-auto">Contenu du modal.</div>
                <div id="main-modal-actions" class="flex justify-end space-x-3"></div>
            </div>
        </div>
        <div class="container mx-auto p-4 sm:p-6 lg:p-8">
        <header class="mb-8 flex justify-between items-start">
    <div>
        <h1 id="main-title" class="text-4xl font-extrabold text-gray-800 tracking-tight">Bon de commande</h1>
        <p class="mt-2 text-lg text-gray-500">Créez et validez votre document.</p>
        <p id="autosave-status" class="mt-1 text-xs text-gray-400" style="min-height: 1em;"></p>
    </div>
    <div class="flex items-center gap-2 flex-wrap">
        <button onclick="location.reload()" class="px-4 py-2 bg-red-600 text-white text-sm font-bold rounded-md hover:bg-red-700 border border-red-500 shadow-lg flex items-center gap-2 transition-all active:scale-95">
            <span>🔒</span> Déconnexion / Changer Club
        </button>

        <button onclick="showAdminDashboard()" class="px-4 py-2 bg-slate-800 text-white text-sm font-bold rounded-md hover:bg-black border border-slate-600 shadow-lg flex items-center gap-2 transition-all active:scale-95">
    <span>🕵️</span> Vue d'ensemble Commercial
</button>


               <button id="global-snapshot-btn" class="px-4 py-2 bg-gray-800 text-white text-sm font-bold rounded-md hover:bg-black border border-gray-600 shadow-sm flex items-center gap-2">
            <span>💾</span> Sauvegarde Complète fichier .json
        </button>
        
        <button id="init-stock-btn" class="px-4 py-2 bg-orange-500 text-white text-sm font-medium rounded-md hover:bg-orange-600">Initialiser le Stock</button>
        <button id="manage-stock-btn" class="px-4 py-2 bg-green-600 text-white text-sm font-medium rounded-md hover:bg-green-700">Gérer le stock</button>
        <button id="poll-manager-btn" class="px-4 py-2 bg-purple-600 text-white text-sm font-bold rounded-md hover:bg-purple-700 border border-purple-500 flex items-center gap-2">
            <span>📊</span> Sondage / Intentions
        </button>
        <button id="editor-mode-btn" class="px-4 py-2 bg-gray-700 text-white text-sm font-bold rounded-md hover:bg-gray-900 border border-gray-500 flex items-center gap-2">
            <span>⚙️</span> Éditeur Catalogue
        </button>
    </div>
</header>
            <section id="dashboard-section" class="mb-8 bg-white p-4 rounded-xl shadow-lg">
                <h2 class="text-xl font-bold text-gray-800 mb-3">Tableau de bord</h2>
                <div class="grid grid-cols-2 md:grid-cols-5 gap-4 text-center">
                    <div>
                        <p class="text-sm text-gray-500">Total Hauts</p>
                        <p id="summary-total-hauts" class="text-2xl font-bold text-indigo-600">0</p>
                    </div>
                    <div>
                        <p class="text-sm text-gray-500">Total Bas</p>
                        <p id="summary-total-bas" class="text-2xl font-bold text-indigo-600">0</p>
                    </div>
                    <div>
                        <p class="text-sm text-gray-500">Total Accessoires</p>
                        <p id="summary-total-accessoires" class="text-2xl font-bold text-indigo-600">0</p>
                    </div>
                    <div>
                        <p class="text-sm text-gray-500">Nb. Licenciés</p>
                        <p id="summary-total-licensees" class="text-2xl font-bold text-indigo-600">0</p>
                    </div>
                    <div>
                        <p class="text-sm text-gray-500">Articles en Stock</p>
                        <p id="summary-total-stock" class="text-2xl font-bold text-green-600">0</p>
                    </div>
                </div>
           </section>

            <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 mt-6">
    <div class="mb-6 p-4 bg-white rounded-lg shadow-md border-l-4 border-orange-500 flex flex-wrap items-center justify-between gap-4">
    <div>
        <h3 class="font-bold text-lg text-gray-800">Importer une Gamme</h3>
        <p class="text-sm text-gray-600">Saisissez le code puis chargez le fichier .json</p>
    </div>
    <div class="flex items-center gap-2">
        <input type="password" id="import-security-code" placeholder="Code Admin" class="w-32 rounded-md border-gray-300 shadow-sm text-sm p-2 border focus:border-orange-500 focus:ring-orange-500">
        
        <input type="file" id="json-gamme-input" accept=".json" style="display: none;" />
        
        <button onclick="if(document.getElementById('import-security-code').value === '582069') { document.getElementById('json-gamme-input').click(); document.getElementById('import-security-code').value = ''; } else { alert('⛔ Code incorrect. Accès refusé.'); }" 
                class="bg-orange-600 hover:bg-orange-700 text-white font-bold py-2 px-4 rounded inline-flex items-center transition shadow-sm cursor-pointer">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-8l-4-4m0 0L8 8m4-4v12" />
            </svg>
            Charger (.json)
        </button>
    </div>
</div>
</div>
            <main class="bg-white p-6 rounded-xl shadow-lg mt-6 space-y-8">
                <section id="info-section">
    <h2 class="text-2xl font-bold text-gray-800 border-b pb-3 mb-6">Informations sur le document</h2>
<div class="col-span-full bg-emerald-50 p-4 rounded-xl border-2 border-dashed border-emerald-300 mb-8 hover:bg-emerald-100 transition-all group">
    <div class="flex items-center justify-between gap-4">
        <div class="flex items-center gap-4">
            <div class="bg-emerald-600 text-white p-3 rounded-lg shadow-lg group-hover:scale-110 transition-transform">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-8l-4-4m0 0L8 8m4-4v12" />
                </svg>
            </div>
            <div>
                <h3 class="font-black text-emerald-900 text-sm uppercase tracking-widest">Importation Rapide Cockpit</h3>
                <p class="text-xs text-emerald-700 mt-1">Chargez le fichier "INFOS IDENTITÉ CLUB" généré depuis votre cockpit.</p>
            </div>
        </div>
        
       <button type="button" onclick="secureImport()" class="bg-emerald-600 hover:bg-emerald-700 text-white px-6 py-3 rounded-xl font-black text-xs shadow-xl transition-all active:scale-95 whitespace-nowrap flex items-center gap-2">
    📂 CHOISIR LE FICHIER
</button>
        
        <input type="file" id="hidden-cockpit-input" accept=".json" class="hidden" onchange="handleCockpitImport(event)">
    </div>
</div>    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">

        <div>
            <label for="clubName" class="block text-sm font-medium text-gray-700">Nom du Club <span class="text-red-500">*</span></label>
            <input type="text" id="clubName" list="club-list" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
            <datalist id="club-list"></datalist>
        </div>
        <div>
            <label for="departement" class="block text-sm font-medium text-gray-700">Département <span class="text-red-500">*</span></label>
            <input type="text" id="departement" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
        </div>
        <div>
            <label for="clientNumber" class="block text-sm font-medium text-gray-700">N° Client</label>
            <input type="text" id="clientNumber" list="client-list" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
            <datalist id="client-list"></datalist>
        </div>
        <div>
            <label for="orderDate" class="block text-sm font-medium text-gray-700">Date</label>
            <input type="date" id="orderDate" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
        </div>

        <div id="order-scope-container">
            <label class="block text-sm font-medium text-gray-700">Type de saisie <span class="text-red-500">*</span></label>
            <div class="mt-2 flex flex-col sm:flex-row items-start sm:items-center space-y-2 sm:space-y-0 sm:space-x-4">
                <div class="flex items-center">
                    <input id="scope-global" name="scope" type="radio" value="global">
                    <label for="scope-global" class="ml-2 block text-sm text-gray-900">Globale</label>
                </div>
                <div class="flex items-center">
                    <input id="scope-licensee" name="scope" type="radio" value="licensee">
                    <label for="scope-licensee" class="ml-2 block text-sm text-gray-900">Par licencié</label>
                </div>
            </div>
        </div>
        <div class="flex items-center">
            <input id="doc-type-reassort" type="checkbox" class="h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500">
            <label for="doc-type-reassort" class="ml-2 block text-sm text-gray-900">Réassort 2 mois</label>
        </div>
                <div>
            <label class="block text-sm font-medium text-gray-700">Type de Création</label>
            <div class="mt-2 flex items-center">
                <input id="custom-creation-check" type="checkbox" class="h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500">
                <label for="custom-creation-check" class="ml-2 block text-sm text-gray-900">Commande Création Personnalisée</label>
            </div>
        </div>
        
        <div id="licencieName-container" class="hidden p-4 rounded-xl lg:col-span-1 md:col-span-2 col-span-1">
            <label for="licencieName" class="block text-sm font-medium text-gray-700">Nom du licencié</label>
            <div class="flex flex-col gap-2 mt-1">
                <div class="flex items-center gap-2">
                    <input type="text" id="licencieName" list="licensee-datalist" class="block w-full rounded-md border-gray-300 shadow-sm" placeholder="Taper ou sélectionner un nom...">
                    <datalist id="licensee-datalist"></datalist>
                    <button id="manage-licensees-btn" class="px-3 py-2 bg-gray-600 text-white rounded-md hover:bg-gray-700 text-sm">Gérer</button>
                </div>
                <button id="next-licensee-btn" class="w-full px-3 py-2 bg-green-600 text-white rounded-md hover:bg-green-700 text-sm">Valider & Suivant</button>
            </div>
        </div>
       <div class="col-span-1 md:col-span-2 lg:col-span-3 bg-indigo-50 p-3 rounded-lg border border-indigo-100">
            <label class="block text-sm font-bold text-indigo-900">Gestion de la remise appliquée par le club</label>
            
            <div class="mt-2 flex flex-wrap items-center gap-6">
                <div class="flex items-center">
                    <input id="apply-discount-check" type="checkbox" class="h-5 w-5 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500">
                    <label for="apply-discount-check" class="ml-2 block text-sm font-medium text-gray-900">Activer la remise</label>
                </div>

                <div id="discount-controls-container" class="hidden flex flex-wrap items-center gap-4 border-l-2 border-indigo-200 pl-4">
                    <div class="flex items-center space-x-4">
                        <div class="flex items-center">
                            <input id="discount-global" name="discount-type" type="radio" value="global" class="text-indigo-600" checked>
                            <label for="discount-global" class="ml-2 text-sm text-gray-700">Sur tout le panier</label>
                        </div>
                        <div class="flex items-center">
                            <input id="discount-item" name="discount-type" type="radio" value="item" class="text-indigo-600">
                            <label for="discount-item" class="ml-2 text-sm text-gray-700">Par article (selon Gamme)</label>
                        </div>
                    </div>
                    <div class="flex items-center gap-2">
                        <label for="clubDiscount" class="text-sm font-bold text-gray-700">Taux :</label>
                        <input type="number" id="clubDiscount" value="0" class="w-20 rounded-md border-gray-300 shadow-sm text-center font-bold text-indigo-700" placeholder="0">
                        <span class="text-sm text-gray-600">%</span>
                    </div>
                </div>
            </div>
        </div>
 <div id="participation-container" class="hidden">
            <label class="block text-sm font-medium text-gray-700">Participation du club (par licencié)</label>
            <div class="mt-2 flex items-center space-x-4">
                <div class="flex items-center">
                    <input id="apply-participation-check" type="checkbox" class="h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500">
                    <label for="apply-participation-check" class="ml-2 block text-sm text-gray-900">Activer la participation</label>
                </div>
<div>
            <label for="packBaseQuantity" class="block text-sm font-medium text-gray-700">Quantité de Réf. pour Packs</label>
            <input type="number" id="packBaseQuantity" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm" placeholder="Ex: 100">
            <p class="text-xs text-gray-500 mt-1">Base pour le calcul des prix des packs et des articles individuels correspondants.</p>
        </div>
                <div id="participation-amount-container" class="hidden flex items-center space-x-2">
                     <input type="number" id="clubParticipationAmount" value="0" class="block w-24 rounded-md border-gray-300 shadow-sm" placeholder="0.00">
                     <span class="text-sm text-gray-600">€</span>
                </div>
            </div>
        </div>
     <div class="grid grid-cols-1 md:grid-cols-4 gap-2">
    <button id="manage-club-range-btn" class="w-full mt-1 px-4 py-2 bg-slate-600 text-white text-sm font-medium rounded-md hover:bg-slate-700"> Gérer la Gamme </button>
    <button id="generate-blank-order-btn" class="w-full mt-1 px-4 py-2 bg-teal-600 text-white text-sm font-medium rounded-md hover:bg-teal-700"> Générer Bon Vierge (PDF) </button>
    <button id="manage-visuals-btn" class="w-full mt-1 px-4 py-2 bg-cyan-600 text-white text-sm font-medium rounded-md hover:bg-cyan-700"> Gérer les Visuels </button>
    <button id="manage-packs-btn" class="w-full mt-1 px-4 py-2 bg-purple-600 text-white text-sm font-medium rounded-md hover:bg-purple-700"> Gérer les Packs </button>
</div>

       <div class="md:col-span-2 lg:col-span-3 border-t pt-4 mt-4 grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
            <div>
                <label for="preOrderNumber" class="block text-sm font-medium text-gray-700">N° Précommande</label>
                <input type="text" id="preOrderNumber" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
            </div>
            <div>
                <label for="factoryDepartureDate" class="block text-sm font-medium text-gray-700">Départ Usine Prévu</label>
                <input type="date" id="factoryDepartureDate" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
            </div>
            
            <div>
                <label for="deliveryContact" class="block text-sm font-medium text-gray-700">N° Portable (livraison)</label>
                <input type="text" id="deliveryContact" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
            </div>

            <div>
                <label for="deliveryEmail" class="block text-sm font-medium text-gray-700">Adresse mail (livraison)</label>
                <input type="email" id="deliveryEmail" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
            </div>

            <div class="md:col-span-2 lg:col-span-4">
                <label for="deliveryAddress" class="block text-sm font-medium text-gray-700">Adresse de Livraison</label>
                <textarea id="deliveryAddress" rows="4" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm"></textarea>
            </div>
        </div>

        <div class="md:col-span-2 lg:col-span-3">
            <label for="orderSpecificity" class="block text-sm font-medium text-gray-700">Spécificité Commande</label>
            <textarea id="orderSpecificity" rows="1" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm" placeholder="Notes générales sur la commande..."></textarea>
        </div>
         </div>
</section>
                             <section id="add-article-section">
                    <div class="flex justify-between items-center border-b pb-3 mb-6">
                        <h2 class="text-2xl font-bold text-gray-800">Ajouter un Article</h2>
                        <div id="toggle-products-view-container" class="hidden">
                            <button id="toggle-products-btn" class="px-3 py-1 bg-gray-200 text-gray-700 text-xs font-medium rounded-md hover:bg-gray-300"> Afficher tous les articles </button>
                        </div>
                    </div>
                                    <div id="article-section-blocker" class="hidden text-center p-8 border-2 border-dashed rounded-lg mt-6 bg-yellow-100 border-yellow-400">
                        <svg class="mx-auto h-12 w-12 text-yellow-500" fill="none" viewBox="0 0 24 24" stroke="currentColor" aria-hidden="true"><path vector-effect="non-scaling-stroke" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 010.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" /></svg>
                        <h3 class="mt-2 text-sm font-medium text-yellow-900">Commencez par les informations du document</h3>
                        <p class="mt-1 text-sm text-yellow-800">Veuillez renseigner le <strong>Nom du Club</strong>, le <strong>Département</strong> et le <strong>Type de saisie</strong> pour débloquer la suite.</p>
                    </div>
                    <div id="product-form-container" class="pt-6"></div>
                </section>
                <section>
                    <div class="flex justify-between items-center border-b pb-3 mb-6">
                        <h2 class="text-2xl font-bold text-gray-800">Détails de la commande</h2>
                        <span id="total-articles-display" class="text-lg font-semibold text-gray-600">Total des articles : 0</span>
                    </div>
                    <div id="quantity-dashboard-container" class="mb-6 bg-gray-50 p-4 rounded-lg shadow-inner"></div>
                 <div class="overflow-x-auto rounded-lg shadow">
    <table class="min-w-full divide-y divide-gray-200">
        <thead id="order-table-head" class="bg-gray-50"></thead>
        <tbody id="order-table-body" class="bg-white divide-y divide-gray-200">
            <tr><td colSpan="8" class="px-6 py-12 text-center text-gray-500">Aucun article dans la commande.</td></tr>
        </tbody>
    </table>
</div>
                </section>
                <section id="summary-and-actions-section">
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-x-8 gap-y-6">
                                                <div class="space-y-2 text-right md:col-start-2">
                            <div class="text-md"><span class="font-medium text-gray-600">Sous-total HT: </span><span id="subtotal-ht" class="font-semibold text-gray-800">0.00€</span></div>
                            <div class="text-md"><span class="font-medium text-gray-600">Sous-total TTC: </span><span id="subtotal-ttc" class="font-semibold text-gray-800">0.00€</span></div>
                            <div class="text-md text-red-600"><span class="font-medium">Remise Club HT (Information): </span><span id="discount-amount-ht" class="font-semibold">-0.00€</span></div>
                            <div class="text-md text-red-600"><span class="font-medium">Remise Club TTC (Information): </span><span id="discount-amount-ttc" class="font-semibold">-0.00€</span></div>
                            <div class="text-md border-t pt-2 mt-2"><span class="font-medium text-gray-600">Frais de port HT: </span><span id="shipping-ht" class="font-semibold text-gray-800">0.00€</span></div>
                            <div class="text-md"><span class="font-medium text-gray-600">Frais de port TTC: </span><span id="shipping-ttc" class="font-semibold text-gray-800">0.00€</span></div>
                            <div id="graphic-fee-container" class="hidden">
                                <div class="text-md"><span class="font-medium text-gray-600">Forfait Création Graphique HT: </span><span id="graphic-fee-ht" class="font-semibold text-gray-800">0.00€</span></div>
                                <div class="text-md"><span class="font-medium text-gray-600">Forfait Création Graphique TTC: </span><span id="graphic-fee-ttc" class="font-semibold text-gray-800">0.00€</span></div>
                            </div>
                            <div class="text-xl mt-4"><span class="font-bold text-gray-700">Total Général HT: </span><span id="grand-total-ht" class="font-extrabold text-indigo-600">0.00€</span></div>
                            <div class="text-2xl"><span class="font-bold text-gray-700">Total Général TTC: </span><span id="grand-total-ttc" class="font-extrabold text-indigo-600">0.00€</span></div>
                            <div id="down-payment-container" class="text-xl mt-2 font-bold text-green-600"><span class="font-bold text-gray-700">Acompte à verser (30%): </span><span id="down-payment">0.00€</span></div>
                        </div>
                    </div>
                    <div class="mt-8 pt-6 border-t flex flex-col sm:flex-row justify-between items-center gap-4">
                        
                        <div class="flex flex-col sm:flex-row gap-4 w-full sm:w-auto">
    <button id="regenerate-docs-btn" class="w-full sm:w-auto inline-flex justify-center items-center px-6 py-3 border border-gray-300 text-base font-medium rounded-md shadow-sm text-gray-700 bg-white hover:bg-gray-50">
        🖨️ Ré-éditer PDF
    </button>
<button id="export-labels-btn" class="w-full sm:w-auto inline-flex justify-center items-center px-6 py-3 border border-transparent text-base font-medium rounded-md shadow-sm text-white bg-pink-600 hover:bg-pink-700">
    🏷️ Étiquettes Sacs
</button>
        <label id="import-order-label" for="load-order-input" class="w-full sm:w-auto inline-flex justify-center items-center px-6 py-3 border border-transparent text-base font-medium rounded-md shadow-sm text-white bg-gray-600 hover:bg-gray-700 cursor-pointer">Importer Fichier</label>
    <button id="validate-order-btn" class="w-full sm:w-auto inline-flex justify-center items-center px-8 py-3 border border-transparent text-base font-bold rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 disabled:bg-indigo-300">Valider la Commande</button>
</div>
                    </div>
                </section>
            </main>
        </div>
    </div>
<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getDatabase, ref, set, get, update, onValue } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCIBQFLZ9rck2q1gG04BJW-t29Jt_PzFFM",
  authDomain: "gestion-commande-club.firebaseapp.com",
  // VÉRIFIEZ BIEN CETTE LIGNE CI-DESSOUS :
  databaseURL: "https://gestion-commande-club-default-rtdb.europe-west1.firebasedatabase.app", 
  projectId: "gestion-commande-club",
  storageBucket: "gestion-commande-club.firebasestorage.app",
  messagingSenderId: "501736145423",
  appId: "1:501736145423:web:ea4d51e3e36f956899597a",
  measurementId: "G-NCQG9YWR3B"
};
// Initialisation Firebase
const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

// Identifiant du club connecté
let currentClubCode = null;
// --- ÉTAPE 2 : FONCTIONS DE SYNCHRONISATION CORRIGÉES ---
window.startAppLogin = async () => {
    const codeInput = document.getElementById('start-club-code');
    let rawCode = codeInput.value.trim().toUpperCase();

    if (!rawCode) {
        alert("Veuillez saisir un code club.");
        return;
    }

    // 1. Nettoyage du code (caractères interdits)
    const code = rawCode.replace(/[.#$[\]]/g, '-');
    if (code !== rawCode) codeInput.value = code;

    currentClubCode = code;
    const clubRef = ref(db, 'clubs/' + currentClubCode);
    
    try {
        const snapshot = await get(clubRef);
        
        // --- CAS 1 : LE DOSSIER EXISTE DÉJÀ (Connexion classique) ---
        if (snapshot.exists()) {
            const cloudData = snapshot.val();
            
            // Vérification si banni
            if (cloudData.isAccessBlocked === true) {
                alert("⛔ ACCÈS REFUSÉ\nCe dossier a été clôturé.");
                currentClubCode = null;
                return;
            }

            // --- NOUVELLE LOGIQUE HYBRIDE ---
            
            // 1. On charge les données générales (Gamme, Licenciés...)
            // On vérifie si elles sont à la racine ou dans un sous-dossier 'general'
            const dataToLoad = cloudData.general ? cloudData.general : cloudData;
            Object.assign(state, dataToLoad);
            
            // 2. MAIS ON VIDE LA SESSION DE COMMANDE (Pour forcer le N° de Précommande ensuite)
            state.currentOrderLineItems = [];
            state.orderScope = ''; 
            state.preOrderNumber = ''; 
            state.orderSpecificity = '';
            
            // On vide les champs visuels
            if (document.getElementById('preOrderNumber')) document.getElementById('preOrderNumber').value = '';
            
            // On décoche les boutons radios pour forcer le choix
            if (document.getElementById('scope-global')) document.getElementById('scope-global').checked = false;
            if (document.getElementById('scope-licensee')) document.getElementById('scope-licensee').checked = false;

            showToast(`✅ Connecté : Club ${state.clubName}`);
            
            // On débloque l'interface
            unlockApp();
            renderAll(); 
        } 
        
        // --- CAS 2 : LE DOSSIER N'EXISTE PAS (Création) ---
        else {
            // A. DÉTECTION DU "PÈRE" (Pour cloner une fiche existante)
            let masterCode = "";
            let masterData = null;

            const allClubsSnap = await get(ref(db, 'clubs'));
            const codes = Object.keys(allClubsSnap.val() || {});
            
            // On cherche la plus longue correspondance existante
            const bestMatch = codes
                .filter(existingCode => code.startsWith(existingCode) && existingCode !== code)
                .sort((a, b) => b.length - a.length)[0];

            if (bestMatch) {
                const masterSnap = await get(ref(db, 'clubs/' + bestMatch));
                if (masterSnap.exists()) {
                    // On vérifie si c'est la structure 'general' ou racine
                    const val = masterSnap.val();
                    masterData = val.general ? val.general : val;
                    masterCode = bestMatch;
                }
            }

            // B. SCÉNARIO : FICHE IDENTITÉ TROUVÉE -> CLONAGE
            if (masterData) {
                const confirmClone = confirm(`✨ Fiche identité trouvée : "${masterData.clubName}" (${masterCode}).\n\nVoulez-vous initialiser la commande "${code}" avec ces données (Adresses, Licenciés, Gamme) ?`);
                
                if (confirmClone) {
                    const creationPass = prompt("🔐 Création de commande : Mot de passe Admin requis :");
                    if (creationPass !== '582069') { 
                        alert("⛔ Refusé."); currentClubCode = null; return; 
                    }

                    // 1. On copie les données STRUCTURELLES
                    state.clubName = masterData.clubName;
                    state.departement = masterData.departement;
                    state.clientNumber = masterData.clientNumber;
                    
                    state.deliveryAddress = masterData.deliveryAddress || '';
                    state.deliveryContact = masterData.deliveryContact || '';
                    state.deliveryEmail = masterData.deliveryEmail || '';
                    
                    state.licenseeList = masterData.licenseeList || [];
                    state.licenseeEmails = masterData.licenseeEmails || {};
                    
                    state.clubProductRange = masterData.clubProductRange || [];
                    state.clubVisuals = masterData.clubVisuals || [];
                    state.clubPacks = masterData.clubPacks || [];
                    state.clubStock = masterData.clubStock || {}; 
                    
                    // 2. On initialise la NOUVELLE COMMANDE
                    state.preOrderNumber = code; 
                    state.orderDate = new Date().toISOString().split('T')[0];
                    state.currentOrderLineItems = [];
                    state.validatedLicensees = [];

                    // 3. Sauvegarde immédiate
                    await window.syncToCloud(true);
                    showToast(`🚀 Commande ${code} créée d'après ${masterCode} !`);
                    
                    unlockApp();
                    renderAll();
                } else {
                    currentClubCode = null; return;
                }
            }
            // C. SCÉNARIO : AUCUNE FICHE TROUVÉE (Création Vierge)
            else {
                const create = confirm(`Code "${code}" inconnu.\nCréer un espace vierge ?`);
                if (!create) { currentClubCode = null; return; }

                const creationPass = prompt("🔐 Création vierge : Mot de passe Admin requis :");
                if (creationPass !== '582069') { alert("⛔ Refusé."); currentClubCode = null; return; }

                state.preOrderNumber = code; 
                await window.syncToCloud(true);
                showToast(`🚀 Espace vierge créé : ${code}`);
                
                unlockApp();
                renderAll();
            }
        }

    } catch (error) {
        console.error(error);
        alert("Erreur technique : " + error.message);
    }
};
window.syncToCloud = async (silent = true) => {
    if (state.isReadOnly) return; 
    if (!currentClubCode) return;

    try {
        // 1. Sauvegarde des Données Générales du Club (Racine)
        // (Gamme, Licenciés, Stock, Packs, Paramètres)
        const generalData = {
            clubName: state.clubName,
            clientNumber: state.clientNumber,
            departement: state.departement,
            deliveryAddress: state.deliveryAddress,
            deliveryContact: state.deliveryContact,
            deliveryEmail: state.deliveryEmail,
            
            licenseeList: state.licenseeList,
            licenseeEmails: state.licenseeEmails,
            licenseeDeposits: state.licenseeDeposits, // Les acomptes restent liés au club
            
            clubProductRange: state.clubProductRange,
            clubStock: state.clubStock,
            clubPacks: state.clubPacks,
            clubVisuals: state.clubVisuals,
            savedPolls: state.savedPolls,
            
            lastUpdate: new Date().toISOString()
        };
        
        // On crée un objet de mise à jour groupée
        const updates = {};
        updates[`clubs/${currentClubCode}/general`] = generalData;

        // 2. Sauvegarde de la Commande Spécifique (Si un numéro est actif)
        if (state.preOrderNumber && state.preOrderNumber.trim() !== '') {
            const orderId = state.preOrderNumber.trim().toUpperCase();
            const orderData = {
                items: state.currentOrderLineItems,
                specificity: state.orderSpecificity,
                date: state.orderDate,
                scope: state.orderScope,
                lastSave: new Date().toISOString()
            };
            updates[`clubs/${currentClubCode}/orders/${orderId}`] = orderData;
        }

        // Envoi atomique (tout ou rien)
        await update(ref(db), updates);
        
        if (!silent) {
            showToast("☁️ Sauvegardé (Club + Commande)");
        }
        
        // Mise à jour visuelle du statut
        dom.autosaveStatus.textContent = `☁️ Cloud à jour : ${new Date().toLocaleTimeString()}`;
        dom.autosaveStatus.className = 'mt-1 text-xs text-gray-400';

    } catch (error) {
        console.error("Erreur Firebase:", error);
        if(!silent) showToast("Erreur de sauvegarde", "error");
        dom.autosaveStatus.textContent = '⚠️ Erreur Synchro';
        dom.autosaveStatus.className = 'mt-1 text-xs text-red-600';
    }
};
const data = {
            cyclisme: [{
                subCategory: "Maillots Manches Courtes",
                items: [{
                    id: "HCH100FRHU",
                    name: "Maillot MC CONFORT CLASSIQUE (Homme/Femme) CYCLO/VTT",
                    ref: "HCH100FRHU / HCF100FRHU",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    panachage: "mc_classique_confort",
                    prices: [{
                        q: [2, 2],
                        ht: 61.50,
                        ttc: 73.80,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 51.25,
                        ttc: 61.50,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 41.00,
                        ttc: 49.20,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 38.95,
                        ttc: 46.74,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 37.72,
                        ttc: 45.26,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 36.90,
                        ttc: 44.28,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 35.67,
                        ttc: 42.80,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 34.85,
                        ttc: 41.82,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HCM050VESP",
                    name: "Maillot sans manches CONFORT (Mixte)",
                    ref: "HCM050VESP",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    prices: [{
                        q: [2, 2],
                        ht: 61.50,
                        ttc: 73.80,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 51.25,
                        ttc: 61.50,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 41.00,
                        ttc: 49.20,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 38.95,
                        ttc: 46.74,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 37.72,
                        ttc: 45.26,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 36.90,
                        ttc: 44.28,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 35.67,
                        ttc: 42.80,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 34.85,
                        ttc: 41.82,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HCM150VESP",
                    name: "Maillot MC CONFORT PERFORMANCE (Mixte)",
                    ref: "HCM150VESP",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    prices: [{
                        q: [2, 2],
                        ht: 63.75,
                        ttc: 76.50,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 53.13,
                        ttc: 63.76,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 42.50,
                        ttc: 51.00,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 40.38,
                        ttc: 48.46,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 39.10,
                        ttc: 46.92,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 38.25,
                        ttc: 45.90,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 36.98,
                        ttc: 44.38,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 36.13,
                        ttc: 43.36,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HBM150MOMO",
                    name: "Maillot Descente MC CONFORT (Mixte)",
                    ref: "HBM150MOMO",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    prices: [{
                        q: [2, 2],
                        ht: 55.50,
                        ttc: 66.60,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 46.25,
                        ttc: 55.50,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 37.00,
                        ttc: 44.40,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 35.15,
                        ttc: 42.18,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 34.04,
                        ttc: 40.85,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 33.30,
                        ttc: 39.96,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 32.19,
                        ttc: 38.63,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 31.45,
                        ttc: 37.74,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HCM180MBST",
                    name: "Maillot MC AERO (Mixte)",
                    ref: "HCM180MBST",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    prices: [{
                        q: [2, 2],
                        ht: 69.00,
                        ttc: 82.80,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 57.50,
                        ttc: 69.00,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 46.00,
                        ttc: 55.20,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 43.70,
                        ttc: 52.44,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 37.50,
                        ttc: 45.00,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 36.75,
                        ttc: 44.10,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 35.63,
                        ttc: 42.76,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 34.90,
                        ttc: 41.88,
                        displayQ: "150 et +"
                    }]
                }]
            }, {
                subCategory: "Maillots Manches Longues",
                items: [{
                    id: "HCH250YAYA",
                    name: "Maillot ML mi-saison CONFORT (Homme/Femme)",
                    ref: "HCH250YAYA / HCF250YAYA",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    panachage: "ml_confort",
                    prices: [{
                        q: [2, 2],
                        ht: 67.50,
                        ttc: 81.00,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 56.25,
                        ttc: 67.50,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 45.00,
                        ttc: 54.00,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 42.75,
                        ttc: 51.30,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 41.40,
                        ttc: 49.68,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 40.50,
                        ttc: 48.60,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 39.15,
                        ttc: 46.98,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 38.25,
                        ttc: 45.90,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HBM250MOMO",
                    name: "Maillot BMX ML (Mixte)",
                    ref: "HBM250MOMO",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    prices: [{
                        q: [2, 2],
                        ht: 63.00,
                        ttc: 75.60,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 52.50,
                        ttc: 63.00,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 42.00,
                        ttc: 50.40,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 39.90,
                        ttc: 47.88,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 38.64,
                        ttc: 46.37,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 37.80,
                        ttc: 45.36,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 36.54,
                        ttc: 43.85,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 35.70,
                        ttc: 42.84,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HCM280CLCL",
                    name: "Maillot ML mi-saison AERO (Mixte)",
                    ref: "HCM280CLCL",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    prices: [{
                        q: [2, 2],
                        ht: 76.50,
                        ttc: 91.80,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 63.75,
                        ttc: 76.50,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 51.00,
                        ttc: 61.20,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 48.45,
                        ttc: 58.14,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 46.92,
                        ttc: 56.30,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 45.90,
                        ttc: 55.08,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 44.37,
                        ttc: 53.24,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 43.35,
                        ttc: 52.02,
                        displayQ: "150 et +"
                    }]
                }]
            }, {
                subCategory: "Essentiels et Vestes",
                items: [{
                    id: "HCM180TPBP",
                    name: "Maillot MC AERO PLUIE (Mixte)",
                    ref: "HCM180TPBP",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 71.00,
                        ttc: 85.20,
                        displayQ: "Tarif fixe"
                    }]
                }, {
                    id: "HCM280TPBP_2",
                    name: "Maillot ML AERO PLUIE (Mixte)",
                    ref: "HCM280TPBP",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 85.00,
                        ttc: 102.00,
                        displayQ: "Tarif fixe"
                    }]
                }, {
                    id: "HCM050FIFI",
                    name: "Gilet coupe-vent LEGER (Mixte)",
                    ref: "HCM050FIFI",
                    options: [{
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    panachage: "gilets",
                    prices: [{
                        q: [2, 2],
                        ht: 51.75,
                        ttc: 62.10,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 43.13,
                        ttc: 51.76,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 34.50,
                        ttc: 41.40,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 32.78,
                        ttc: 39.34,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 31.74,
                        ttc: 38.09,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 31.05,
                        ttc: 37.26,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 30.02,
                        ttc: 36.02,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 29.33,
                        ttc: 35.20,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HCM050MTBS",
                    name: "Gilet coupe-vent MI-SAISON (Mixte)",
                    ref: "HCM050MTBS",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    panachage: "gilets",
                    prices: [{
                        q: [2, 2],
                        ht: 65.25,
                        ttc: 78.30,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 54.38,
                        ttc: 65.26,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 43.50,
                        ttc: 52.20,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 41.33,
                        ttc: 49.60,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 40.02,
                        ttc: 48.02,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 39.15,
                        ttc: 46.98,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 37.85,
                        ttc: 45.42,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 36.98,
                        ttc: 44.38,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HCM050MTMT",
                    name: "Gilet coupe-vent HIVER (Mixte)",
                    ref: "HCM050MTMT",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    panachage: "gilets",
                    prices: [{
                        q: [2, 2],
                        ht: 69.75,
                        ttc: 83.70,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 58.13,
                        ttc: 69.76,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 46.50,
                        ttc: 55.80,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 44.18,
                        ttc: 53.02,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 42.78,
                        ttc: 51.34,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 41.85,
                        ttc: 50.22,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 40.46,
                        ttc: 48.55,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 39.53,
                        ttc: 47.44,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HCM250FIFI",
                    name: "Coupe-vent léger ML (Mixte)",
                    ref: "HCM250FIFI",
                    options: [{
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    panachage: "coupe_vent",
                    prices: [{
                        q: [2, 2],
                        ht: 69.00,
                        ttc: 82.80,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 57.50,
                        ttc: 69.00,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 46.00,
                        ttc: 55.20,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 43.70,
                        ttc: 52.44,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 42.32,
                        ttc: 50.78,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 41.40,
                        ttc: 49.68,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 40.02,
                        ttc: 48.02,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 39.10,
                        ttc: 46.92,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HCM250IMIM",
                    name: "Coupe-vent léger déperlant ML (Mixte)",
                    ref: "HCM250IMIM",
                    options: [{
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    panachage: "coupe_vent",
                    prices: [{
                        q: [2, 2],
                        ht: 91.50,
                        ttc: 109.80,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 76.25,
                        ttc: 91.50,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 61.00,
                        ttc: 73.20,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 57.95,
                        ttc: 69.54,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 56.12,
                        ttc: 67.34,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 54.90,
                        ttc: 65.88,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 53.07,
                        ttc: 63.68,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 51.85,
                        ttc: 62.22,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HCH200IMYA",
                    name: "Veste mi-saison CONFORT (Homme/Femme)",
                    ref: "HCH200IMYA / HCF200IMYA",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    panachage: "veste_misaison_confort",
                    prices: [{
                        q: [2, 2],
                        ht: 97.50,
                        ttc: 117.00,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 81.25,
                        ttc: 97.50,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 65.00,
                        ttc: 78.00,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 61.75,
                        ttc: 74.10,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 59.80,
                        ttc: 71.76,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 58.50,
                        ttc: 70.20,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 56.55,
                        ttc: 67.86,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 55.25,
                        ttc: 66.30,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HCM280EPKI",
                    name: "Veste hiver AERO (Mixte)",
                    ref: "HCM280EPKI",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    prices: [{
                        q: [2, 2],
                        ht: 105.00,
                        ttc: 126.00,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 87.50,
                        ttc: 105.00,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 70.00,
                        ttc: 84.00,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 66.50,
                        ttc: 79.80,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 64.40,
                        ttc: 77.28,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 63.00,
                        ttc: 75.60,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 60.90,
                        ttc: 73.08,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 59.50,
                        ttc: 71.40,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HCH250IDYA",
                    name: "Veste hiver CONFORT (Homme/Femme)",
                    ref: "HCH250IDYA / HCF250IDYA",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    panachage: "vestes_hiver_confort",
                    prices: [{
                        q: [2, 2],
                        ht: 111.00,
                        ttc: 133.20,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 92.50,
                        ttc: 111.00,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 74.00,
                        ttc: 88.80,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 70.30,
                        ttc: 84.36,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 68.08,
                        ttc: 81.70,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 66.60,
                        ttc: 79.92,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 64.38,
                        ttc: 77.26,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 62.90,
                        ttc: 75.48,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HCH250IDID",
                    name: "Veste hiver Thermique CONFORT (Homme/Femme)",
                    ref: "HCH250IDID / HCF250IDID",
                    options: [{
                        name: "Poche zippée dos",
                        ht: 5.00,
                        ttc: 6.00
                    }, {
                        name: "Bande réflective (par bande)",
                        ht: 3.00,
                        ttc: 3.60
                    }],
                    panachage: "vestes_hiver_confort",
                    prices: [{
                        q: [2, 2],
                        ht: 114.00,
                        ttc: 136.80,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 95.00,
                        ttc: 114.00,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 76.00,
                        ttc: 91.20,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 72.20,
                        ttc: 86.64,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 69.92,
                        ttc: 83.90,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 68.40,
                        ttc: 82.08,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 66.12,
                        ttc: 79.34,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 64.60,
                        ttc: 77.52,
                        displayQ: "150 et +"
                    }]
                }]
            }, {
                subCategory: "Cuissards et Collants",
                links: [{
                    name: "ENDURANCE 2.5",
                    url: "https://acrobat.adobe.com/id/urn:aaid:sc:EU:e7d8c649-9a89-4b45-8695-a99b33dc1dfc"
                }, {
                    name: "LANDSCAPE",
                    url: "https://acrobat.adobe.com/id/urn:aaid:sc:EU:8e78c611-4fd9-4e19-b61a-6cfd3684e755"
                }, {
                    name: "CERVINO",
                    url: "https://acrobat.adobe.com/id/urn:aaid:sc:EU:2c798018-d0ea-4fc3-89cc-e8f5a0c45820"
                }],
                items: [{
                    id: "BCM150TDSP",
                    name: "Cuissard à bretelles CONFORT (Mixte) - fond LANDSCAPE",
                    ref: "BCM150TDSP/NMSP",
                    options: [],
                    panachage: "cuissard_confort",
                    prices: [{ q: [2, 2], ht: 86.50, ttc: 103.80, displayQ: "2 pièces" }, { q: [3, 4], ht: 72.25, ttc: 86.70, displayQ: "3-4 pièces" }, { q: [5, 14], ht: 58.00, ttc: 69.60, displayQ: "5-14 pièces" }, { q: [15, 24], ht: 55.15, ttc: 66.18, displayQ: "15-24 pièces" }, { q: [25, 49], ht: 53.44, ttc: 64.13, displayQ: "25-49 pièces" }, { q: [50, 79], ht: 52.30, ttc: 62.76, displayQ: "50-79 pièces" }, { q: [80, 149], ht: 50.59, ttc: 60.71, displayQ: "80-149 pièces" }, { q: [150, Infinity], ht: 49.45, ttc: 59.34, displayQ: "150 et +" }]
                }, {
                    id: "BCF101TDTD",
                    name: "Cuissard Femme CONFORT sans bretelles - fond LANDSCAPE",
                    ref: "BCF101TDTD/TDNM",
                    options: [],
                    panachage: "cuissard_confort",
                    prices: [{ q: [2, 2], ht: 83.50, ttc: 100.20, displayQ: "2 pièces" }, { q: [3, 4], ht: 69.75, ttc: 83.70, displayQ: "3-4 pièces" }, { q: [5, 14], ht: 56.00, ttc: 67.20, displayQ: "5-14 pièces" }, { q: [15, 24], ht: 53.25, ttc: 63.90, displayQ: "15-24 pièces" }, { q: [25, 49], ht: 51.60, ttc: 61.92, displayQ: "25-49 pièces" }, { q: [50, 79], ht: 50.50, ttc: 60.60, displayQ: "50-79 pièces" }, { q: [80, 149], ht: 48.85, ttc: 58.62, displayQ: "80-149 pièces" }, { q: [150, Infinity], ht: 47.75, ttc: 57.30, displayQ: "150 et +" }]
                }, {
                    id: "BCM180BTBT",
                    name: "Cuissard à bretelles AERO (Mixte) - fond CERVINO",
                    ref: "BCM180BTBT/BTNM",
                    options: [],
                    prices: [{ q: [2, 2], ht: 101.50, ttc: 121.80, displayQ: "2 pièces" }, { q: [3, 4], ht: 84.75, ttc: 101.70, displayQ: "3-4 pièces" }, { q: [5, 14], ht: 68.00, ttc: 81.60, displayQ: "5-14 pièces" }, { q: [15, 24], ht: 64.65, ttc: 77.58, displayQ: "15-24 pièces" }, { q: [25, 49], ht: 62.64, ttc: 75.17, displayQ: "25-49 pièces" }, { q: [50, 79], ht: 61.30, ttc: 73.56, displayQ: "50-79 pièces" }, { q: [80, 149], ht: 59.29, ttc: 71.15, displayQ: "80-149 pièces" }, { q: [150, Infinity], ht: 57.95, ttc: 69.54, displayQ: "150 et +" }]
                }, {
                    id: "BCM180BPBP",
                    name: "Cuissard à bretelles AERO mi-saison - fond CERVINO",
                    ref: "BCM180BPBP",
                    options: [],
                    prices: [{ q: [2, 2], ht: 106.00, ttc: 127.20, displayQ: "2 pièces" }, { q: [3, 4], ht: 88.50, ttc: 106.20, displayQ: "3-4 pièces" }, { q: [5, 14], ht: 71.00, ttc: 85.20, displayQ: "5-14 pièces" }, { q: [15, 24], ht: 67.50, ttc: 81.00, displayQ: "15-24 pièces" }, { q: [25, 49], ht: 65.40, ttc: 78.48, displayQ: "25-49 pièces" }, { q: [50, 79], ht: 64.00, ttc: 76.80, displayQ: "50-79 pièces" }, { q: [80, 149], ht: 61.90, ttc: 74.28, displayQ: "80-149 pièces" }, { q: [150, Infinity], ht: 60.50, ttc: 72.60, displayQ: "150 et +" }]
                }, {
                    id: "BM117MZATT",
                    name: "Cuissard bretelles GRAVEL",
                    ref: "BM117MZATT",
                    options: [],
                    description: "Vendu uniquement avec le fond Endurance 2.5 Homme.",
                    prices: [{
                        q: [2, 2],
                        ht: 87.00,
                        ttc: 104.40,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 72.50,
                        ttc: 87.00,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 58.00,
                        ttc: 69.60,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 55.10,
                        ttc: 66.12,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 53.36,
                        ttc: 64.03,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 52.20,
                        ttc: 62.64,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 50.46,
                        ttc: 60.55,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 49.30,
                        ttc: 59.16,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "BM4000MKO",
                    name: "Short VTT",
                    ref: "BM4000MKO",
                    options: [],
                    description: "Vendu uniquement avec le fond Endurance 2.5 Homme.",
                    prices: [{
                        q: [1, Infinity],
                        ht: 62.50,
                        ttc: 75.00,
                        displayQ: "Tarif fixe"
                    }]
                }, {
                    id: "BCH350TDTD",
                    name: "Corsaire à bretelles été CONFORT - fond LANDSCAPE",
                    ref: "BCH350TDTD/TDNM",
                    options: [],
                    panachage: "corsaire_confort",
                    prices: [{ q: [2, 14], ht: 66.00, ttc: 79.20, displayQ: "2-14 pièces" }, { q: [15, 24], ht: 62.75, ttc: 75.30, displayQ: "15-24 pièces" }, { q: [25, 49], ht: 60.80, ttc: 72.96, displayQ: "25-49 pièces" }, { q: [50, 79], ht: 59.50, ttc: 71.40, displayQ: "50-79 pièces" }, { q: [80, 149], ht: 57.55, ttc: 69.06, displayQ: "80-149 pièces" }, { q: [150, Infinity], ht: 56.25, ttc: 67.50, displayQ: "150 et +" }]
                }, {
                    id: "BCF300TDTD",
                    name: "Corsaire sans bretelles été Femme - fond LANDSCAPE",
                    ref: "BCF300TDTD/TDNM",
                    options: [],
                    panachage: "corsaire_confort",
                    prices: [{ q: [2, 14], ht: 64.00, ttc: 76.80, displayQ: "2-14 pièces" }, { q: [15, 24], ht: 60.85, ttc: 73.02, displayQ: "15-24 pièces" }, { q: [25, 49], ht: 58.96, ttc: 70.75, displayQ: "25-49 pièces" }, { q: [50, 79], ht: 57.70, ttc: 69.24, displayQ: "50-79 pièces" }, { q: [80, 149], ht: 55.81, ttc: 66.97, displayQ: "80-149 pièces" }, { q: [150, Infinity], ht: 54.55, ttc: 65.46, displayQ: "150 et +" }]
                }, {
                    id: "BCM251SRSR",
                    name: "Collant à bretelles Hiver CONFORT (Mixte) - fond LANDSCAPE",
                    ref: "BCM251SRSR/SRKI",
                    options: [],
                    panachage: "collant_confort",
                    prices: [{ q: [2, 2], ht: 98.50, ttc: 118.20, displayQ: "2 pièces" }, { q: [3, 4], ht: 82.25, ttc: 98.70, displayQ: "3-4 pièces" }, { q: [5, 14], ht: 66.00, ttc: 79.20, displayQ: "5-14 pièces" }, { q: [15, 24], ht: 62.75, ttc: 75.30, displayQ: "15-24 pièces" }, { q: [25, 49], ht: 60.80, ttc: 72.96, displayQ: "25-49 pièces" }, { q: [50, 79], ht: 59.50, ttc: 71.40, displayQ: "50-79 pièces" }, { q: [80, 149], ht: 57.55, ttc: 69.06, displayQ: "80-149 pièces" }, { q: [150, Infinity], ht: 56.25, ttc: 67.50, displayQ: "150 et +" }]
                }, {
                    id: "BCF201SRSR",
                    name: "Collant sans bretelles Hiver Femme - fond LANDSCAPE",
                    ref: "BCF201SRSR/SRKI",
                    options: [],
                    panachage: "collant_confort",
                    prices: [{ q: [2, 2], ht: 95.50, ttc: 114.60, displayQ: "2 pièces" }, { q: [3, 4], ht: 79.75, ttc: 95.70, displayQ: "3-4 pièces" }, { q: [5, 14], ht: 64.00, ttc: 76.80, displayQ: "5-14 pièces" }, { q: [15, 24], ht: 60.85, ttc: 73.02, displayQ: "15-24 pièces" }, { q: [25, 49], ht: 58.96, ttc: 70.75, displayQ: "25-49 pièces" }, { q: [50, 79], ht: 57.70, ttc: 69.24, displayQ: "50-79 pièces" }, { q: [80, 149], ht: 55.81, ttc: 66.97, displayQ: "80-149 pièces" }, { q: [150, Infinity], ht: 54.55, ttc: 65.46, displayQ: "150 et +" }]
                }, {
                    id: "BCM280BPBP_2",
                    name: "Collant à bretelles Hiver AERO (Mixte) - fond CERVINO",
                    ref: "BCM280BPBP/BPKI",
                    options: [],
                    prices: [{ q: [2, 2], ht: 118.00, ttc: 141.60, displayQ: "2 pièces" }, { q: [3, 4], ht: 98.50, ttc: 118.20, displayQ: "3-4 pièces" }, { q: [5, 14], ht: 79.00, ttc: 94.80, displayQ: "5-14 pièces" }, { q: [15, 24], ht: 75.10, ttc: 90.12, displayQ: "15-24 pièces" }, { q: [25, 49], ht: 72.76, ttc: 87.31, displayQ: "25-49 pièces" }, { q: [50, 79], ht: 71.20, ttc: 85.44, displayQ: "50-79 pièces" }, { q: [80, 149], ht: 68.86, ttc: 82.63, displayQ: "80-149 pièces" }, { q: [150, Infinity], ht: 67.30, ttc: 80.76, displayQ: "150 et +" }]
                }, {
                    id: "BCM210SRSR",
                    name: "Collant d'échauffement (Mixte)",
                    ref: "BCM210SRSR/DODO",
                    options: [],
                    prices: [{
                        q: [2, 2],
                        ht: 70.50,
                        ttc: 84.60,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 58.75,
                        ttc: 70.50,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 47.00,
                        ttc: 56.40,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 44.65,
                        ttc: 53.58,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 43.24,
                        ttc: 51.89,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 42.30,
                        ttc: 50.76,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 40.89,
                        ttc: 49.07,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 39.95,
                        ttc: 47.94,
                        displayQ: "150 et +"
                    }]
                }]
            }, {
                subCategory: "Combinaisons et Enfants",
                items: [{
                    id: "CCM180STBT",
                    name: "Combinaison ROUTE MC AERO (Mixte) - fond CERVINO",
                    ref: "CCM180STBT",
                    options: [],
                    prices: [{ q: [1, Infinity], ht: 96.00, ttc: 115.20, displayQ: "Tarif fixe" }]
                }, {
                    id: "CCM480CLCB",
                    name: "Combinaison Cyclo-Cross ML AERO (Mixte) - fond CERVINO",
                    ref: "CCM480CLCB",
                    options: [],
                    prices: [{ q: [1, Infinity], ht: 100.00, ttc: 120.00, displayQ: "Tarif fixe" }]
                }, {
                    id: "CCM180NMTD_PDC",
                    name: "Combinaison CHRONO Route MC AERO (Mixte) - fond CERVINO",
                    ref: "CCM180NMTD/PDC",
                    options: [],
                    prices: [{ q: [1, Infinity], ht: 96.00, ttc: 115.20, displayQ: "Tarif fixe" }]
                }, {
                    id: "CCM480NMTD_PDC",
                    name: "Combinaison CHRONO Route ML AERO (Mixte) - fond CERVINO",
                    ref: "CCM480NMTD/PDC",
                    options: [],
                    prices: [{ q: [1, Infinity], ht: 100.00, ttc: 120.00, displayQ: "Tarif fixe" }]
                }, {
                    id: "CCM180NMTD",
                    name: "Combinaison CHRONO Piste MC AERO (Mixte) - fond CERVINO",
                    ref: "CCM180NMTD",
                    options: [],
                    prices: [{ q: [1, Infinity], ht: 93.00, ttc: 111.60, displayQ: "Tarif fixe" }]
                }, {
                    id: "CCM480NMTD",
                    name: "Combinaison CHRONO Piste ML AERO (Mixte) - fond CERVINO",
                    ref: "CCM480NMTD",
                    options: [],
                    prices: [{ q: [1, Infinity], ht: 97.00, ttc: 116.40, displayQ: "Tarif fixe" }]
                }, {
                    id: "HCE150VESP",
                    name: "Maillot MC Enfant PERFORMANCE",
                    ref: "HCE150VESP",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 35.00,
                        ttc: 42.00,
                        displayQ: "Tarif fixe"
                    }]
                }, {
                    id: "HCE250YAYA",
                    name: "Maillot ML Enfant mi-saison",
                    ref: "HCE250YAYA",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 38.00,
                        ttc: 45.60,
                        displayQ: "Tarif fixe"
                    }]
                }, {
                    id: "HBE250MOMO",
                    name: "Maillot BMX ML Enfant",
                    ref: "HBE250MOMO",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 38.00,
                        ttc: 45.60,
                        displayQ: "Tarif fixe"
                    }]
                }, {
                    id: "HCE050FIFI",
                    name: "Gilet coupe-vent LEGER Enfant",
                    ref: "HCE050FIFI",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 32.00,
                        ttc: 38.40,
                        displayQ: "Tarif fixe"
                    }]
                }, {
                    id: "HCE250IDYA",
                    name: "Veste hiver CONFORT Enfant",
                    ref: "HCE250IDYA",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 70.00,
                        ttc: 84.00,
                        displayQ: "Tarif fixe"
                    }]
                }, {
                    id: "BCE150TDSP",
                    name: "Cuissard à bretelles Enfant",
                    ref: "BCE150TDSP/NMSP",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 35.00,
                        ttc: 42.00,
                        displayQ: "Tarif fixe"
                    }]
                }, {
                    id: "BCE251SRSR",
                    name: "Collant à bretelles Hiver Enfant",
                    ref: "BCE251SRSR/SRKI",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 50.00,
                        ttc: 60.00,
                        displayQ: "Tarif fixe"
                    }]
                }, {
                    id: "BCE210SRSR",
                    name: "Collant d'échauffement Enfant",
                    ref: "BCE210SRSR/DODO",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 41.50,
                        ttc: 49.80,
                        displayQ: "Tarif fixe"
                    }]
                }]
            }],
            running: [{
                subCategory: "Hauts et Bas",
                items: [{
                    id: "HAH150VEVE",
                    name: "Maillot Running / Trail MC (Homme/Femme)",
                    ref: "HAH150VEVE / HAF150VEVE",
                    options: [],
                    panachage: "running_mc",
                    prices: [{
                        q: [2, 2],
                        ht: 45.00,
                        ttc: 54.00,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 37.50,
                        ttc: 45.00,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 30.00,
                        ttc: 36.00,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 28.50,
                        ttc: 34.20,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 27.60,
                        ttc: 33.12,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 27.00,
                        ttc: 32.40,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 26.10,
                        ttc: 31.32,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 25.50,
                        ttc: 30.60,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HAH050VEVE",
                    name: "Débardeur athlétisme (Homme/Femme)",
                    ref: "HAH050VEVE / HAF050VEVE",
                    options: [],
                    panachage: "running_debardeur",
                    prices: [{
                        q: [2, 2],
                        ht: 40.50,
                        ttc: 48.60,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 33.75,
                        ttc: 40.50,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 27.00,
                        ttc: 32.40,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 25.65,
                        ttc: 30.78,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 24.84,
                        ttc: 29.81,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 24.30,
                        ttc: 29.16,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 23.49,
                        ttc: 28.19,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 22.95,
                        ttc: 27.54,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "ABROFOSMSM",
                    name: "Brassière Femme",
                    ref: "ABROFOSMSM",
                    options: [],
                    prices: [{
                        q: [2, 14],
                        ht: 30.00,
                        ttc: 36.00,
                        displayQ: "2-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 28.50,
                        ttc: 34.20,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 27.60,
                        ttc: 33.12,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 27.00,
                        ttc: 32.40,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 26.10,
                        ttc: 31.32,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 25.50,
                        ttc: 30.60,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "HAH250KIKI",
                    name: "Maillot running ML Hiver (Homme/Femme)",
                    ref: "HAH250KIKI / HAF250KIKI",
                    options: [],
                    panachage: "running_ml",
                    prices: [{
                        q: [2, 2],
                        ht: 67.50,
                        ttc: 81.00,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 56.25,
                        ttc: 67.50,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 45.00,
                        ttc: 54.00,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 42.75,
                        ttc: 51.30,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 41.40,
                        ttc: 49.68,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 40.50,
                        ttc: 48.60,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 39.15,
                        ttc: 46.98,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 38.25,
                        ttc: 45.90,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "BAM100ADAD",
                    name: "Short Running (Mixte)",
                    ref: "BAM100ADAD",
                    options: [],
                    prices: [{
                        q: [2, 2],
                        ht: 57.00,
                        ttc: 68.40,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 47.50,
                        ttc: 57.00,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 38.00,
                        ttc: 45.60,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 36.10,
                        ttc: 43.32,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 34.96,
                        ttc: 41.95,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 34.20,
                        ttc: 41.04,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 33.06,
                        ttc: 39.67,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 32.30,
                        ttc: 38.76,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "BAF110ALAL",
                    name: "Shorty Running Femme",
                    ref: "BAF110ALAL",
                    options: [],
                    prices: [{
                        q: [2, 2],
                        ht: 60.00,
                        ttc: 72.00,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 50.00,
                        ttc: 60.00,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 40.00,
                        ttc: 48.00,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 38.00,
                        ttc: 45.60,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 36.80,
                        ttc: 44.16,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 36.00,
                        ttc: 43.20,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 34.80,
                        ttc: 41.76,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 34.00,
                        ttc: 40.80,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "BAH110ALAL",
                    name: "Cuissard Running (Mixte)",
                    ref: "BAH110ALAL",
                    options: [],
                    prices: [{
                        q: [2, 2],
                        ht: 60.00,
                        ttc: 72.00,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 50.00,
                        ttc: 60.00,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 40.00,
                        ttc: 48.00,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 38.00,
                        ttc: 45.60,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 36.80,
                        ttc: 44.16,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 36.00,
                        ttc: 43.20,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 34.80,
                        ttc: 41.76,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 34.00,
                        ttc: 40.80,
                        displayQ: "150 et +"
                    }]
                }, {
                    id: "BAM210ALAL",
                    name: "Collant Running (Mixte)",
                    ref: "BAM210ALAL",
                    options: [],
                    prices: [{
                        q: [2, 2],
                        ht: 75.00,
                        ttc: 90.00,
                        displayQ: "2 pièces"
                    }, {
                        q: [3, 4],
                        ht: 62.50,
                        ttc: 75.00,
                        displayQ: "3-4 pièces"
                    }, {
                        q: [5, 14],
                        ht: 50.00,
                        ttc: 60.00,
                        displayQ: "5-14 pièces"
                    }, {
                        q: [15, 24],
                        ht: 47.50,
                        ttc: 57.00,
                        displayQ: "15-24 pièces"
                    }, {
                        q: [25, 49],
                        ht: 46.00,
                        ttc: 55.20,
                        displayQ: "25-49 pièces"
                    }, {
                        q: [50, 79],
                        ht: 45.00,
                        ttc: 54.00,
                        displayQ: "50-79 pièces"
                    }, {
                        q: [80, 149],
                        ht: 43.50,
                        ttc: 52.20,
                        displayQ: "80-149 pièces"
                    }, {
                        q: [150, Infinity],
                        ht: 42.50,
                        ttc: 51.00,
                        displayQ: "150 et +"
                    }]
                }]
            }, {
                subCategory: "Enfants Running",
                items: [{
                    id: "HAE150VEVE",
                    name: "Maillot running MC Enfant",
                    ref: "HAE150VEVE",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 25.00,
                        ttc: 30.00,
                        displayQ: "Tarif fixe"
                    }]
                }, {
                    id: "HAE050VEVE",
                    name: "Débardeur athlétisme Enfant",
                    ref: "HAE050VEVE",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 22.50,
                        ttc: 27.00,
                        displayQ: "Tarif fixe"
                    }]
                }, {
                    id: "BAE110ALAL",
                    name: "Cuissard Running Enfant",
                    ref: "BAE110ALAL",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 30.00,
                        ttc: 36.00,
                        displayQ: "Tarif fixe"
                    }]
                }]
            }],
            triathlon: [{
                subCategory: "Trifonctions",
                items: [{
                    id: "CTE080K7BT",
                    name: "Trifonction Enfant",
                    ref: "CTE080K7BT/K7NM",
                    options: [],
                    prices: [
                        { q: [2, 4], ht: 75.00, ttc: 90.00, displayQ: "2-4 pièces" },
                        { q: [5, 14], ht: 75.00, ttc: 90.00, displayQ: "5-14 pièces" },
                        { q: [15, 24], ht: 71.25, ttc: 85.50, displayQ: "15-24 pièces" },
                        { q: [25, 49], ht: 69.00, ttc: 82.80, displayQ: "25-49 pièces" },
                        { q: [50, 79], ht: 67.50, ttc: 81.00, displayQ: "50-79 pièces" },
                        { q: [80, 149], ht: 65.25, ttc: 78.30, displayQ: "80-149 pièces" },
                        { q: [150, Infinity], ht: 63.75, ttc: 76.50, displayQ: "150 et +" }
                    ]
                }, {
                    id: "CTH080K7BT",
                    name: "Trifonction Courte/Moyenne dist. (Homme/Femme)",
                    ref: "CTH080K7BT / CTF080K7NM",
                    options: [],
                    prices: [
                        { q: [2, 4], ht: 85.00, ttc: 102.00, displayQ: "2-4 pièces" },
                        { q: [5, 14], ht: 85.00, ttc: 102.00, displayQ: "5-14 pièces" },
                        { q: [15, 24], ht: 80.75, ttc: 96.90, displayQ: "15-24 pièces" },
                        { q: [25, 49], ht: 78.20, ttc: 93.84, displayQ: "25-49 pièces" },
                        { q: [50, 79], ht: 76.50, ttc: 91.80, displayQ: "50-79 pièces" },
                        { q: [80, 149], ht: 73.95, ttc: 88.74, displayQ: "80-149 pièces" },
                        { q: [150, Infinity], ht: 72.25, ttc: 86.70, displayQ: "150 et +" }
                    ]
                }, {
                    id: "CTM180K7BT",
                    name: "Trifonction Longue distance (Mixte)",
                    ref: "CTM180K7BT/K7NM",
                    options: [],
                    prices: [
                        { q: [2, 4], ht: 95.00, ttc: 114.00, displayQ: "2-4 pièces" },
                        { q: [5, 14], ht: 95.00, ttc: 114.00, displayQ: "5-14 pièces" },
                        { q: [15, 24], ht: 90.25, ttc: 108.30, displayQ: "15-24 pièces" },
                        { q: [25, 49], ht: 87.40, ttc: 104.88, displayQ: "25-49 pièces" },
                        { q: [50, 79], ht: 85.50, ttc: 102.60, displayQ: "50-79 pièces" },
                        { q: [80, 149], ht: 82.65, ttc: 99.18, displayQ: "80-149 pièces" },
                        { q: [150, Infinity], ht: 80.75, ttc: 96.90, displayQ: "150 et +" }
                    ]
                }]
            }],
            accessoires_perso: [{
                subCategory: "Accessoires Personnalisés",
                isAccessory: true,
                items: [{
                    id: "ACC_BANDANA",
                    name: "Bandana",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [10, 19],
                        ht: 10.00,
                        ttc: 12.00,
                        displayQ: "10-19 pièces"
                    }, {
                        q: [20, 49],
                        ht: 8.70,
                        ttc: 10.44,
                        displayQ: "20-49 pièces"
                    }, {
                        q: [50, Infinity],
                        ht: 8.50,
                        ttc: 10.20,
                        displayQ: "50 et +"
                    }]
                }, {
                    id: "ACC_BANDEAU",
                    name: "Bandeau",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [10, 19],
                        ht: 7.50,
                        ttc: 9.00,
                        displayQ: "10-19 pièces"
                    }, {
                        q: [20, 49],
                        ht: 7.00,
                        ttc: 8.40,
                        displayQ: "20-49 pièces"
                    }, {
                        q: [50, Infinity],
                        ht: 6.00,
                        ttc: 7.20,
                        displayQ: "50 et +"
                    }]
                }, {
                    id: "ACC_TOURDECOU",
                    name: "Tour de cou",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [10, 19],
                        ht: 8.50,
                        ttc: 10.20,
                        displayQ: "10-19 pièces"
                    }, {
                        q: [20, 49],
                        ht: 7.25,
                        ttc: 8.70,
                        displayQ: "20-49 pièces"
                    }, {
                        q: [50, Infinity],
                        ht: 7.00,
                        ttc: 8.40,
                        displayQ: "50 et +"
                    }]
                }, {
                    id: "ACC_PASSEMONTAGNE",
                    name: "Passe-Montagne",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [10, 19],
                        ht: 15.00,
                        ttc: 18.00,
                        displayQ: "10-19 pièces"
                    }, {
                        q: [20, 49],
                        ht: 13.00,
                        ttc: 15.60,
                        displayQ: "20-49 pièces"
                    }, {
                        q: [50, Infinity],
                        ht: 12.00,
                        ttc: 14.40,
                        displayQ: "50 et +"
                    }]
                }, {
                    id: "ACC_MANCHETTES_THERM",
                    name: "Manchettes thermiques",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [10, 19],
                        ht: 17.00,
                        ttc: 20.40,
                        displayQ: "10-19 pièces"
                    }, {
                        q: [20, 49],
                        ht: 15.00,
                        ttc: 18.00,
                        displayQ: "20-49 pièces"
                    }, {
                        q: [50, Infinity],
                        ht: 14.00,
                        ttc: 16.80,
                        displayQ: "50 et +"
                    }]
                }, {
                    id: "ACC_MANCHETTES_ETE",
                    name: "Manchettes été",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [10, 19],
                        ht: 12.50,
                        ttc: 15.00,
                        displayQ: "10-19 pièces"
                    }, {
                        q: [20, 49],
                        ht: 11.75,
                        ttc: 14.10,
                        displayQ: "20-49 pièces"
                    }, {
                        q: [50, Infinity],
                        ht: 10.75,
                        ttc: 12.90,
                        displayQ: "50 et +"
                    }]
                }, {
                    id: "ACC_JAMBIERES",
                    name: "Jambières",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [10, 19],
                        ht: 22.00,
                        ttc: 26.40,
                        displayQ: "10-19 pièces"
                    }, {
                        q: [20, 49],
                        ht: 20.00,
                        ttc: 24.00,
                        displayQ: "20-49 pièces"
                    }, {
                        q: [50, Infinity],
                        ht: 19.00,
                        ttc: 22.80,
                        displayQ: "50 et +"
                    }]
                }, {
                    id: "ACC_GANTS_ETE",
                    name: "Gants été",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [10, 19],
                        ht: 17.50,
                        ttc: 21.00,
                        displayQ: "10-19 pièces"
                    }, {
                        q: [20, 49],
                        ht: 15.00,
                        ttc: 18.00,
                        displayQ: "20-49 pièces"
                    }, {
                        q: [50, Infinity],
                        ht: 14.00,
                        ttc: 16.80,
                        displayQ: "50 et +"
                    }]
                }, {
                    id: "ACC_GANTS_ETE_SLIM",
                    name: "Gants été slim",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [10, 19],
                        ht: 25.00,
                        ttc: 30.00,
                        displayQ: "10-19 pièces"
                    }, {
                        q: [20, 49],
                        ht: 23.50,
                        ttc: 28.20,
                        displayQ: "20-49 pièces"
                    }, {
                        q: [50, Infinity],
                        ht: 21.00,
                        ttc: 25.20,
                        displayQ: "50 et +"
                    }]
                }, {
                    id: "ACC_TAPIS",
                    name: "Tapis de transition multisports",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [10, 19],
                        ht: 9.00,
                        ttc: 10.80,
                        displayQ: "10-19 pièces"
                    }, {
                        q: [20, 49],
                        ht: 7.65,
                        ttc: 9.18,
                        displayQ: "20-49 pièces"
                    }, {
                        q: [50, Infinity],
                        ht: 7.00,
                        ttc: 8.40,
                        displayQ: "50 et +"
                    }]
                }, {
                    id: "ACC_CHAUSSETTES_AERO",
                    name: "Chaussettes vélo aéro",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [10, 19],
                        ht: 17.50,
                        ttc: 21.00,
                        displayQ: "10-19 pièces"
                    }, {
                        q: [20, 49],
                        ht: 17.00,
                        ttc: 20.40,
                        displayQ: "20-49 pièces"
                    }, {
                        q: [50, Infinity],
                        ht: 16.00,
                        ttc: 19.20,
                        displayQ: "50 et +"
                    }]
                }, {
                    id: "ACC_CHAUSSETTES",
                    name: "Chaussettes (vélo/CAP)",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [50, 99],
                        ht: 10.90,
                        ttc: 13.08,
                        displayQ: "50-99 pièces"
                    }, {
                        q: [100, Infinity],
                        ht: 9.90,
                        ttc: 11.88,
                        displayQ: "100 et +"
                    }]
                }, {
                    id: "ACC_CASQUETTE",
                    name: "Casquette (Gapette) vélo",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [50, 99],
                        ht: 12.50,
                        ttc: 15.00,
                        displayQ: "50-99 pièces"
                    }, {
                        q: [100, Infinity],
                        ht: 11.00,
                        ttc: 13.20,
                        displayQ: "100 et +"
                    }]
                }]
            }],
            accessoires_perm: [{
                subCategory: "Gamme Noret",
                isAccessory: true,
                link: "https://acrobat.adobe.com/id/urn:aaid:sc:EU:8e9f373d-ef5b-4822-b7ef-ffca136128cb",
                description: "Cliquez sur le titre pour accéder au catalogue.",
                items: [{
                    id: "PERM_NORET_SOUSMAILLOT_SM",
                    name: "Sous-maillot sans manches",
                    ref: "HCM080AVAV/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 33.33,
                        ttc: 40.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_SOUSMAILLOT_MC",
                    name: "Sous-maillot MC mi-saison",
                    ref: "HCM180COCO/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 37.50,
                        ttc: 45.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_SOUSMAILLOT_ML",
                    name: "Sous-maillot ML Hiver",
                    ref: "HCM280COCO/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 45.83,
                        ttc: 55.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_SOUSCASQUE",
                    name: "Sous casque",
                    ref: "ASC2FOSRSR/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 15.00,
                        ttc: 18.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_CAGOULE",
                    name: "Cagoule",
                    ref: "ACG2FOSRSR/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 16.67,
                        ttc: 20.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_GANTS_HIVER",
                    name: "Gants Hiver",
                    ref: "AGA2N01373/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 45.83,
                        ttc: 55.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_GANTS_CONFORT",
                    name: "Gants été CONFORT",
                    ref: "AGAON00408/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 25.00,
                        ttc: 30.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_GANTS_SLIM",
                    name: "Gants été SLIM",
                    ref: "AGA4N01212/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 33.33,
                        ttc: 40.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_GANTS_MISAISON",
                    name: "Gants mi-saison",
                    ref: "AGAIN01188/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 25.00,
                        ttc: 30.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_COUVRE_AERO",
                    name: "Couvre-chaussures AÉRO",
                    ref: "ACC4N01212/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 33.33,
                        ttc: 40.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_COUVRE_HIVER",
                    name: "Couvre-chaussures HIVER/PLUIE",
                    ref: "ACC5N00408/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 54.17,
                        ttc: 65.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_BANDEAU",
                    name: "Bandeau",
                    ref: "ACO2FOCLCL/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 10.00,
                        ttc: 12.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_TOURDECOU",
                    name: "Tour de cou",
                    ref: "ATCIFONINI/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 12.50,
                        ttc: 15.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_GAPETTE",
                    name: "Gapette",
                    ref: "AM086",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 16.67,
                        ttc: 20.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_MANCHETTES",
                    name: "Manchettes",
                    ref: "AMA2FOSRSR/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 27.50,
                        ttc: 33.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_GENOUILLERES",
                    name: "Genouillères",
                    ref: "AGEIFOSRSR/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 27.50,
                        ttc: 33.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_JAMBIERES",
                    name: "Jambières",
                    ref: "AJA2FOSRSR/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 33.33,
                        ttc: 40.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_CHAUSSETTES_AERO",
                    name: "Chaussettes AÉRO",
                    ref: "ACH4FOEGEG/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 25.00,
                        ttc: 30.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_NORET_CHAUSSETTES_MIHAUTES",
                    name: "Chaussettes mi-hautes",
                    ref: "ACHONO1384/NORET",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 14.17,
                        ttc: 17.00,
                        displayQ: "Prix unique"
                    }]
                }]
            }, {
                subCategory: "Gamme Brodelec",
                isAccessory: true,
                isBrodelec: true,
                link: "https://acrobat.adobe.com/id/urn:aaid:sc:EU:410838c3-d7dc-42d7-9163-586320a8b21f",
                description: "Tarif 2025 - Frais de port inclus. Cliquez sur le titre pour accéder au catalogue.",
                items: [{
                    id: "PERM_BROD_CEINTURE",
                    name: "Ceinture + 2 Bracelets haute visibilité",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 16.58,
                        ttc: 19.90,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_BROD_VESTE_HV",
                    name: "Veste haute visibilité",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 58.25,
                        ttc: 69.90,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_BROD_VESTE_TRAIL",
                    name: "Veste Crosslite Trail & Trek",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 42.42,
                        ttc: 50.90,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_BROD_GILET_HV",
                    name: "Gilet coupe-vent haute visibilité",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 33.33,
                        ttc: 40.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_BROD_GANTS",
                    name: "Gants de sport Softshell",
                    ref: "",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 13.25,
                        ttc: 15.90,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_BROD_TOURDECOU",
                    name: "Tour de cou haute visibilité",
                    ref: "BF950",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 5.75,
                        ttc: 6.90,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_BROD_BANDEAU",
                    name: "Bandeau Softshell Sports Tech",
                    ref: "BF316",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 8.33,
                        ttc: 10.00,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_BROD_CASQUETTE_5P",
                    name: "Casquette 5 panneaux",
                    ref: "BF015",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 8.25,
                        ttc: 9.90,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_BROD_CASQUETTE_5PC",
                    name: "Casquette 5 panneaux bicolore",
                    ref: "BF15C",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 8.25,
                        ttc: 9.90,
                        displayQ: "Prix unique"
                    }]
                }, {
                    id: "PERM_BROD_CASQUETTE_SPORT",
                    name: "Casquette de sport 5 panneaux",
                    ref: "PK970",
                    options: [],
                    prices: [{
                        q: [1, Infinity],
                        ht: 8.33,
                        ttc: 10.00,
                        displayQ: "Prix unique"
                    }]
                }]
            }]
        };

// =================================================================================
// --- DATA & CONFIG ---
// =================================================================================

// Grille de prix pour les vestes HIVER CONFORT (prix d'origine)
const confortWinterJacketTiers = [
    { quantity: 1, price: 155.40 }, { quantity: 2, price: 133.20 }, { quantity: 3, price: 111.00 },
    { quantity: 5, price: 88.80 }, { quantity: 15, price: 84.36 }, { quantity: 25, price: 81.70 },
    { quantity: 50, price: 79.92 }, { quantity: 80, price: 77.26 }, { quantity: 150, price: 75.48 }
];

// Grille de prix pour les vestes HIVER THERMIQUE
const thermiqueWinterJacketTiers = [
    { quantity: 1, price: 159.60 }, { quantity: 2, price: 136.80 }, { quantity: 3, price: 114.00 },
    { quantity: 5, price: 91.20 }, { quantity: 15, price: 86.64 }, { quantity: 25, price: 83.90 },
    { quantity: 50, price: 82.08 }, { quantity: 80, price: 79.34 }, { quantity: 150, price: 77.52 }
];

let allAvailableProducts = [
    // =========================================================================
    // 1. CYCLISME - MAILLOTS MANCHES COURTES (MC)
    // =========================================================================
    { name: 'MAILLOT CLASSIQUE HOMME CONFORT MC VTT/ROUTE', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', pricingGroup: 'maillotClassiqueMC', pricingTiers: [ { quantity: 1, price: 86.10 }, { quantity: 2, price: 73.80 }, { quantity: 3, price: 61.50 }, { quantity: 5, price: 49.20 }, { quantity: 15, price: 46.74 }, { quantity: 25, price: 45.26 }, { quantity: 50, price: 44.28 }, { quantity: 80, price: 42.80 }, { quantity: 150, price: 41.82 } ] },
    { name: 'MAILLOT CLASSIQUE FEMME CONFORT MC', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', pricingGroup: 'maillotClassiqueMC', pricingTiers: [ { quantity: 1, price: 86.10 }, { quantity: 2, price: 73.80 }, { quantity: 3, price: 61.50 }, { quantity: 5, price: 49.20 }, { quantity: 15, price: 46.74 }, { quantity: 25, price: 45.26 }, { quantity: 50, price: 44.28 }, { quantity: 80, price: 42.80 }, { quantity: 150, price: 41.82 } ] },
    { name: 'MAILLOT MIXTE CONFORT SANS MANCHE', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', pricingTiers: [ { quantity: 1, price: 86.10 }, { quantity: 2, price: 73.80 }, { quantity: 3, price: 61.50 }, { quantity: 5, price: 49.20 }, { quantity: 15, price: 46.74 }, { quantity: 25, price: 45.26 }, { quantity: 50, price: 44.28 }, { quantity: 80, price: 42.80 }, { quantity: 150, price: 41.82 } ] },
    { name: 'MAILLOT MIXTE PERFORMANCE MC', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', pricingGroup: 'maillotPerformanceMC', pricingTiers: [ { quantity: 1, price: 89.25 },{ quantity: 2, price: 76.50 }, { quantity: 3, price: 63.75 }, { quantity: 5, price: 51.00 }, { quantity: 15, price: 48.45 }, { quantity: 25, price: 46.92 }, { quantity: 50, price: 45.90 }, { quantity: 80, price: 44.37 }, { quantity: 150, price: 43.35 } ] },
    { name: 'MAILLOT VTT/DESCENTE MIXTE CONFORT MC (Très ample)', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', sizeType: 'largeHaut', hasOptions: false, pricingTiers: [ { quantity: 1, price: 77.70 }, { quantity: 2, price: 66.60 }, { quantity: 3, price: 55.50 }, { quantity: 5, price: 44.40 }, { quantity: 15, price: 42.18 }, { quantity: 25, price: 40.85 }, { quantity: 50, price: 39.96 }, { quantity: 80, price: 38.63 }, { quantity: 150, price: 37.74 } ] },
    { name: 'MAILLOT MIXTE AERO MC', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', sizeType: 'aero', pricingGroup: 'maillotAeroMC', pricingTiers: [ { quantity: 1, price: 96.60 }, { quantity: 2, price: 82.80 }, { quantity: 3, price: 69.00 }, { quantity: 5, price: 55.20 }, { quantity: 15, price: 52.44 }, { quantity: 25, price: 45.00 }, { quantity: 50, price: 44.10 }, { quantity: 80, price: 42.76 }, { quantity: 150, price: 41.88 } ] },  
    
    // =========================================================================
    // 2. CYCLISME - MAILLOTS MANCHES LONGUES (ML)
    // =========================================================================
    { name: 'MAILLOT MI-SAISON HOMME CONFORT ML', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Longues', pricingGroup: 'maillotMiSaisonML', pricingTiers: [ { quantity: 1, price: 94.50 }, { quantity: 2, price: 81.00 }, { quantity: 3, price: 67.50 }, { quantity: 5, price: 54.00 }, { quantity: 15, price: 51.30 }, { quantity: 25, price: 49.68 }, { quantity: 50, price: 48.60 }, { quantity: 80, price: 46.98 }, { quantity: 150, price: 45.90 } ] },
    { name: 'MAILLOT MI-SAISON FEMME CONFORT ML', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Longues', pricingGroup: 'maillotMiSaisonML', pricingTiers: [ { quantity: 1, price: 94.50 }, { quantity: 2, price: 81.00 }, { quantity: 3, price: 67.50 }, { quantity: 5, price: 54.00 }, { quantity: 15, price: 51.30 }, { quantity: 25, price: 49.68 }, { quantity: 50, price: 48.60 }, { quantity: 80, price: 46.98 }, { quantity: 150, price: 45.90 } ] },
    { name: 'MAILLOT BMX MIXTE CONFORT ML (Très ample)', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Longues', sizeType: 'largeHaut', hasOptions: false, pricingTiers: [ { quantity: 1, price: 88.20 }, { quantity: 2, price: 75.60 }, { quantity: 3, price: 63.00 }, { quantity: 5, price: 50.40 }, { quantity: 15, price: 47.88 }, { quantity: 25, price: 46.37 }, { quantity: 50, price: 45.36 }, { quantity: 80, price: 43.85 }, { quantity: 150, price: 42.84 } ] },
    { name: 'MAILLOT MI-SAISON MIXTE AERO ML', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Longues', sizeType: 'aero', pricingTiers: [ { quantity: 1, price: 107.10 }, { quantity: 2, price: 91.80 }, { quantity: 3, price: 76.50 }, { quantity: 5, price: 61.20 }, { quantity: 15, price: 58.14 }, { quantity: 25, price: 56.30 }, { quantity: 50, price: 55.08 }, { quantity: 80, price: 53.24 }, { quantity: 150, price: 52.02 } ] },
    { name: 'MAILLOT PLUIE MIXTE AERO MC (non sublimé, marquage DTF)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingTiers: [{ quantity: 1, price: 85.20 }], sizeType: 'aero' },
    { name: 'MAILLOT PLUIE MIXTE AERO ML (non sublimé, marquage DTF)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingTiers: [{ quantity: 1, price: 102.00 }], sizeType: 'aero' },

    // =========================================================================
    // 3. CYCLISME - ESSENTIELS, GILETS ET VESTES
    // =========================================================================
    { name: 'GILET COUPE-VENT LEGER MIXTE (vent et pluie fine, sans poche dos)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 72.45 }, { quantity: 2, price: 62.10 }, { quantity: 3, price: 51.75 }, { quantity: 5, price: 41.40 }, { quantity: 15, price: 39.33 }, { quantity: 25, price: 38.09 }, { quantity: 50, price: 37.26 }, { quantity: 80, price: 36.02 }, { quantity: 150, price: 35.19 } ] },
    { name: 'GILET COUPE-VENT MI-SAISON MIXTE (dos ajouré)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 91.35 }, { quantity: 2, price: 78.30 }, { quantity: 3, price: 65.25 }, { quantity: 5, price: 52.20 }, { quantity: 15, price: 49.59 }, { quantity: 25, price: 48.02 }, { quantity: 50, price: 46.98 }, { quantity: 80, price: 45.41 }, { quantity: 150, price: 44.37 } ] },
    { name: 'GILET COUPE-VENT HIVER MIXTE (tout membranné)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'giletCoupeVent', pricingTiers: [ { quantity: 1, price: 97.65 }, { quantity: 2, price: 83.70 }, { quantity: 3, price: 69.75 }, { quantity: 5, price: 55.80 }, { quantity: 15, price: 53.01 }, { quantity: 25, price: 51.34 }, { quantity: 50, price: 50.22 }, { quantity: 80, price: 48.55 }, { quantity: 150, price: 47.43 } ] },
    { name: 'COUPE-VENT LEGER MIXTE CONFORT (vent et pluie fine)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'coupeVent', pricingTiers: [ { quantity: 1, price: 96.60 }, { quantity: 2, price: 82.80 }, { quantity: 3, price: 69.00 }, { quantity: 5, price: 55.20 }, { quantity: 15, price: 52.44 }, { quantity: 25, price: 50.78 }, { quantity: 50, price: 49.68 }, { quantity: 80, price: 48.02 }, { quantity: 150, price: 46.92 } ] },
    { name: 'COUPE-VENT LEGER DEPERLANT MIXTE CONFORT (avec membranne)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'coupeVent', pricingTiers: [ { quantity: 1, price: 128.10 }, { quantity: 2, price: 109.80 }, { quantity: 3, price: 91.50 }, { quantity: 5, price: 73.20 }, { quantity: 15, price: 69.54 }, { quantity: 25, price: 67.34 }, { quantity: 50, price: 65.88 }, { quantity: 80, price: 63.68 }, { quantity: 150, price: 62.22 } ] },
    { name: 'VESTE MI-SAISON MIXTE CONFORT (membranne coupe-vent + mi-saison)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteMiSaison', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },
    { name: 'VESTE MI-SAISON MIXTE CONFORT FEMME avec -6cm aux ML', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteMiSaison', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },
    { name: 'VESTE HIVER HOMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteHiver', pricingTiers: confortWinterJacketTiers },
    { name: 'VESTE HIVER FEMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteHiver', pricingTiers: confortWinterJacketTiers },
    { name: 'VESTE HIVER THERMIQUE HOMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteHiver', pricingTiers: thermiqueWinterJacketTiers },
    { name: 'VESTE HIVER THERMIQUE FEMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteHiver', pricingTiers: thermiqueWinterJacketTiers },
    { name: 'VESTE HIVER MIXTE AERO', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', sizeType: 'aero', pricingTiers: [ { quantity: 1, price: 147.00 }, { quantity: 2, price: 126.00 }, { quantity: 3, price: 105.00 }, { quantity: 5, price: 84.00 }, { quantity: 15, price: 79.80 }, { quantity: 25, price: 77.28 }, { quantity: 50, price: 75.60 }, { quantity: 80, price: 73.08 }, { quantity: 150, price: 71.40 } ] },

    // =========================================================================
    // 4. CYCLISME - BAS (CUISSARDS, CORSAIRES, COLLANTS)
    // =========================================================================
    { name: 'CUISSARD A BRETELLES HOMME CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', isCuissardOrCollant: true, pricingGroup: 'cuissardConfortLandscape', pricingTiers: [ { quantity: 1, price: 121.80 }, { quantity: 2, price: 104.40 }, { quantity: 3, price: 87.00 }, { quantity: 5, price: 69.60 }, { quantity: 15, price: 66.12 }, { quantity: 25, price: 64.03 }, { quantity: 50, price: 62.64 }, { quantity: 80, price: 60.55 }, { quantity: 150, price: 59.16 }, ] },
    { name: 'CUISSARD A BRETELLES FEMME CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', isCuissardOrCollant: true, pricingGroup: 'cuissardConfortLandscape', pricingTiers: [ { quantity: 1, price: 121.80 }, { quantity: 2, price: 104.40 }, { quantity: 3, price: 87.00 }, { quantity: 5, price: 69.60 }, { quantity: 15, price: 66.12 }, { quantity: 25, price: 64.03 }, { quantity: 50, price: 62.64 }, { quantity: 80, price: 60.55 }, { quantity: 150, price: 59.16 }, ] },
    { name: 'CUISSARD FEMME SANS BRETELLES CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', isCuissardOrCollant: true, pricingGroup: 'cuissardConfortLandscape', pricingTiers: [ { quantity: 1, price: 117.60 }, { quantity: 2, price: 100.80 }, { quantity: 3, price: 84.00 }, { quantity: 5, price: 67.20 }, { quantity: 15, price: 63.84 }, { quantity: 25, price: 61.82 }, { quantity: 50, price: 60.48 }, { quantity: 80, price: 58.46 }, { quantity: 150, price: 57.12 }, ] },
    { name: 'CUISSARD HOMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', sizeType: 'aero', isCuissardOrCollant: true, pricingGroup: 'cuissardAeroCervino', pricingTiers: [ { quantity: 1, price: 142.80 }, { quantity: 2, price: 122.40 }, { quantity: 3, price: 102.00 }, { quantity: 5, price: 81.60 }, { quantity: 15, price: 77.52 }, { quantity: 25, price: 75.07 }, { quantity: 50, price: 73.44 }, { quantity: 80, price: 70.99 }, { quantity: 150, price: 69.36 }, ] },
    { name: 'CUISSARD FEMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', sizeType: 'aero', isCuissardOrCollant: true, pricingGroup: 'cuissardAeroCervino', pricingTiers: [ { quantity: 1, price: 142.80 }, { quantity: 2, price: 122.40 }, { quantity: 3, price: 102.00 }, { quantity: 5, price: 81.60 }, { quantity: 15, price: 77.52 }, { quantity: 25, price: 75.07 }, { quantity: 50, price: 73.44 }, { quantity: 80, price: 70.99 }, { quantity: 150, price: 69.36 }, ] },
    { name: 'SHORT VTT FOND Peau ENDURANCE 2.5', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', isCuissardOrCollant: true, pricingTiers: [ { quantity: 1, price: 75.00 } ] },
    { name: 'CORSAIRE HOMME A BRETELLES CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'corsaireConfortLandscape', pricingTiers: [ { quantity: 1, price: 79.20 }, { quantity: 2, price: 79.20 }, { quantity: 3, price: 79.20 }, { quantity: 5, price: 79.20 }, { quantity: 15, price: 75.24 }, { quantity: 25, price: 72.86 }, { quantity: 50, price: 71.28 }, { quantity: 80, price: 68.90 }, { quantity: 150, price: 67.32 } ] },
    { name: 'CORSAIRE FEMME SANS BRETELLES CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'corsaireConfortLandscape', pricingTiers: [ { quantity: 1, price: 76.80 }, { quantity: 2, price: 76.80 }, { quantity: 3, price: 76.80 }, { quantity: 5, price: 76.80 }, { quantity: 15, price: 72.96 }, { quantity: 25, price: 70.66 }, { quantity: 50, price: 69.12 }, { quantity: 80, price: 66.82 }, { quantity: 150, price: 65.28 } ] },
    { name: 'COLLANT HIVER A BRETELLES HOMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'collantHiverConfortLandscape', pricingTiers: [ { quantity: 1, price: 138.60 }, { quantity: 2, price: 118.80 }, { quantity: 3, price: 99.00 }, { quantity: 5, price: 79.20 }, { quantity: 15, price: 75.24 }, { quantity: 25, price: 72.86 }, { quantity: 50, price: 71.28 }, { quantity: 80, price: 68.90 }, { quantity: 150, price: 67.32 }, ] },
    { name: 'COLLANT HIVER A BRETELLES FEMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'collantHiverConfortLandscape', pricingTiers: [ { quantity: 1, price: 138.60 }, { quantity: 2, price: 118.80 }, { quantity: 3, price: 99.00 }, { quantity: 5, price: 79.20 }, { quantity: 15, price: 75.24 }, { quantity: 25, price: 72.86 }, { quantity: 50, price: 71.28 }, { quantity: 80, price: 68.90 }, { quantity: 150, price: 67.32 }, ] },
    { name: 'COLLANT HIVER FEMME SANS BRETELLES CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'collantHiverConfortLandscape', pricingTiers: [ { quantity: 1, price: 134.40 }, { quantity: 2, price: 115.20 }, { quantity: 3, price: 96.00 }, { quantity: 5, price: 76.80 }, { quantity: 15, price: 72.96 }, { quantity: 25, price: 70.66 }, { quantity: 50, price: 69.12 }, { quantity: 80, price: 66.82 }, { quantity: 150, price: 65.28 }, ] },
    { name: 'COLLANT HIVER HOMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', pricingGroup: 'collantHiverAeroCervino', sizeType: 'aero', isCuissardOrCollant: true, pricingTiers: [ { quantity: 1, price: 165.90 }, { quantity: 2, price: 142.20 }, { quantity: 3, price: 118.50 }, { quantity: 5, price: 94.80 }, { quantity: 15, price: 90.06 }, { quantity: 25, price: 87.22 }, { quantity: 50, price: 85.32 }, { quantity: 80, price: 82.48 }, { quantity: 150, price: 80.58 }, ] },
    { name: 'COLLANT HIVER FEMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', pricingGroup: 'collantHiverAeroCervino', sizeType: 'aero', isCuissardOrCollant: true, pricingTiers: [ { quantity: 1, price: 165.90 }, { quantity: 2, price: 142.20 }, { quantity: 3, price: 118.50 }, { quantity: 5, price: 94.80 }, { quantity: 15, price: 90.06 }, { quantity: 25, price: 87.22 }, { quantity: 50, price: 85.32 }, { quantity: 80, price: 82.48 }, { quantity: 150, price: 80.58 }, ] },
    { name: 'COLLANT MIXTE ECHAUFFEMENT', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingTiers: [ { quantity: 1, price: 98.70 }, { quantity: 2, price: 84.60 }, { quantity: 3, price: 70.50 }, { quantity: 5, price: 56.40 }, { quantity: 15, price: 53.58 }, { quantity: 25, price: 51.89 }, { quantity: 50, price: 50.76 }, { quantity: 80, price: 49.07 }, { quantity: 150, price: 47.94 }, ] },

    // =========================================================================
    // 5. CYCLISME - COMBINAISONS
    // =========================================================================
    { name: 'COMBINAISON ROUTE MANCHES COURTES HOMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 115.20 }] },
    { name: 'COMBINAISON ROUTE MANCHES COURTES FEMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 115.20 }] },
    { name: 'COMBINAISON CHRONO ROUTE MANCHES COURTES HOMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 115.20 }] },
    { name: 'COMBINAISON CHRONO ROUTE MANCHES COURTES FEMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 115.20 }] },
    { name: 'COMBINAISON CHRONO ROUTE MANCHES LONGUES HOMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 120.00 }] },
    { name: 'COMBINAISON CHRONO ROUTE MANCHES LONGUES FEMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 120.00 }] },
    { name: 'COMBINAISON CHRONO PISTE MANCHES COURTES HOMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 111.60 }] },
    { name: 'COMBINAISON CHRONO PISTE MANCHES COURTES FEMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 111.60 }] },
    { name: 'COMBINAISON CHRONO PISTE MANCHES LONGUES HOMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 116.40 }] },
    { name: 'COMBINAISON CHRONO PISTE MANCHES LONGUES FEMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 116.40 }] },
    { name: 'COMBINAISON CYCLO-CROSS MANCHES LONGUES HOMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 120.00 }] },
    { name: 'COMBINAISON CYCLO-CROSSMANCHES LONGUES FEMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 120.00 }] },

    // =========================================================================
    // 6. RUNNING
    // =========================================================================
    { name: 'MAILLOT RUNNING HOMME', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'maillotRunning', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 63.00 }, { quantity: 2, price: 54.00 }, { quantity: 3, price: 45.00 }, { quantity: 5, price: 36.00 }, { quantity: 15, price: 34.20 }, { quantity: 25, price: 33.12 }, { quantity: 50, price: 32.40 }, { quantity: 80, price: 31.32 }, { quantity: 150, price: 30.60 } ] },
    { name: 'MAILLOT RUNNING FEMME', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'maillotRunning', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 63.00 }, { quantity: 2, price: 54.00 }, { quantity: 3, price: 45.00 }, { quantity: 5, price: 36.00 }, { quantity: 15, price: 34.20 }, { quantity: 25, price: 33.12 }, { quantity: 50, price: 32.40 }, { quantity: 80, price: 31.32 }, { quantity: 150, price: 30.60 } ] },
    { name: 'DEBARDEUR ATHLETISME HOMME', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'debardeurAthletisme', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 56.70 }, { quantity: 2, price: 48.60 }, { quantity: 3, price: 40.50 }, { quantity: 5, price: 32.40 }, { quantity: 15, price: 30.78 }, { quantity: 25, price: 29.81 }, { quantity: 50, price: 29.16 }, { quantity: 80, price: 28.19 }, { quantity: 150, price: 27.54 } ] },
    { name: 'DEBARDEUR ATHLETISME FEMME', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'debardeurAthletisme', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 56.70 }, { quantity: 2, price: 48.60 }, { quantity: 3, price: 40.50 }, { quantity: 5, price: 32.40 }, { quantity: 15, price: 30.78 }, { quantity: 25, price: 29.81 }, { quantity: 50, price: 29.16 }, { quantity: 80, price: 28.19 }, { quantity: 150, price: 27.54 } ] },
  { name: 'BRASSIERE RUNNING FEMME', category: 'RUNNING', type: 'haut', subtype: 'Hauts', hasOptions: false, pricingTiers: [ { quantity: 1, price: 63.00 }, { quantity: 2, price: 36.00 }, { quantity: 15, price: 34.20 }, { quantity: 25, price: 33.12 }, { quantity: 50, price: 32.40 }, { quantity: 80, price: 31.32 }, { quantity: 150, price: 30.60 } ] },
    { name: 'MAILLOT RUNNING HIVER HOMME MANCHES LONGUES', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'maillotRunningHiver', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 94.50 }, { quantity: 2, price: 81.00 }, { quantity: 3, price: 67.50 }, { quantity: 5, price: 54.00 }, { quantity: 15, price: 51.30 }, { quantity: 25, price: 49.68 }, { quantity: 50, price: 48.60 }, { quantity: 80, price: 46.98 }, { quantity: 150, price: 45.90 } ] },
    { name: 'MAILLOT RUNNING HIVER FEMME MANCHES LONGUES', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'maillotRunningHiver', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 94.50 }, { quantity: 2, price: 81.00 }, { quantity: 3, price: 67.50 }, { quantity: 5, price: 54.00 }, { quantity: 15, price: 51.30 }, { quantity: 25, price: 49.68 }, { quantity: 50, price: 48.60 }, { quantity: 80, price: 46.98 }, { quantity: 150, price: 45.90 } ] },
    { name: 'GILET COUPE-VENT LEGER MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 72.45 }, { quantity: 2, price: 62.10 }, { quantity: 3, price: 51.75 }, { quantity: 5, price: 41.40 }, { quantity: 15, price: 39.33 }, { quantity: 25, price: 38.09 }, { quantity: 50, price: 37.26 }, { quantity: 80, price: 36.02 }, { quantity: 150, price: 35.19 } ] },
    { name: 'GILET COUPE-VENT MI-SAISON MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 91.35 }, { quantity: 2, price: 78.30 }, { quantity: 3, price: 65.25 }, { quantity: 5, price: 52.20 }, { quantity: 15, price: 49.59 }, { quantity: 25, price: 48.02 }, { quantity: 50, price: 46.98 }, { quantity: 80, price: 45.41 }, { quantity: 150, price: 44.37 } ] },
    { name: 'GILET COUPE-VENT HIVER MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'giletCoupeVent', pricingTiers: [ { quantity: 1, price: 97.65 }, { quantity: 2, price: 83.70 }, { quantity: 3, price: 69.75 }, { quantity: 5, price: 55.80 }, { quantity: 15, price: 53.01 }, { quantity: 25, price: 51.34 }, { quantity: 50, price: 50.22 }, { quantity: 80, price: 48.55 }, { quantity: 150, price: 47.43 } ] },
    { name: 'COUPE-VENT LEGER MIXTE CONFORT', category: 'RUNNING', type: 'haut', subtype: 'Hauts', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 96.60 }, { quantity: 2, price: 82.80 }, { quantity: 3, price: 69.00 }, { quantity: 5, price: 55.20 }, { quantity: 15, price: 52.44 }, { quantity: 25, price: 50.78 }, { quantity: 50, price: 49.68 }, { quantity: 80, price: 48.02 }, { quantity: 150, price: 46.92 } ] },
    { name: 'VESTE MI-SAISON HOMME CONFORT', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'vesteMiSaisonRunning', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },
    { name: 'VESTE MI-SAISON FEMME CONFORT', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'vesteMiSaisonRunning', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },
    { name: 'SHORT RUNNING MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Bas', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 79.80 }, { quantity: 2, price: 68.40 }, { quantity: 3, price: 57.00 }, { quantity: 5, price: 45.60 }, { quantity: 15, price: 43.32 }, { quantity: 25, price: 41.95 }, { quantity: 50, price: 41.04 }, { quantity: 80, price: 39.67 }, { quantity: 150, price: 38.76 } ] },
    { name: 'SHORTY FEMME RUNNING', category: 'RUNNING', type: 'haut', subtype: 'Bas', pricingGroup: 'cuissardShortyRunning', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 84.00 }, { quantity: 2, price: 72.00 }, { quantity: 3, price: 60.00 }, { quantity: 5, price: 48.00 }, { quantity: 15, price: 45.60 }, { quantity: 25, price: 44.16 }, { quantity: 50, price: 43.20 }, { quantity: 80, price: 41.76 }, { quantity: 150, price: 40.80 } ] },
    { name: 'CUISSARD RUNNING HOMME', category: 'RUNNING', type: 'haut', subtype: 'Bas', pricingGroup: 'cuissardShortyRunning', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 84.00 }, { quantity: 2, price: 72.00 }, { quantity: 3, price: 60.00 }, { quantity: 5, price: 48.00 }, { quantity: 15, price: 45.60 }, { quantity: 25, price: 44.16 }, { quantity: 50, price: 43.20 }, { quantity: 80, price: 41.76 }, { quantity: 150, price: 40.80 } ] },
    { name: 'COLLANT RUNNING MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Bas', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 105.00 }, { quantity: 2, price: 90.00 }, { quantity: 3, price: 75.00 }, { quantity: 5, price: 60.00 }, { quantity: 15, price: 57.00 }, { quantity: 25, price: 55.20 }, { quantity: 50, price: 54.00 }, { quantity: 80, price: 52.20 }, { quantity: 150, price: 51.00 } ] },

    // =========================================================================
    // 7. TRIFONCTIONS
    // =========================================================================
    { name: 'TRIFONCTION HOMME COURTE ET MOYENNE DISTANCE Peau TRI GEL', category: 'RUNNING', type: 'haut', subtype: 'Trifonctions', hasOptions: false, pricingGroup: 'trifonctionCourte', pricingTiers: [ { quantity: 1, price: 102.00 }, { quantity: 2, price: 102.00 }, { quantity: 3, price: 102.00 }, { quantity: 5, price: 102.00 }, { quantity: 15, price: 96.90 }, { quantity: 25, price: 93.84 }, { quantity: 50, price: 91.80 }, { quantity: 80, price: 88.74 }, { quantity: 150, price: 86.70 } ] },
    { name: 'TRIFONCTION FEMME COURTE ET MOYENNE DISTANCE Peau TRI GEL', category: 'RUNNING', type: 'haut', subtype: 'Trifonctions', hasOptions: false, pricingGroup: 'trifonctionCourte', pricingTiers: [ { quantity: 1, price: 102.00 }, { quantity: 2, price: 102.00 }, { quantity: 3, price: 102.00 }, { quantity: 5, price: 102.00 }, { quantity: 15, price: 96.90 }, { quantity: 25, price: 93.84 }, { quantity: 50, price: 91.80 }, { quantity: 80, price: 88.74 }, { quantity: 150, price: 86.70 } ] },
    { name: 'TRIFONCTION MIXTE LONGUE DISTANCE Peau TRI GEL, ZIP DEVANT OU DOS', category: 'RUNNING', type: 'haut', subtype: 'Trifonctions', hasOptions: false, pricingGroup: 'trifonctionHalf', pricingTiers: [ { quantity: 1, price: 114.00 }, { quantity: 2, price: 114.00 }, { quantity: 3, price: 114.00 }, { quantity: 5, price: 114.00 }, { quantity: 15, price: 108.30 }, { quantity: 25, price: 104.88 }, { quantity: 50, price: 102.60 }, { quantity: 80, price: 99.18 }, { quantity: 150, price: 96.90 } ] },

    // =========================================================================
    // 8. ACCESSOIRES ET AUTRES
    // =========================================================================
    { name: 'BANDANA ÉTÉ', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 12.00 }, { quantity: 20, price: 10.44 }, { quantity: 50, price: 10.20 } ] },
    { name: 'BANDEAU PERSONNALISÉ', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 9.00 },  { quantity: 20, price: 8.40 }, { quantity: 50, price: 7.20 } ] },
    { name: 'TOUR DE COU PERSONNALISÉ', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 10.20 }, { quantity: 20, price: 8.70 }, { quantity: 50, price: 8.40 } ] },
    { name: 'PASSE MONTAGNE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 18.00 }, { quantity: 20, price: 15.60 }, { quantity: 50, price: 14.40 } ] },
    { name: 'MANCHETTES ETE VELO/RUNNING', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'manchettes', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 15.00 }, { quantity: 20, price: 14.10 }, { quantity: 50, price: 12.90 } ] },
    { name: 'MANCHETTES HIVER VELO/RUNNING', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'manchettes', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 20.40 }, { quantity: 20, price: 18.00 }, { quantity: 50, price: 16.80 } ] },
    { name: 'JAMBIERES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'jambieres', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 26.40 }, { quantity: 20, price: 24.00 }, { quantity: 50, price: 22.80 } ] },
    { name: 'GANTS ÉTÉ', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'gants', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 21.00 }, { quantity: 20, price: 18.00 }, { quantity: 50, price: 16.80 } ] },
    { name: 'GANTS ÉTÉ SLIM', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'gants', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 30.00 }, { quantity: 20, price: 28.20 }, { quantity: 50, price: 25.20 } ] },
    { name: 'TAPIS DE TRANSITION MULTISPORTS', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 10.80 }, { quantity: 20, price: 9.18 }, { quantity: 50, price: 8.40 } ] },
    { name: 'CHAUSSETTES AERO MIXTE 18cm', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'chaussettes', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 21.00 }, { quantity: 20, price: 20.40 }, { quantity: 50, price: 19.20 } ] },
    { name: 'CHAUSSETTES VELO/COURSE A PIED Mixte Tige 13 ou 17cm', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'chaussettes', minQuantity: 50, pricingTiers: [ { quantity: 50, price: 13.08 }, { quantity: 100, price: 11.88 }, { quantity: 200, price: 11.88 } ] },
    { name: 'GAPETTE VELO', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', minQuantity: 50, pricingTiers: [ { quantity: 50, price: 15.00 }, { quantity: 100, price: 13.20 }, { quantity: 200, price: 13.20 } ] },
    { name: 'DOSSARDS JEU DE 1 à 100', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', pricingTiers: [{quantity: 1, price: 68.80}] },
    { name: 'DOSSARDS JEU DE 1 à 150', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', pricingTiers: [{quantity: 1, price: 91.20}] },
    { name: 'DOSSARDS JEU DE 1 à 200', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', pricingTiers: [{quantity: 1, price: 115.20}] },
    { name: 'DOSSARDS JEU DE 1 à 250', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', pricingTiers: [{quantity: 1, price: 136.00}] },
    { name: 'DOSSARDS JEU DE 1 à 300', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', pricingTiers: [{quantity: 1, price: 158.40}] },
    
    // Accessoires Permanents
    { name: 'SOUS-MAILLOT SANS MANCHES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'sousMaillot', price: 40, colors: ["blanc"]},
    { name: 'SOUS-MAILLOT MI-SAISON MANCHES COURTES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'sousMaillot', price: 45, colors: ["blanc"]},
    { name: 'SOUS-MAILLOT HIVER MANCHES LONGUES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'sousMaillotHiver', price: 55, colors: ["blanc"]},
    { name: 'SOUS CASQUE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'unique', price: 18, colors: ["NOIR"]},
    { name: 'CAGOULE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'unique', price: 20, colors: ["NOIR"]},
    { name: 'GANTS HIVER', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'gants', price: 55, colors: ["NOIR"]},
    { name: 'GANTS ETE CONFORT', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'gants', price: 30, colors: ["NOIR", "BLANC", "MARINE", "BRETON PUR BEURRE"]},
    { name: 'GANTS ETE SLIM', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'gants', price: 40, colors: ["NOIR"]},
    { name: 'GANTS MI-SAISON', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'gantsMiSaison', price: 30, colors: ["NOIR"]},
    { name: 'COUVRE-CHAUSSURES AÉRO', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'couvreChaussuresAero', price: 40, colors: ["NOIR"]},
    { name: 'COUVRE-CHAUSSURES HIVER/PLUIE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'couvreChaussuresHiver', price: 65, colors: ["NOIR"]},
    { name: 'BANDEAU', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'bandeau', price: 12, colors: ["ARDENT", "FLUO", "HYPNOTIC", "BRETON PUR BEURRE"]},
    { name: 'TOUR DE COU PERMANENT', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'tourDeCou', price: 15, colors: ["ARDENT", "FLUO", "HYPNOTIC", "BRETON PUR BEURRE"]},
    { name: 'GAPETTE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'unique', price: 20, colors: ["ARDENT", "MAGICREME", "HYPNOTIC", "NOIR"]},
    { name: 'MANCHETTES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'manchettes', price: 33, colors: ["NOIR"]},
    { name: 'GENOUILLERES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'manchettes', price: 33, colors: ["NOIR"]},
    { name: 'JAMBIERES PERMANENTES (NOIR)', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'jambieres', price: 40, colors: ["NOIR"]},
    { name: 'CHAUSSETTES AÉRO', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'chaussettes', price: 30, colors: ["NOIR", "BLANC", "ARDENT", "HYPNOTIC"]},
    { name: 'CHAUSSETTES MI-HAUTES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'chaussettes', price: 17, colors: ["NOIR", "BLANC", "BEIGE", "BRETON PUR BEURRE"]},

    // =========================================================================
    // 9. ENFANTS
    // =========================================================================
    { name: 'MAILLOT ENFANT PERFORMANCE MC', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 42.00 }] },
    { name: 'MAILLOT BMX ENFANT CONFORT ML', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 45.60 }] },
    { name: 'MAILLOT MI-SAISON ENFANT CONFORT ML', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 45.60 }] },
    { name: 'GILET COUPE-VENT LEGER ENFANT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 38.40 }] },
    { name: 'VESTE HIVER ENFANT CONFORT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 84.00 }] },
    { name: 'CUISSARD ENFANT CONFORT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 42.00 }] },
    { name: 'COLLANT HIVER ENFANT SUBLIME CONFORT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 60.00 }] },
    { name: 'COLLANT ENFANT VELOURS ECHAUFFEMENT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 49.80 }] },
    { name: 'MAILLOT RUNNING ENFANT MANCHES COURTES', category: 'ENFANTS', type: 'enfant', subtype: 'Running Enfants', hasOptions: false, pricingTiers: [{ quantity: 1, price: 30.00 }] },
    { name: 'DEBARDEUR ATHLETISME ENFANT', category: 'ENFANTS', type: 'enfant', subtype: 'Running Enfants', hasOptions: false, pricingTiers: [{ quantity: 1, price: 27.00 }] },
    { name: 'CUISSARD RUNNING ENFANT', category: 'ENFANTS', type: 'enfant', subtype: 'Running Enfants', hasOptions: false, pricingTiers: [{ quantity: 1, price: 36.00 }] },
    { name: 'TRIFONCTION ENFANT COURTE ET MOYENNE DISTANCE', category: 'ENFANTS', type: 'enfant', subtype: 'Running Enfants', hasOptions: false, pricingTiers: [ { quantity: 1, price: 90.00 }, { quantity: 2, price: 90.00 }, { quantity: 3, price: 90.00 }, { quantity: 5, price: 90.00 }, { quantity: 15, price: 85.50 }, { quantity: 25, price: 81.23 }, { quantity: 50, price: 77.16 }, { quantity: 80, price: 73.31 }, { quantity: 150, price: 69.64 } ] },

    // =========================================================================
    // 10. OPTIONS TECHNIQUES (TOUJOURS EN DERNIER OU MASQUÉ)
    // =========================================================================
    { name: 'POCHE DOS ZIPPEE', category: 'option', type: 'option', pricingTiers: [ { quantity: 1, price: 11.72 }, { quantity: 2, price: 9.38 }, { quantity: 3, price: 7.50 }, { quantity: 5, price: 6.00 }, { quantity: 15, price: 5.70 }, { quantity: 25, price: 5.52 }, { quantity: 50, price: 5.40 }, { quantity: 80, price: 5.22 }, { quantity: 150, price: 5.10 } ] },
    { name: 'BANDE REFLECTIVE', category: 'option', type: 'option', pricingTiers: [ { quantity: 1, price: 7.08 }, { quantity: 2, price: 5.40 }, { quantity: 3, price: 4.50 }, { quantity: 5, price: 3.60 }, { quantity: 15, price: 3.42 }, { quantity: 25, price: 3.31 }, { quantity: 50, price: 3.24 }, { quantity: 80, price: 3.13 }, { quantity: 150, price: 3.06 }, ] },
    { name: 'Ajustement Longueur +3cm', category: 'option', type: 'option', optionGroup: 'length', fixedPriceTTC: 7.20 },
    { name: 'Ajustement Longueur +6cm', category: 'option', type: 'option', optionGroup: 'length', fixedPriceTTC: 7.20 },
    { name: 'Ajustement Longueur +9cm', category: 'option', type: 'option', optionGroup: 'length', fixedPriceTTC: 7.20 },
    { name: 'Ajustement Longueur -3cm', category: 'option', type: 'option', optionGroup: 'length', fixedPriceTTC: 7.20 },
    { name: 'Ajustement Longueur -6cm', category: 'option', type: 'option', optionGroup: 'length', fixedPriceTTC: 7.20 },
    { name: 'Ajustement Longueur -9cm', category: 'option', type: 'option', optionGroup: 'length', fixedPriceTTC: 7.20 },
    { name: 'Majoration Taille Décalée', category: 'option', type: 'option', fixedPriceTTC: 7.20 },
    { name: 'AJOUT NOM/PRENOM', category: 'option', type: 'option', fixedPriceTTC: 7.20 },
    { name: 'Personnalisation logo club', category: 'option', type: 'option', fixedPriceTTC: 0 },
{ name: 'SUR-MESURE', category: 'option', type: 'option', fixedPriceTTC: 10.00 },

    // =========================================================================
    // 11. BRODELEC
    // =========================================================================
    { name: 'KIT VISIBILITE (Ceinture + 2 Bracelets)', category: 'BRODELEC', type: 'accessoire', subtype: 'Running/Vélo/Marche', supplier: 'BRODELEC', sizeType: 'unique', hasOptions: false, pricingTiers: [{ quantity: 1, price: 20.00 }] },
    { name: 'VESTE REFLECHISSANTE (Silver)', category: 'BRODELEC', type: 'haut', subtype: 'Visibilité', supplier: 'BRODELEC', sizeType: 'hautBrodelec', hasOptions: false, pricingTiers: [{ quantity: 1, price: 70.00 }] },
    { name: 'VESTE LEGERE CROSSLITE', category: 'BRODELEC', type: 'haut', subtype: 'Running/Vélo/Marche', supplier: 'BRODELEC', sizeType: 'hautBrodelec_S_XXL', hasOptions: false, pricingTiers: [{ quantity: 1, price: 51.00 }], colors: ["JAUNE FLUO"] },
    { name: 'GILET COUPE-VENT VISIBILITE', category: 'BRODELEC', type: 'haut', subtype: 'Vélo', supplier: 'BRODELEC', sizeType: 'hautBrodelec_XS_XXL', hasOptions: false, pricingTiers: [{ quantity: 1, price: 40.00 }], colors: ["JAUNE FLUO"] },
    { name: 'GANTS SPORT SOFTSHELL', category: 'BRODELEC', type: 'accessoire', subtype: 'Accessoires', supplier: 'BRODELEC', sizeType: 'gantsBrodelec', hasOptions: false, pricingTiers: [{ quantity: 1, price: 16.00 }], colors: ["NOIR", "GRIS", "JAUNE FLUO"] },
    { name: 'TOUR DE COU REFLECHISSANT', category: 'BRODELEC', type: 'accessoire', subtype: 'Accessoires', supplier: 'BRODELEC', sizeType: 'unique', hasOptions: false, pricingTiers: [{ quantity: 1, price: 7.00 }], colors: ["JAUNE FLUO"] },
    { name: 'BANDEAU SOFTSHELL', category: 'BRODELEC', type: 'accessoire', subtype: 'Accessoires', supplier: 'BRODELEC', sizeType: 'unique', hasOptions: false, pricingTiers: [{ quantity: 1, price: 10.00 }], colors: ["NOIR"] },
    { name: 'CASQUETTE 5 PANNEAUX (Coton)', category: 'BRODELEC', type: 'accessoire', subtype: 'Accessoires', supplier: 'BRODELEC', sizeType: 'unique', hasOptions: false, pricingTiers: [{ quantity: 1, price: 10.00 }], colors: ["BLACK", "CLASSIC RED", "LIGHT GREY", "WHITE", "BOTTLE GREEN", "FRENCH NAVY", "OLIVE GREEN", "BURGUNDY", "GRAPHITE GREY", "ROYAL"] },
    { name: 'CASQUETTE BF15C 5 PANNEAUX BICOLORE', category: 'BRODELEC', type: 'accessoire', subtype: 'Accessoires', supplier: 'BRODELEC', sizeType: 'unique', hasOptions: false, pricingTiers: [{ quantity: 1, price: 10.00 }], colors: ["BLACK/GREY", "BLACK/RED", "GRAPHITE/OYSTER", "ROYAL/WHITE", "BLACK/FUCHSIA", "BLACK/ROYAL", "NAVY/PUTTY", "WHITE/NAVY", "BLACK/LIME", "BLACK/YELLOW", "PUTTY/NAVY", "BLACK/ORANGE", "FRENCH NAVY/CLASSIC RED", "RED/WHITE"] },
    { name: 'CASQUETTE BF171 5 PANNEAUX BICOLORE', category: 'BRODELEC', type: 'accessoire', subtype: 'Accessoires', supplier: 'BRODELEC', sizeType: 'unique', hasOptions: false, pricingTiers: [{ quantity: 1, price: 12.00 }], colors: ["BLACK/BRIGHT ROYAL", "BLACK/GRAPHITE GREY/WHITE", "GRAPHITE/OYSTER", "BOTTLE GREEN/BLACK/WHITE", "FRENCH NAVY/CLASSIC RED/WHITE", "BLACK/CLASSIC RED", "BLACK/LIME GREEN", "BRIGHT ROYAL/BLACK/WHITE", "OLIVE GREEN/BLACK", "BLACK/GRAPHITE GREY/CLASSIC RED", "BLACK/ORANGE", "CLASSIC RED/BLACK/WHITE", "ORANGE/BLACK", "BLACK/GRAPHITE GREY/CLASSIC RED" , "BLACK/YELLOW" , "FRENCH NAVY/BRIGHT ROYAL/WHITE"] },
    { name: 'CASQUETTE SPORT (Tech)', category: 'BRODELEC', type: 'accessoire', subtype: 'Accessoires', supplier: 'BRODELEC', sizeType: 'unique', hasOptions: false, pricingTiers: [{ quantity: 1, price: 10.00 }] }
];

const productSizes = {
    haut: ['XXS', 'XS', 'S', 'M', 'L', 'XL', 'XXL', '3XL', '4XL', '5XL', '6XL'],
    enfant: ['6A', '8A', '10A', '12A', '14A', '16A'],
    aero: ['XXS', 'XS', 'S', 'M', 'L', 'XL', 'XXL', '3XL'],
    ample: ['XXS', 'XS', 'S', 'M', 'L', 'XL', 'XXL', '3XL', '4XL', '5XL', '6XL'],
    largeHaut: ['XXS', 'XS', 'S', 'M', 'L', 'XL', 'XXL', '3XL', '4XL', '5XL', '6XL'],
    manchettes: ["P (Biceps 27/31cm)", "G (Biceps 31/34cm)"],
    jambieres: ["P (Cuisses 39/44cm)", "G (Cuisses 44/50cm)"],
    unique: ["U"],
    gants: ["XXS", "XS", "S", "M", "L", "XL", "XXL"],
    chaussettes: ["S/M (35/40)", "L/XL (41/46)"],
    sousMaillot: ["2XS/XS", "S/M", "L/XL", "2XL/3XL"],
    sousMaillotHiver: ["S", "M", "L", "XL"],
    gantsMiSaison: ["S", "M", "L", "XL"],
    couvreChaussuresAero: ["36/38", "39/41", "42/44", "45/47"],
    couvreChaussuresHiver: ["38/39", "40/42", "43/44", "45/46", "47/48"],
    bandeau: ["XXS", "XS", "S", "M", "L", "XL", "XXL"],
    tourDeCou: ["XXS", "XS", "S", "M", "L", "XL", "XXL"],

    // ▼▼▼ LES TAILLES SPÉCIFIQUES BRODELEC ▼▼▼
    gantsBrodelec: ["S/M", "L/XL"],
    hautBrodelec: ["XS", "S", "M", "L", "XL", "XXL", "3XL"], // Pour la Veste Réflechissante
    hautBrodelec_S_XXL: ["S", "M", "L", "XL", "XXL"],       // Pour la Veste Crosslite
    hautBrodelec_XS_XXL: ["XS", "S", "M", "L", "XL", "XXL"]  // Pour le Gilet
    // ▲▲▲ -------------------------------- ▲▲▲
};
const TVA_RATE = 0.20;
const DOWN_PAYMENT_RATE = 0.30;
const GRAPHIC_FEE_TTC = 150;
const ADMIN_PASSWORD = "582069Whim#";

// --- PERFORMANCE: Create a Map for quick product lookups ---
const productMap = new Map(allAvailableProducts.map(p => [p.name, p]));
const refreshProductMap = () => {
    productMap.clear();
    allAvailableProducts.forEach(p => productMap.set(p.name, p));
};
// =================================================================================
// --- APPLICATION STATE ---
// =================================================================================
let state = {
    isLoadedFromBackup: false, // <--- AJOUTER CETTE LIGNE
activeCategory: null,
documentType: 'commande',
    isReassort: false,
    isCustomCreation: false,
    isStoreOrder: false,
    applyDiscount: false,
    clubName: '',
    departement: '',
    clientNumber: '',
    orderDate: new Date().toISOString().split('T')[0],
    lastDeliveryDate: '',
    licencieName: '',
    activeLicensee: '',
    licenseeList: [],
licenseeEmails: {},
    licenseeDeposits: {},
    clubDiscount: 0,
    currentOrderLineItems: [],
    discountType: 'global',
    orderScope: '',
    orderSpecificity: '',
    currentProduct: '',
    currentQuantities: {},
    currentCalculatedUnitPrice: 0,
    manualUnitPrice: '',
    currentSelectedOptions: [],
    currentSpecificity: '',
    currentAccessoryQuantity: '',
currentMarking: '',
    currentColor: '',
    clubProductRange: [], 
    showAllProducts: false, 
    clubStock: {},
    isAddingForStock: false,
    clubVisuals: [],
    currentVisual: '',
    preOrderNumber: '',
    factoryDepartureDate: '',
    deliveryAddress: '',
    deliveryContact: '',
deliveryEmail: '',
    clubPacks: [],              
    addMode: 'article',         
    currentPackSelection: '',   
    currentPackItemConfigs: {}, 
 importedRestockPrices: {},
    applyClubParticipation: false,
    clubParticipationAmount: 0,
    packBaseQuantity: 0, 
    licenseeBackup: null,
validatedLicensees: [], // On remet la liste à zéro
savedPolls: [], // Stockage des campagnes de sondage
};
// =================================================================================
// --- DOM ELEMENT REFERENCES ---
// =================================================================================
const dom = {
    mainAppView: document.getElementById('main-app-view'),
    mainModal: document.getElementById('main-modal'),
    mainModalTitle: document.getElementById('main-modal-title'),
    mainModalBody: document.getElementById('main-modal-body'),
    mainModalActions: document.getElementById('main-modal-actions'),
    mainModalCloseBtn: document.getElementById('main-modal-close-btn'),
    toastContainer: document.getElementById('toast-container'),
    mainTitle: document.getElementById('main-title'),
    summaryTotalHauts: document.getElementById('summary-total-hauts'),
    summaryTotalBas: document.getElementById('summary-total-bas'),
    summaryTotalAccessoires: document.getElementById('summary-total-accessoires'),
    summaryTotalLicensees: document.getElementById('summary-total-licensees'),
    summaryTotalStock: document.getElementById('summary-total-stock'),
    docTypeReassortCheck: document.getElementById('doc-type-reassort'),
    customCreationCheck: document.getElementById('custom-creation-check'),
    orderScopeContainer: document.getElementById('order-scope-container'),
    scopeGlobalRadio: document.getElementById('scope-global'),
    scopeLicenseeRadio: document.getElementById('scope-licensee'),
    storeOrderCheck: document.getElementById('store-order-check'),
    applyDiscountCheck: document.getElementById('apply-discount-check'),
    clubName: document.getElementById('clubName'),
    departement: document.getElementById('departement'),
    clientNumber: document.getElementById('clientNumber'),
    orderDate: document.getElementById('orderDate'),
    orderSpecificity: document.getElementById('orderSpecificity'),
    licencieNameContainer: document.getElementById('licencieName-container'),
    licencieNameInput: document.getElementById('licencieName'),
    licenseeDatalist: document.getElementById('licensee-datalist'),
    activeLicenseeBanner: document.getElementById('active-licensee-banner'),
    bannerLicenseeName: document.getElementById('banner-licensee-name'),
    clearActiveLicenseeBtn: document.getElementById('clear-active-licensee-btn'),
licenseeRecapBtn: document.getElementById('licensee-recap-btn'),
    manageLicenseesBtn: document.getElementById('manage-licensees-btn'),
    nextLicenseeBtn: document.getElementById('next-licensee-btn'),
    productTabsContainer: document.getElementById('product-tabs-container'),
    productFormContainer: document.getElementById('product-form-container'),
    orderTableBody: document.getElementById('order-table-body'),
    orderTableHead: document.getElementById('order-table-head'),
    totalArticlesDisplay: document.getElementById('total-articles-display'),
    discountControlsContainer: document.getElementById('discount-controls-container'),
    discountGlobalRadio: document.getElementById('discount-global'),
    discountItemRadio: document.getElementById('discount-item'),
    clubDiscount: document.getElementById('clubDiscount'),
    subtotalHT: document.getElementById('subtotal-ht'),
    subtotalTTC: document.getElementById('subtotal-ttc'),
    discountAmountHT: document.getElementById('discount-amount-ht'),
    discountAmountTTC: document.getElementById('discount-amount-ttc'),
    shippingHT: document.getElementById('shipping-ht'),
    shippingTTC: document.getElementById('shipping-ttc'),
    graphicFeeContainer: document.getElementById('graphic-fee-container'),
    graphicFeeHT: document.getElementById('graphic-fee-ht'),
    graphicFeeTTC: document.getElementById('graphic-fee-ttc'),
    grandTotalHT: document.getElementById('grand-total-ht'),
    grandTotalTTC: document.getElementById('grand-total-ttc'),
    downPaymentContainer: document.getElementById('down-payment-container'),
    downPayment: document.getElementById('down-payment'),
    newOrderBtn: document.getElementById('new-order-btn'),
    saveOrderBtn: document.getElementById('save-order-btn'),
    loadOrderInput: document.getElementById('load-order-input'),
    importLicenseesInput: document.getElementById('import-licensees-input'),
    importStockInput: document.getElementById('import-stock-input'),
    validateOrderBtn: document.getElementById('validate-order-btn'),
    manageStockBtn: document.getElementById('manage-stock-btn'),
editorModeBtn: document.getElementById('editor-mode-btn'),
saveHtmlAppBtn: document.getElementById('save-html-app-btn'),
initStockBtn: document.getElementById('init-stock-btn'),
manageClubRangeBtn: document.getElementById('manage-club-range-btn'), // NOUVEAU
    toggleProductsViewContainer: document.getElementById('toggle-products-view-container'), // NOUVEAU
    toggleProductsBtn: document.getElementById('toggle-products-btn'), // NOUVEAU
exportLabelsBtn: document.getElementById('export-labels-btn'),
    autosaveStatus: document.getElementById('autosave-status'),
showStatsBtn: document.getElementById('show-stats-btn'),
};
// --- RENDRE DOM ACCESSIBLE AU HTML ---
window.dom = dom;
// =================================================================================
// --- HELPER & UTILITY FUNCTIONS ---
// =================================================================================

const sanitizeForId = (text) => {
    if (!text) return '';
    return text.replace(/[^a-zA-Z0-9]/g, '_');
};

const scrollToLicensee = (licenseeName) => {
    if (!licenseeName) return;
    const sanitizedName = sanitizeForId(licenseeName);
    const targetElement = document.getElementById(`licencie-header-${sanitizedName}`);
    if (targetElement) {
        targetElement.scrollIntoView({ behavior: 'smooth', block: 'center' });
    }
};

let clientDatabase = [];

const saveClientInfo = () => {
    const clubName = dom.clubName.value.trim();
    const clientNumber = dom.clientNumber.value.trim();
    const departement = dom.departement.value.trim();

    if (clubName) {
        let clientFound = false;
        clientDatabase.forEach(client => {
            if (client.clubName === clubName) {
                client.clientNumber = clientNumber;
                client.departement = departement;
                clientFound = true;
            }
        });

        if (!clientFound) {
            clientDatabase.push({ clubName, clientNumber, departement });
        }

        localStorage.setItem('clientDatabase', JSON.stringify(clientDatabase));
        updateDatalists();
    }
};
const updateSectionHighlights = () => {
    const allSections = document.querySelectorAll('#info-section, #add-article-section, #summary-and-actions-section, #licencieName-container, #product-selection-step, #size-selection-step, #add-button-step, #order-scope-container');

    allSections.forEach(el => el.classList.remove('highlight-section'));

    // Logique de surbrillance mise à jour
    if (!state.clubName.trim() || !state.departement.trim()) {
        document.getElementById('info-section')?.classList.add('highlight-section');
    } else if (!state.orderScope) { // NOUVELLE CONDITION
        document.getElementById('order-scope-container')?.classList.add('highlight-section');
    } else if (state.orderScope === 'licensee' && !state.activeLicensee) {
        document.getElementById('licencieName-container')?.classList.add('highlight-section');
    } else if (!state.currentProduct) {
        document.getElementById('product-selection-step')?.classList.add('highlight-section');
    } else if (Object.values(state.currentQuantities).reduce((s, q) => s + (parseInt(q, 10) || 0), 0) === 0 && !state.currentAccessoryQuantity) {
        document.getElementById('size-selection-step')?.classList.add('highlight-section');
    } else if (state.currentProduct) {
        document.getElementById('add-button-step')?.classList.add('highlight-section');
    } else {
        document.getElementById('summary-and-actions-section')?.classList.add('highlight-section');
    }
};const updateDatalists = () => {
    const clubList = document.getElementById('club-list');
    const clientList = document.getElementById('client-list');
    if(clubList) clubList.innerHTML = clientDatabase.map(c => `<option value="${c.clubName}"></option>`).join('');
    if(clientList) clientList.innerHTML = clientDatabase.map(c => `<option value="${c.clientNumber}"></option>`).join('');
};

const showToast = (message, type = 'success', action = null) => {
    const toast = document.createElement('div');
    const bgColor = type === 'success' ? 'bg-green-500' : (type === 'info' ? 'bg-blue-500' : 'bg-red-500');
    toast.className = `toast ${bgColor} text-white p-3 rounded-lg shadow-lg mb-2 flex justify-between items-center gap-4`;
    
    const textSpan = document.createElement('span');
    textSpan.textContent = message;
    toast.appendChild(textSpan);

    if (action) {
        const btn = document.createElement('button');
        btn.textContent = action.label;
        btn.className = "bg-white text-gray-800 text-xs font-bold px-3 py-1.5 rounded shadow hover:bg-gray-100 transition transform active:scale-95";
        btn.onclick = (e) => {
            e.stopPropagation();
            action.onClick();
            toast.remove();
        };
        toast.appendChild(btn);
    }

    dom.toastContainer.appendChild(toast);
    
    requestAnimationFrame(() => {
        toast.classList.add('show');
    });

    const duration = action ? 6000 : 3000;
    
    setTimeout(() => {
        if (toast.parentNode) {
            toast.classList.remove('show');
            toast.addEventListener('transitionend', () => toast.remove());
        }
    }, duration);
};

const showModal = (modalElement, title, content, actions = [], maxWidth = 'max-w-md', onOpen = null) => {
    const modalDialog = modalElement.querySelector('div');
    modalDialog.classList.remove('max-w-md', 'max-w-lg', 'max-w-xl', 'max-w-2xl', 'max-w-4xl');
    modalDialog.classList.add(maxWidth);
    dom.mainModalTitle.textContent = title;
    dom.mainModalBody.innerHTML = '';
    dom.mainModalBody.appendChild(content);
    dom.mainModalActions.innerHTML = '';
    if (actions.length > 0) {
        actions.forEach(action => {
            const button = document.createElement('button');
            button.textContent = action.label;
            button.className = `${action.className || 'bg-indigo-600 hover:bg-indigo-700 text-white'} font-bold py-2 px-4 rounded-lg`;
            button.onclick = action.onClick;
            dom.mainModalActions.appendChild(button);
        });
    } else {
        const closeButton = document.createElement('button');
        closeButton.textContent = 'Fermer';
        closeButton.className = 'bg-gray-500 hover:bg-gray-700 text-white font-bold py-2 px-4 rounded-lg';
        closeButton.onclick = () => hideModal(modalElement);
        dom.mainModalActions.appendChild(closeButton);
    }
    modalElement.classList.add('is-open');

    // On exécute une fonction si elle est fournie après l'ouverture
    if (onOpen && typeof onOpen === 'function') {
        onOpen();
    }
};

const hideModal = (modalElement) => {
    modalElement.classList.remove('is-open');
};
// --- RENDRE LA FONCTION ACCESSIBLE AU HTML ---
window.hideModal = hideModal;
const getSortedSizesText = (item) => {
    if (item.quantitiesPerSize['Devis']) {
        return `Quantité globale`;
    }
    const product = productMap.get(item.productName);
    const defaultText = Object.entries(item.quantitiesPerSize).map(([s, q]) => `${s}: ${q}`).join(', ');
    if (!product) return defaultText;
    const sizeOrder = productSizes[product.sizeType || product.type] || [];
    if (sizeOrder.length === 0) return defaultText;
    const sortedQuantities = Object.entries(item.quantitiesPerSize)
        .filter(([, qty]) => (parseInt(qty, 10) || 0) > 0)
        .sort(([sizeA], [sizeB]) => {
            const indexA = sizeOrder.indexOf(sizeA);
            const indexB = sizeOrder.indexOf(sizeB);
            if (indexA === -1 && indexB === -1) return sizeA.localeCompare(sizeB);
            if (indexA === -1) return 1;
            if (indexB === -1) return -1;
            return indexA - indexB;
        });
    return sortedQuantities.map(([size, qty]) => `${size}: ${qty}`).join(', ');
};
// ▼▼▼ AJOUTEZ CETTE NOUVELLE FONCTION ▼▼▼
const unrollPacksForDistribution = (lineItems) => {
    const unrolledList = [];
    lineItems.forEach(item => {
        if (item.type === 'pack') {
            item.packItems.forEach(packItem => {
                // On crée un "faux" article de commande pour chaque pièce du pack
                unrolledList.push({
                    ...packItem, // Contient productName, size, visual, options
                    licencieName: item.licencieName,
                    totalQuantity: 1,
                    quantitiesPerSize: { [packItem.size]: 1 },
                });
            });
        } else {
            // Si ce n'est pas un pack, on l'ajoute tel quel
            unrolledList.push(item);
        }
    });
    return unrolledList;
};

// =================================================================================
// --- CALCULATION LOGIC ---
// =================================================================================

const getPriceForQuantity = (pricingTiers, totalQuantity) => {
    if (!pricingTiers || pricingTiers.length === 0) return 0;
    let applicableTier = pricingTiers[0];
    for (let i = pricingTiers.length - 1; i >= 0; i--) {
        if (totalQuantity >= pricingTiers[i].quantity) {
            applicableTier = pricingTiers[i];
            break;
        }
    }
    return applicableTier.price;
};

const getUnitPriceTTC = (productName, totalPricingQuantity, selectedOptions) => {
    const product = productMap.get(productName);
    // Add safety check for product existence
    if (!product) {
         console.error(`getUnitPriceTTC: Product not found - ${productName}`); // Log error if product missing
         return 0;
    }

    // Ensure selectedOptions is always an array
    const optionsArray = Array.isArray(selectedOptions) ? selectedOptions : [];

    // Calculate base price
    const basePrice = product.price ? product.price : getPriceForQuantity(product.pricingTiers, totalPricingQuantity);

    // Calculate options price safely
    const optionsPrice = optionsArray.reduce((total, optionName) => {
        const optionProduct = productMap.get(optionName);
        if (!optionProduct) return total;
        if (optionProduct.fixedPriceTTC) return total + optionProduct.fixedPriceTTC;
        // Ensure pricingTiers exists before calling getPriceForQuantity for options
        if (optionProduct.pricingTiers && Array.isArray(optionProduct.pricingTiers)) {
             return total + getPriceForQuantity(optionProduct.pricingTiers, totalPricingQuantity);
        }
        return total;
    }, 0);

    const finalPrice = basePrice + optionsPrice;

    // *** AJOUT DU LOG ICI ***
    console.log(` -> getUnitPriceTTC for "${productName}" (qty: ${totalPricingQuantity}, options: ${JSON.stringify(optionsArray)}) calculated Base: ${basePrice}, Options: ${optionsPrice}, Final TTC: ${finalPrice}`);

    return finalPrice;
};
const refreshExistingCartDiscounts = () => {
    if (state.currentOrderLineItems.length === 0) return;

    state.currentOrderLineItems.forEach(item => {
        const rangeItem = state.clubProductRange.find(r => r.name === item.productName);

        if (state.applyDiscount && state.discountType === 'item') {
            if (rangeItem && rangeItem.allowDiscount) {
                item.specificDiscountRate = state.clubDiscount; 
            } else {
                item.specificDiscountRate = undefined; 
            }
        } 
        else if (state.applyDiscount && state.discountType === 'global') {
            item.specificDiscountRate = undefined; 
        }
    });
    renderAll();
};// --- CORRIGÉ ---
const calculateAllTotals = () => {
    // 1. PRÉPARATION DU TRI
    const productRankMap = new Map(allAvailableProducts.map((p, index) => [p.name, index]));

    state.currentOrderLineItems.sort((a, b) => {
        const rankA = productRankMap.has(a.productName) ? productRankMap.get(a.productName) : 9999;
        const rankB = productRankMap.has(b.productName) ? productRankMap.get(b.productName) : 9999;
        if (rankA !== rankB) return rankA - rankB;
        if (a.isStockOrder !== b.isStockOrder) return a.isStockOrder ? 1 : -1;
        const nameA = a.licencieName || '';
        const nameB = b.licencieName || '';
        return nameA.localeCompare(nameB);
    });

    // 2. Calcul des quantités TOTALES PAR PRODUIT (Pour les paliers de prix)
    const groupTotals = {};
    const productGlobalTotals = {};
 
    // Prise en compte du Réassort (Quantité de référence)
    if (state.isReassort) {
        state.clubProductRange.forEach(rangeItem => {
            if (rangeItem.lastRestockRefQty && rangeItem.lastRestockRefQty > 0) {
                const product = productMap.get(rangeItem.name);
                if (product) {
                    if (product.pricingGroup) {
                        groupTotals[product.pricingGroup] = (groupTotals[product.pricingGroup] || 0) + rangeItem.lastRestockRefQty;
                    }
                    // On ajoute aussi au total global du produit
                    productGlobalTotals[rangeItem.name] = (productGlobalTotals[rangeItem.name] || 0) + rangeItem.lastRestockRefQty;
                }
            }
        });
    }
    
    // Prise en compte des Packs (Base quantité)
    if (state.packBaseQuantity > 0) {
        state.clubPacks.forEach(packDefinition => {
            packDefinition.productNames.forEach(productName => {
                const product = productMap.get(productName);
                if (product) {
                    if (product.pricingGroup) groupTotals[product.pricingGroup] = (groupTotals[product.pricingGroup] || 0) + state.packBaseQuantity;
                    productGlobalTotals[productName] = (productGlobalTotals[productName] || 0) + state.packBaseQuantity;
                }
            });
        });
    }
    
    // Compte des articles dans le panier actuel
    state.currentOrderLineItems.forEach(item => {
        if (item.type === 'pack') {
            (item.packItems || []).forEach(packItem => {
                const product = productMap.get(packItem.productName);
                if (product) {
                    const qty = packItem.quantity || 1;
                    if (product.pricingGroup) groupTotals[product.pricingGroup] = (groupTotals[product.pricingGroup] || 0) + qty;
                    productGlobalTotals[packItem.productName] = (productGlobalTotals[packItem.productName] || 0) + qty;
                }
            });
        } else {
            const product = productMap.get(item.productName);
            if (product) {
                const qty = item.totalQuantity;
                if (product.pricingGroup) groupTotals[product.pricingGroup] = (groupTotals[product.pricingGroup] || 0) + qty;
                // C'est ici la clé : on cumule par NOM de produit
                productGlobalTotals[item.productName] = (productGlobalTotals[item.productName] || 0) + qty;
            }
        }
    });

    // 3. Calcul des prix unitaires
    let originalSubtotalHT = 0;
    let originalSubtotalTTC = 0;
    let shippingBaseHT = 0; 

    const updatedLineItems = state.currentOrderLineItems.map(item => {
        let finalUnitPriceTTC = 0;
        let finalUnitPriceHT = 0;
        let totalPriceTTC = 0;
        let totalPriceHT = 0;

        if (item.type === 'pack') {
            finalUnitPriceTTC = item.unitPriceTTC || item.totalPriceTTC || 0; 
            totalPriceTTC = finalUnitPriceTTC * item.totalQuantity; 
            finalUnitPriceHT = finalUnitPriceTTC / (1 + TVA_RATE); 
            totalPriceHT = totalPriceTTC / (1 + TVA_RATE);
            
            let realPackPriceTTC = 0;
            if (item.packItems && Array.isArray(item.packItems)) {
                item.packItems.forEach(packItem => {
                    const product = productMap.get(packItem.productName);
                    if (product) {
                        // CORRECTION ICI AUSSI : On utilise le total global si pas de groupe
                        const pricingQuantity = product.pricingGroup ? (groupTotals[product.pricingGroup] || 0) : (productGlobalTotals[packItem.productName] || 0);
                        const itemOptions = packItem.options || [];
                        const itemRealPriceTTC = getUnitPriceTTC(packItem.productName, pricingQuantity, itemOptions);
                        realPackPriceTTC += itemRealPriceTTC * (packItem.quantity || 1);
                    }
                });
            }
            item.realPriceTTC = realPackPriceTTC;
            item.totalPriceHT = totalPriceHT;
            item.totalPriceTTC = totalPriceTTC;

        } else {
            const product = productMap.get(item.productName);
            if (product) {
                const rangeItem = state.clubProductRange.find(r => r.name === item.productName);
                const quantityToCheck = productGlobalTotals[item.productName] || item.totalQuantity;
                
                const hasSpecificDiscount = item.specificDiscountRate !== undefined && item.specificDiscountRate >= 0;

                // PRIORITÉ 1 : Cadeau 0€
                if (item.isManualPrice && item.initialManualPrice === 0) {
                    finalUnitPriceTTC = 0;
                } 
                // PRIORITÉ 2 : PRIX ADMIN / MANUEL
                else if (item.isManualPrice && typeof item.initialManualPrice === 'number') {
                    finalUnitPriceTTC = item.initialManualPrice;
                } 
                // PRIORITÉ 3 : REMISE SPÉCIFIQUE
                else if (hasSpecificDiscount) {
                    // CORRECTION MAJEURE ICI : On utilise productGlobalTotals si pas de groupe
                    const pricingQuantity = product.pricingGroup ? (groupTotals[product.pricingGroup] || 0) : (productGlobalTotals[item.productName] || 0);
                    finalUnitPriceTTC = getUnitPriceTTC(item.productName, pricingQuantity, item.options || []);
                }
                // PRIORITÉ 4 : Prix Réassort mémorisé
                else if (state.isReassort && rangeItem && rangeItem.lastRestockPrice && quantityToCheck >= 2) {
                    finalUnitPriceTTC = rangeItem.lastRestockPrice;
                } 
                // PRIORITÉ 5 : PRIX FIXE GAMME
                else if (rangeItem && typeof rangeItem.licenseePrice === 'number' && rangeItem.licenseePrice >= 0) {
                     finalUnitPriceTTC = rangeItem.licenseePrice;
                }
                // PRIORITÉ 6 : Prix Standard Calculé
                else {
                    // CORRECTION MAJEURE ICI : On utilise productGlobalTotals si pas de groupe
                    // Avant : ... : item.totalQuantity (ce qui ne prenait que la ligne)
                    // Après : ... : productGlobalTotals[item.productName] (ce qui prend TOUT le tableau)
                    const pricingQuantity = product.pricingGroup ? (groupTotals[product.pricingGroup] || 0) : (productGlobalTotals[item.productName] || 0);
                    finalUnitPriceTTC = getUnitPriceTTC(item.productName, pricingQuantity, item.options || []);
                }

                if (item.isFromStock && item.licencieName === 'Commande Globale') {
                    finalUnitPriceTTC = 0;
                }

                finalUnitPriceHT = finalUnitPriceTTC / (1 + TVA_RATE);
                totalPriceTTC = finalUnitPriceTTC * item.totalQuantity;
                totalPriceHT = totalPriceTTC / (1 + TVA_RATE);
            }
            item.realPriceTTC = 0;
        }

        originalSubtotalHT += totalPriceHT;
        originalSubtotalTTC += totalPriceTTC;

        const product = productMap.get(item.productName);
        const isBrodelec = product && product.supplier === 'BRODELEC';
        
        if (!isBrodelec) {
            shippingBaseHT += totalPriceHT;
        }

        return { ...item, unitPriceTTC: finalUnitPriceTTC, unitPriceHT: finalUnitPriceHT, totalPriceTTC, totalPriceHT, realPriceTTC: item.realPriceTTC };
    });

    state.currentOrderLineItems = updatedLineItems;

    // 4. Calcul Remises
    let totalDiscountAmountHT = 0;
    
    updatedLineItems.forEach(item => {
        let itemDiscountRate = 0; 
        const rangeItem = state.clubProductRange.find(rItem => rItem.name === item.productName);
        
        const hasFixedLicenseePrice = item.type !== 'pack' && rangeItem && typeof rangeItem.licenseePrice === 'number' && rangeItem.licenseePrice >= 0;
        
        if (typeof item.specificDiscountRate === 'number' && item.specificDiscountRate >= 0) {
            itemDiscountRate = item.specificDiscountRate;
        } 
        else {
             const isRestockPriceApplied = state.isReassort && rangeItem && rangeItem.lastRestockPrice && !item.isManualPrice;
             if (state.applyDiscount && !hasFixedLicenseePrice && !isRestockPriceApplied) { 
                 if (state.discountType === 'global') {
                    itemDiscountRate = state.clubDiscount;
                }
             }
        }
        
        item.appliedDiscountRate = itemDiscountRate;

        if (itemDiscountRate > 0) {
            const discountFactor = 1 - (itemDiscountRate / 100);
            const priceBeforeDiscountHT = item.totalPriceHT || 0;
            const discountAmountHT = priceBeforeDiscountHT * (itemDiscountRate / 100);
            totalDiscountAmountHT += discountAmountHT;

            item.unitPriceTTC = item.unitPriceTTC * discountFactor;
            item.unitPriceHT = item.unitPriceHT * discountFactor;
            item.totalPriceTTC = item.totalPriceTTC * discountFactor;
            item.totalPriceHT = item.totalPriceHT * discountFactor;
        }
    });

    const discountAmountHT = totalDiscountAmountHT; 
    const discountAmountTTC = discountAmountHT * (1 + TVA_RATE);
    
    // 5. Calcul Frais de Port & Graphiques
    let shippingHT = 0;
    if (shippingBaseHT > 2000) shippingHT = 0;
    else if (shippingBaseHT > 1000) shippingHT = 14;
    else if (shippingBaseHT > 500) shippingHT = 12;
    else if (shippingBaseHT > 0) shippingHT = 9.50;
    const shippingTTC = shippingHT * (1 + TVA_RATE);

    const totalNonAccessoryQty = updatedLineItems.reduce((sum, item) => {
        let qtyToAdd = 0;
        const prod = productMap.get(item.productName);
        if (item.type === 'pack') {
            qtyToAdd = (item.packItems || []).reduce((packSum, packItem) => {
                const p = productMap.get(packItem.productName);
                return (p && p.type !== 'accessoire' && p.supplier !== 'BRODELEC') ? packSum + (packItem.quantity || 1) : packSum;
            }, 0);
        } else {
            if (prod && prod.type !== 'accessoire' && prod.supplier !== 'BRODELEC') {
                qtyToAdd = item.totalQuantity || 0;
            }
        }
        return sum + qtyToAdd;
    }, 0);

    const applyGraphicFee = state.isCustomCreation && totalNonAccessoryQty < 20;
    const graphicFeeTTC = applyGraphicFee ? GRAPHIC_FEE_TTC : 0;
    const graphicFeeHT = graphicFeeTTC / (1 + TVA_RATE);

    const grandTotalHT = originalSubtotalHT - discountAmountHT + shippingHT + graphicFeeHT;
    const grandTotalTTC = originalSubtotalTTC - discountAmountTTC + shippingTTC + graphicFeeTTC;
    const downPayment = grandTotalTTC * DOWN_PAYMENT_RATE;

    return {
        subtotalHT: originalSubtotalHT,
        subtotalTTC: originalSubtotalTTC,
        discountAmountHT, 
        discountAmountTTC, 
        shippingHT,
        shippingTTC,
        graphicFeeHT,
        graphicFeeTTC,
        grandTotalHT,
        grandTotalTTC,
        downPayment
    };
};// =================================================================================
// --- UI RENDER FUNCTIONS ---
// =================================================================================

const renderAll = () => {
    // 1. Calculs
    const totals = calculateAllTotals();

    // 2. Gestion Dynamique du Titre
    const titles = Array.from(document.querySelectorAll('h2'));
    const detailsTitle = titles.find(el => el.textContent.includes('Détails de la commande') || el.textContent.includes('Remise appliquée'));
    
    if (detailsTitle) {
        let baseTitle = 'Détails de la commande';
        if (state.applyDiscount && state.clubDiscount > 0) {
            detailsTitle.innerHTML = `${baseTitle} <span class="ml-3 inline-flex items-center px-3 py-1 rounded-full text-sm font-bold bg-green-100 text-green-800 border border-green-200 shadow-sm">✅ Remise appliquée : -${state.clubDiscount}%</span>`;
        } else {
            detailsTitle.textContent = baseTitle;
        }
    }

    // 3. Mise à jour de l'en-tête du tableau (AVEC LES "U.")
    if (dom.orderTableHead) {
        dom.orderTableHead.innerHTML = `
            <tr class="bg-gray-50 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider border-b border-gray-200">
                <th class="px-2 py-3">Produit</th>
                <th class="px-2 py-3 text-center">Qté</th>
                <th class="px-2 py-3 text-right">Prix U.<br>HT</th>
                
                <th class="px-2 py-3 text-right">Prix U. Public<br>TTC</th>
                <th class="px-2 py-3 text-right text-red-600">Prix U. Club<br>TTC</th>
                
                <th class="px-2 py-3 text-right">Total<br>HT</th>
                <th class="px-2 py-3 text-right">Total<br>TTC</th>
                <th class="px-2 py-3 text-center sticky right-0 bg-gray-50 shadow-[-5px_0_5px_-5px_rgba(0,0,0,0.1)]">Actions</th>
            </tr>
        `;
    }

    // 4. Rendu Standard
    renderUIState();
    renderTotals(); 
const formContainer = document.getElementById('add-article-section');
    const existingBanner = document.getElementById('readonly-banner');
    if (existingBanner) existingBanner.remove();

    if (state.isReadOnly) {
        // 1. Masquer le formulaire d'ajout
        if (formContainer) formContainer.classList.add('hidden');
        
        // 2. Afficher le bandeau d'alerte
        const banner = document.createElement('div');
        banner.id = 'readonly-banner';
        banner.className = "bg-orange-100 border-l-4 border-orange-500 text-orange-700 p-4 mb-6 rounded shadow-sm flex justify-between items-center";
        banner.innerHTML = `
            <div>
                <p class="font-bold">📂 MODE CONSULTATION D'ARCHIVE</p>
                <p class="text-sm">Vous consultez un fichier importé. La modification est bloquée pour protéger les données.</p>
            </div>
            <button onclick="location.reload()" class="bg-orange-600 text-white px-4 py-2 rounded text-sm font-bold hover:bg-orange-700">Fermer / Retour</button>
        `;
        const mainContainer = document.querySelector('main');
        if (mainContainer) mainContainer.insertBefore(banner, mainContainer.firstChild);

        // 3. Masquer le bouton de validation finale (inutile en archive)
        if (dom.validateOrderBtn) dom.validateOrderBtn.classList.add('hidden');

    } else {
        // Mode normal
        if (formContainer) formContainer.classList.remove('hidden');
        if (dom.validateOrderBtn) dom.validateOrderBtn.classList.remove('hidden');
    }
    renderDashboard();
    renderQuantityDashboard();
    renderOrderTable();
    renderProductForm();
    renderLicenseeDatalist();
    updateButtonStates();
    updateSectionHighlights();
};
const renderUIState = () => {
    // 1. Identité Club
    dom.clubName.value = state.clubName || '';
    dom.departement.value = state.departement || '';
    dom.clientNumber.value = state.clientNumber || '';
    
    // 2. Infos Commande (C'est souvent celles-ci qui manquent)
    if(document.getElementById('preOrderNumber')) document.getElementById('preOrderNumber').value = state.preOrderNumber || '';
    if(document.getElementById('factoryDepartureDate')) document.getElementById('factoryDepartureDate').value = state.factoryDepartureDate || '';
    
    // 3. Livraison
    if(document.getElementById('deliveryContact')) document.getElementById('deliveryContact').value = state.deliveryContact || '';
    if(document.getElementById('deliveryEmail')) document.getElementById('deliveryEmail').value = state.deliveryEmail || '';
    if(document.getElementById('deliveryAddress')) document.getElementById('deliveryAddress').value = state.deliveryAddress || '';
dom.orderDate.value = state.orderDate;
    dom.orderSpecificity.value = state.orderSpecificity;
    document.getElementById('preOrderNumber').value = state.preOrderNumber;
    document.getElementById('factoryDepartureDate').value = state.factoryDepartureDate;
    document.getElementById('deliveryAddress').value = state.deliveryAddress;
    document.getElementById('deliveryContact').value = state.deliveryContact;
    document.getElementById('deliveryEmail').value = state.deliveryEmail;

    if (state.isReassort) {
        dom.mainTitle.textContent = 'PRÉVISIONNEL DE COMMANDE - RÉASSORT 2 MOIS';
    } else {
        dom.mainTitle.textContent = 'PRÉVISIONNEL DE COMMANDE';
    }
    dom.downPaymentContainer.style.display = 'block';
    dom.docTypeReassortCheck.checked = state.isReassort;
    dom.customCreationCheck.checked = state.isCustomCreation;
    
    dom.applyDiscountCheck.checked = state.applyDiscount;
    
    dom.scopeGlobalRadio.checked = state.orderScope === 'global';
    dom.scopeLicenseeRadio.checked = state.orderScope === 'licensee';
    
    dom.discountGlobalRadio.checked = state.discountType === 'global';
    dom.discountItemRadio.checked = state.discountType === 'item';
    
    dom.licencieNameContainer.classList.toggle('hidden', state.orderScope !== 'licensee');

    const participationContainer = document.getElementById('participation-container');
    const participationAmountContainer = document.getElementById('participation-amount-container');
    const applyParticipationCheck = document.getElementById('apply-participation-check');
    const clubParticipationAmountInput = document.getElementById('clubParticipationAmount');

    participationContainer.classList.toggle('hidden', state.orderScope !== 'licensee');
    applyParticipationCheck.checked = state.applyClubParticipation;
    clubParticipationAmountInput.value = state.clubParticipationAmount;
    participationAmountContainer.classList.toggle('hidden', !state.applyClubParticipation);
    dom.discountControlsContainer.classList.toggle('hidden', !state.applyDiscount);

    if (state.activeLicensee) {
        dom.licencieNameInput.value = '';
        dom.bannerLicenseeName.textContent = state.activeLicensee;
        dom.activeLicenseeBanner.classList.remove('hidden');
    } else {
        dom.activeLicenseeBanner.classList.add('hidden');
    }

    dom.validateOrderBtn.textContent = 'Valider la Commande';
    dom.validateOrderBtn.classList.remove('bg-blue-600', 'hover:bg-blue-700');
    dom.validateOrderBtn.classList.add('bg-indigo-600', 'hover:bg-indigo-700');

    const articleSectionBlocker = document.getElementById('article-section-blocker');
    
    if (!state.clubName.trim() || !state.departement.trim() || !state.orderScope) {
        dom.productFormContainer.classList.add('hidden');
        articleSectionBlocker.classList.remove('hidden');
    } else {
        dom.productFormContainer.classList.remove('hidden');
        articleSectionBlocker.classList.add('hidden');
    }

    // --- MISE À JOUR DU BOUTON PDF (ET RECONNEXION FORCEE) ---
    const generateBtn = document.getElementById('generate-blank-order-btn');
    if (generateBtn) {
        // On clone le bouton pour supprimer les anciens écouteurs et éviter les doublons ou pertes
        const newBtn = generateBtn.cloneNode(true);
        generateBtn.parentNode.replaceChild(newBtn, generateBtn);

        if (state.isReassort) {
            newBtn.innerHTML = "⚙️ Configurer Tarifs & PDF";
            newBtn.className = "w-full mt-1 px-4 py-2 bg-indigo-600 text-white text-sm font-medium rounded-md hover:bg-indigo-700 shadow-md transition-colors";
            newBtn.title = "Définir les prix de la commande initiale et préparer la saisie";
        } else {
            newBtn.innerHTML = "📄 Générer Bon Vierge (PDF)";
            newBtn.className = "w-full mt-1 px-4 py-2 bg-teal-600 text-white text-sm font-medium rounded-md hover:bg-teal-700 transition-colors";
            newBtn.title = "Créer un PDF pour impression";
        }

        // On rattache l'événement proprement
        newBtn.onclick = (e) => {
            e.preventDefault();
            showBlankOrderOptionsModal();
        };
    }
};const renderDashboard = () => {
    let totalHauts = 0;
    let totalBas = 0;
    let totalAccessoires = 0;
    let pendingStock = 0; // Compteur pour le stock en attente dans le panier
    const licenseeSet = new Set();

    state.currentOrderLineItems.forEach(item => {
        // Compte les licenciés
        if (item.licencieName && item.licencieName !== 'Commande Globale' && item.licencieName !== 'Stock Club') {
            licenseeSet.add(item.licencieName);
        }

        // Si c'est une commande pour le stock, on l'ajoute au compteur "en attente"
        if (item.isStockOrder) {
            pendingStock += item.totalQuantity;
        }

        // Logique de comptage par type (Haut/Bas/Accessoire)
        if (item.type === 'pack') {
            item.packItems.forEach(packItem => {
                const product = productMap.get(packItem.productName);
                if (!product) return;
                if (product.type === 'accessoire') {
                    totalAccessoires += packItem.quantity;
                } else if (product.isCuissardOrCollant || product.subtype === 'Bas' || product.subtype.includes('Cuissard') || product.subtype.includes('Corsaire') || product.subtype.includes('Collant')) {
                    totalBas += packItem.quantity;
                } else {
                    totalHauts += packItem.quantity;
                }
            });
        } else {
            const product = productMap.get(item.productName);
            if (!product) return;
            if (product.type === 'accessoire') {
                totalAccessoires += item.totalQuantity;
            } else if (product.isCuissardOrCollant || product.subtype === 'Bas' || product.subtype.includes('Cuissard') || product.subtype.includes('Corsaire') || product.subtype.includes('Collant')) {
                totalBas += item.totalQuantity;
            } else {
                totalHauts += item.totalQuantity;
            }
        }
    });

    // Calcul du stock actuel (déjà enregistré)
    const totalCurrentStock = Object.values(state.clubStock).reduce((sum, product) => 
        sum + Object.values(product).reduce((prodSum, qty) => prodSum + qty, 0), 0);

    // Mise à jour de l'affichage
    dom.summaryTotalHauts.textContent = totalHauts;
    dom.summaryTotalBas.textContent = totalBas;
    dom.summaryTotalAccessoires.textContent = totalAccessoires;
    dom.summaryTotalLicensees.textContent = licenseeSet.size;
    
const stockDisplay = document.getElementById('summary-total-stock');
    
    // Si il y a du stock en attente ET que ce N'EST PAS une archive chargée
    if (pendingStock > 0 && !state.isLoadedFromBackup) {
        stockDisplay.innerHTML = `${totalCurrentStock} <span class="text-sm text-green-600 font-normal">(+${pendingStock})</span>`;
    } else {
        // Sinon (Archive ou pas de modif), on affiche juste le total brut
        stockDisplay.textContent = totalCurrentStock;
    }
};
const renderQuantityDashboard = () => {
    const container = document.getElementById('quantity-dashboard-container');
    if (state.currentOrderLineItems.length === 0) {
        container.innerHTML = '<p class="text-center text-gray-500 text-sm">Le tableau de bord des quantités apparaîtra ici.</p>';
        return;
    }

    const quantityBySubtype = state.currentOrderLineItems.reduce((acc, item) => {
        if (item.type === 'pack') {
            acc['Packs'] = (acc['Packs'] || 0) + item.totalQuantity;
            item.packItems.forEach(packItem => {
                const product = productMap.get(packItem.productName);
                if (!product) return;
                const groupKey = product.category === 'ACCESSOIRES' ? 'Accessoires' : product.subtype;
                if (groupKey) {
                    acc[groupKey] = (acc[groupKey] || 0) + packItem.quantity;
                }
            });
        } else {
            const product = productMap.get(item.productName);
            if (!product) return acc;
            const groupKey = product.category === 'ACCESSOIRES' ? 'Accessoires' : product.subtype;
            if (groupKey) {
                acc[groupKey] = (acc[groupKey] || 0) + item.totalQuantity;
            }
        }
        return acc;
    }, {});

    const getSortPriority = (subtype) => {
        if (subtype === 'Packs') return 1;
        if (subtype === 'Accessoires') return 2;
        return 3;
    };

    const sortedSubtypes = Object.keys(quantityBySubtype).sort((a, b) => {
        const priorityA = getSortPriority(a);
        const priorityB = getSortPriority(b);
        if (priorityA !== priorityB) {
            return priorityA - priorityB;
        }
        return a.localeCompare(b);
    });

    if (sortedSubtypes.length === 0) {
        container.innerHTML = '<p class="text-center text-gray-500 text-sm">Le tableau de bord des quantités apparaîtra ici.</p>';
        return;
    }

    // ▼▼▼ NOUVEAU DESIGN COMPACT "BADGES" ▼▼▼
    let contentHtml = '<h3 class="text-lg font-semibold text-gray-700 mb-3 text-center">Quantités par Type de Produit</h3>';
    contentHtml += '<div class="flex flex-wrap justify-center gap-x-3 gap-y-2">'; 

    sortedSubtypes.forEach(subtype => {
        const quantity = quantityBySubtype[subtype];
        
        const isPackCard = subtype === 'Packs';
        const isSpecialCard = isPackCard; 
        
        const cardClasses = isSpecialCard 
            ? 'bg-purple-100 border-purple-300 text-purple-800 cursor-default'
            : 'bg-white border-gray-300 text-gray-700 cursor-pointer hover:bg-indigo-50 hover:border-indigo-400 quantity-card';
        const cardDataAttr = isSpecialCard ? '' : `data-subtype="${subtype}"`;
        const bubbleClasses = isSpecialCard ? 'bg-purple-600' : 'bg-indigo-600';

        contentHtml += `
            <div class="flex items-center border rounded-full shadow-sm font-medium transition-all ${cardClasses}" ${cardDataAttr}>
                <span class="ps-3 pe-2 py-1 text-sm">${subtype}</span>
                <span class="text-white text-xs font-bold rounded-full w-6 h-6 flex items-center justify-center ${bubbleClasses}">
                    ${quantity}
                </span>
            </div>
        `;
    });

    contentHtml += '</div>';
    // ▲▲▲ FIN DU NOUVEAU DESIGN ▲▲▲
    container.innerHTML = contentHtml;
};
const renderLicenseeDatalist = () => {
    dom.licenseeDatalist.innerHTML = state.licenseeList
        .sort((a, b) => a.localeCompare(b))
        .map(name => `<option value="${name}"></option>`)
        .join('');
};
const renderOrderTableHead = () => {
    let headers = '';

    // Colonne Remise (si activée)
    if (state.applyDiscount) {
         headers += `<th class="px-3 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider w-24">Remise (%)</th>`;
    }

    headers += `
        <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Produit</th>
        <th class="px-6 py-3 text-center text-xs font-medium text-gray-500 uppercase tracking-wider">Qté</th>
        <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">Prix U.<br>HT</th>
        
        <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">Prix U.Public<br>TTC</th>
        <th class="px-6 py-3 text-right text-xs font-bold text-red-600 uppercase tracking-wider">Prix U.Club<br>TTC</th>
        
        <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">Total<br>HT</th>
        <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">Total<br>TTC</th>
        <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">Actions</th>
    `;
    
    dom.orderTableHead.innerHTML = `<tr>${headers}</tr>`;
};// Fonction helper pour calculer le nombre de colonnes
const calculateTotalColumns = () => {
    let count = 8; // Base : Produit, Qté, PU HT, PU TTC, PRIX CLUB (New), Total HT, Total TTC, Actions
    if (state.applyDiscount) count++; // +1 si colonne Remise activée
    return count;
}
const renderOrderTable = () => {
    const totalCols = calculateTotalColumns();
    if (state.currentOrderLineItems.length === 0) {
        dom.orderTableBody.innerHTML = `<tr><td colspan="${totalCols}" class="px-6 py-12 text-center text-gray-500">Aucun article dans la commande.</td></tr>`;
        return;
    }

    let tableHtml = '';
    if (state.orderScope === 'licensee') {
        const groupedItems = state.currentOrderLineItems.reduce((acc, item) => {
            const key = item.licencieName || 'Commande Globale';
            if (!acc[key]) acc[key] = [];
            acc[key].push(item);
            return acc;
        }, {});

        // ▼▼▼ TRI AMÉLIORÉ POUR METTRE STOCK CLUB À LA FIN ▼▼▼
        const sortedLicensees = Object.keys(groupedItems).sort((a, b) => {
             if (a === 'Commande Globale') return 1;
             if (b === 'Commande Globale') return -1;
             if (a === 'STOCK CLUB' || a === 'Stock Club') return 1; // Stock toujours à la fin
             if (b === 'STOCK CLUB' || b === 'Stock Club') return -1;
             return a.localeCompare(b);
        });
        // ▲▲▲ ------------------------------------------- ▲▲▲

sortedLicensees.forEach(licensee => {
            let licenseeTotalDue = 0;
            let licenseeTotalPublic = 0;
            
            // ▼▼▼ C'EST CETTE LIGNE QUI VOUS MANQUAIT ▼▼▼
            const sanitizedName = sanitizeForId(licensee);
            // ▲▲▲ ----------------------------------- ▲▲▲

            // Logique Icone Validé
            const validatedList = state.validatedLicensees || [];
            const isValidated = validatedList.includes(licensee);
            const validationIcon = isValidated ? '<span class="ml-2 text-lg" title="Ce licencié a validé sa commande">✅</span>' : '';

            const headerClass = (licensee === 'Commande Globale' || licensee === 'STOCK CLUB' || licensee === 'Stock Club') ? 'bg-gray-700 text-white font-medium' : 'bg-indigo-50 text-indigo-800 font-bold';

            tableHtml += `<tr id="licencie-header-${sanitizedName}" class="${headerClass}">
                            <td colspan="${totalCols}" class="px-6 py-2 text-left text-sm flex justify-between items-center">
                                <div class="flex items-center gap-2">
                                    <span>${licensee}</span>
                                    ${validationIcon}
                                </div>
                             ${ (licensee !== 'Commande Globale' && licensee !== 'STOCK CLUB' && licensee !== 'Stock Club') ? 
                                    `<div class="flex items-center gap-2">
                                        <button data-action="return-to-input" class="text-xs bg-gray-500 text-white px-2 py-1 rounded hover:bg-gray-600">↑ Retour Saisie</button>
                                        <button data-action="manage-deposit" data-licensee-name="${licensee}" class="text-xs bg-yellow-500 text-white px-2 py-1 rounded hover:bg-yellow-600">Gérer Acompte</button>
                                        <button data-action="add-for-licensee" data-licensee-name="${licensee}" class="text-xs bg-blue-500 text-white px-2 py-1 rounded hover:bg-blue-600">+ Ajouter article</button>
                                        <button data-action="verify-licensee-order" data-licensee-name="${licensee}" class="text-xs bg-indigo-600 text-white px-2 py-1 rounded hover:bg-indigo-700 shadow-sm font-bold">✅ Valider / Ticket</button>
                                    </div>`
                                    : ''
                                }
                            </td>
                          </tr>`;

            const itemRowsHTML = groupedItems[licensee].map(item => {
                // --- 1. PRIX CLUB ---
                let priceForLicenseeTotal = item.totalPriceTTC || 0;

                if (!item.isStockOrder && !item.isFromStock) {
                    const rangeItem = state.clubProductRange.find(rItem => rItem.name === item.productName);
                    if (item.type !== 'pack' && rangeItem && typeof rangeItem.licenseePrice === 'number' && rangeItem.licenseePrice >= 0) {
                        priceForLicenseeTotal = rangeItem.licenseePrice * item.totalQuantity;
                    } else {
                        const effectiveDiscountRate = item.appliedDiscountRate || 0;
                        if (effectiveDiscountRate > 0) priceForLicenseeTotal *= (1 - (effectiveDiscountRate / 100));
                    }
                } else {
                     // Pour le stock (STOCK CLUB), on affiche le prix normal mais on pourrait le mettre à 0 si c'est une dotation.
                     // Ici on garde le prix normal pour info.
                }
                
                licenseeTotalDue += priceForLicenseeTotal;

                // --- 2. PRIX PUBLIC ---
                let priceForPublicTotal = 0;
                if (item.type === 'pack') {
                    priceForPublicTotal = item.realPriceTTC || item.totalPriceTTC; 
                } else {
                    if (!item.isStockOrder) {
                        const product = productMap.get(item.productName);
                        let unitPublic = 0;
                        if (product) unitPublic = getUnitPriceTTC(item.productName, item.totalQuantity, item.options || []);
                        priceForPublicTotal = unitPublic * item.totalQuantity;
                    }
                }
                
                if (licensee !== 'Commande Globale' && licensee !== 'STOCK CLUB' && licensee !== 'Stock Club') {
                    licenseeTotalPublic += priceForPublicTotal;
                }

                return createSingleItemRowHTML(item); 
            }).join('');
            
            tableHtml += itemRowsHTML;

             // Only show summary row for actual licensees (NOT STOCK)
             if (licensee !== 'Commande Globale' && licensee !== 'STOCK CLUB' && licensee !== 'Stock Club') {
                 const deposit = state.licenseeDeposits[licensee] || 0;
                 const participation = state.applyClubParticipation ? state.clubParticipationAmount : 0;
                 
                 let remaining = licenseeTotalDue - participation - deposit;
                 if (Math.abs(remaining) < 0.005) remaining = 0;

                 const participationHtml = participation > 0
                     ? `<span>Partic. Club: <strong class="font-bold text-green-600">-${participation.toFixed(2)}€</strong></span>`
                     : '';

                 let publicPriceDisplay = '';
                 if (licenseeTotalPublic > licenseeTotalDue + 0.01) {
                     publicPriceDisplay = `<span class="text-gray-500 text-xs mr-1">Public: <span class="line-through">${licenseeTotalPublic.toFixed(2)}€</span></span>`;
                 }

                 tableHtml += `<tr class="bg-indigo-100">
                     <td colspan="${totalCols}" class="px-6 py-2 text-sm text-indigo-900 font-semibold">
                         <div class="flex flex-wrap justify-start items-center gap-x-4 gap-y-1">
                             ${publicPriceDisplay}
                             <span>Total Licencié: <strong class="font-bold">${licenseeTotalDue.toFixed(2)}€</strong></span>
                             ${participationHtml}
                             <span>Acompte Versé: <strong class="font-bold">${deposit.toFixed(2)}€</strong></span>
                             <span class="text-red-600">Restant Dû: <strong class="font-bold">${remaining.toFixed(2)}€</strong></span>
                         </div>
                     </td>
                 </tr>`;
             }
        });
    } else { // Mode Global
        tableHtml = state.currentOrderLineItems.map(createSingleItemRowHTML).join('');
    }
    dom.orderTableBody.innerHTML = tableHtml;
};
const createSingleItemRowHTML = (item) => {
    // --- 1. RÉCUPÉRATION INFOS ---
    const productInfo = productMap.get(item.productName);
    const rangeItem = state.clubProductRange.find(rItem => rItem.name === item.productName);

    // --- 2. LOGIQUE MARQUAGE (TECHNIQUE) ---
    let displaySpecificity = item.specificity || '';
    if (rangeItem && rangeItem.markingType) {
        const markingLabel = `FINITION : ${rangeItem.markingType}`;
        if (!displaySpecificity.includes(markingLabel)) {
            if (displaySpecificity !== '') displaySpecificity += ' | ';
            displaySpecificity += markingLabel;
        }
    }

    // --- 3. BADGES ---
    let badgeHtml = '';
    if (item.isStockOrder) {
        badgeHtml = `<span class="px-1 py-0.5 rounded text-[10px] font-bold bg-yellow-100 text-yellow-800 border border-yellow-200 mr-1">📦 STOCK</span>`;
    } else if (item.isFromStock) {
        badgeHtml = `<span class="px-1 py-0.5 rounded text-[10px] font-bold bg-teal-100 text-teal-800 border border-teal-200 mr-1">📤 DU STOCK</span>`;
    } else if (productInfo && productInfo.supplier === 'BRODELEC') {
        badgeHtml = `<span class="px-1 py-0.5 rounded text-[10px] font-bold bg-green-100 text-green-800 border border-green-200 mr-1">🛡️ BRODELEC</span>`;
    } else {
        badgeHtml = `<span class="px-1 py-0.5 rounded text-[10px] font-bold bg-blue-100 text-blue-800 border border-blue-200 mr-1">🚴 NORET</span>`;
    }

    // --- 4. AFFICHAGE PACK (Inchangé) ---
    if (item.type === 'pack') {
        let packItemsDetails = (item.packItems || []).map(packItem => 
            `<li>${packItem.productName} (${packItem.size || 'N/A'})</li>`
        ).join('');
        
        if (item.internalNote) {
             packItemsDetails += `<li class="mt-1 list-none"><span class="text-[10px] text-purple-700 font-bold bg-purple-50 px-1 rounded border border-purple-100">🔒 Note: ${item.internalNote}</span></li>`;
        }

        const totalHT = (item.totalPriceTTC || 0) / (1 + TVA_RATE);
        const publicPrice = item.realPriceTTC || item.totalPriceTTC;
        const isClubPriceApplied = item.totalPriceTTC < (publicPrice - 0.01);

        return `
        <tr class="bg-purple-50 hover:bg-purple-100 transition-colors duration-150 text-sm">
            <td class="px-2 py-2 align-top">
                <div class="flex items-start">
                    <span class="bg-purple-200 text-purple-800 px-1 rounded text-[10px] font-bold mr-1 mt-0.5">PACK</span>
                    <div>
                        <div class="font-bold text-purple-900">${item.productName}</div>
                        <ul class="text-[10px] text-gray-600 list-disc list-inside pl-1 mt-1 leading-tight">${packItemsDetails}</ul>
                    </div>
                </div>
            </td>
            <td class="px-2 py-2 text-center font-medium">${item.totalQuantity || 1}</td>
            <td class="text-right px-2 py-2 text-gray-400">-</td>
            
            <td class="text-right px-2 py-2 text-gray-500 ${isClubPriceApplied ? 'line-through text-xs' : ''}">
                ${publicPrice.toFixed(2)}€
            </td>
            
            <td class="text-right px-2 py-2 font-bold ${isClubPriceApplied ? 'text-red-600 bg-red-50 rounded' : 'text-gray-300'}">
                ${isClubPriceApplied ? item.totalPriceTTC.toFixed(2) + '€' : '-'}
            </td> 

            <td class="text-right px-2 py-2 font-medium text-gray-600">${totalHT.toFixed(2)}€</td>
            <td class="text-right px-2 py-2 font-bold text-gray-900">${(item.totalPriceTTC).toFixed(2)}€</td>
           <td class="px-2 py-2 text-center align-middle whitespace-nowrap sticky right-0 bg-white shadow-[-5px_0_5px_-5px_rgba(0,0,0,0.1)]">
            ${state.isReadOnly ? 
                '<span class="text-gray-400 text-xs italic">Verrouillé</span>' : 
                `<div class="flex items-center justify-center gap-1">
                    <button type="button" data-action="edit-item" data-item-id="${item.id}" class="text-[11px] bg-blue-50 text-blue-600 px-2 py-1 rounded hover:bg-blue-100 border border-blue-200 transition font-medium">Modif.</button>
                    <button type="button" data-action="remove-item" data-item-id="${item.id}" class="text-[11px] bg-red-50 text-red-600 px-2 py-1 rounded hover:bg-red-100 border border-red-200 transition font-bold" title="Supprimer">X</button>
                </div>`
            }
        </td>
    </tr>`;
    }

    // --- 5. AFFICHAGE ARTICLE STANDARD ---
    let detailsHtml = '';
    
    // A. Tailles
    if (Object.keys(item.quantitiesPerSize).length > 0) {
        const qtyParts = [];
        for (const [size, qty] of Object.entries(item.quantitiesPerSize)) {
            if (qty > 0) qtyParts.push(`${size}: <strong>${qty}</strong>`);
        }
        if (qtyParts.length > 0) detailsHtml += `<div class="text-[11px] text-gray-600 mt-1">Tailles: ${qtyParts.join(', ')}</div>`;
    }
    
    // B. Options
    if (item.options && item.options.length > 0) {
        detailsHtml += `<div class="text-[11px] text-indigo-600 mt-0.5">Options: ${item.options.join(', ')}</div>`;
    }
    
    // C. Visuel
    if (item.visual) {
        detailsHtml += `<div class="text-[11px] text-purple-600 mt-0.5">Visuel: ${item.visual}</div>`;
    }

    // D. Spécificité & Couleur
    let specText = [];
    if (item.color) specText.push(`Coul: ${item.color}`);
    if (displaySpecificity) specText.push(displaySpecificity);
    if (specText.length > 0) {
        detailsHtml += `<div class="text-[10px] text-gray-500 italic mt-0.5 bg-gray-50 inline-block px-1 rounded border border-gray-100">${specText.join(' | ')}</div>`;
    }

    // E. Note Interne
    if (item.internalNote) {
        detailsHtml += `<div class="mt-1"><span class="text-[10px] text-purple-700 font-bold bg-purple-50 px-1 py-0.5 rounded border border-purple-100">🔒 Note Club: ${item.internalNote}</span></div>`;
    }

    // --- CALCUL PRIX PUBLIC DE RÉFÉRENCE (MODIFIÉ) ---
    let publicPriceTTC = 0;
    if (productInfo) {
        const isStandardNoret = productInfo.type !== 'accessoire' && productInfo.supplier !== 'BRODELEC';
        
        if (isStandardNoret) {
            // CAS STANDARD NORET : On force la base tarifaire "5 pièces" pour le prix public affiché
            // Cela permet de montrer le prix de base avant dégressivité volume ou remise club
            publicPriceTTC = getUnitPriceTTC(item.productName, 5, item.options || []);
        } else {
            // CAS ACCESSOIRES / BRODELEC : On garde le calcul réel (souvent prix fixe ou petites tranches)
            publicPriceTTC = getUnitPriceTTC(item.productName, item.totalQuantity, item.options || []);
        }
    } else {
        publicPriceTTC = item.unitPriceTTC; // Fallback
    }

    // Détection des cas
    const isLowerPrice = item.unitPriceTTC < (publicPriceTTC - 0.01);
    const isFixedClubPrice = rangeItem && typeof rangeItem.licenseePrice === 'number' && rangeItem.licenseePrice > 0 && !item.isManualPrice;
    const isRestockPrice = state.isReassort && rangeItem && rangeItem.lastRestockPrice && !item.isManualPrice;
    const isClubPriceApplied = isLowerPrice || isFixedClubPrice || isRestockPrice;

    // Gestion du style du prix
    let priceClass = "text-gray-300"; 
    
    if (isClubPriceApplied) {
        if (isFixedClubPrice) {
            priceClass = "text-red-700 font-extrabold bg-red-100 rounded border border-red-200 px-1";
        } else {
            priceClass = "text-red-600 bg-red-50 rounded font-bold px-1";
        }
    }

    return `
    <tr class="hover:bg-gray-50 transition-colors duration-150 border-b border-gray-100 group text-sm">
        <td class="px-2 py-2 align-top">
            <div class="flex items-start">
                ${badgeHtml}
                <div class="min-w-0">
                    <div class="font-bold text-gray-800 leading-tight">${item.productName}</div>
                    ${detailsHtml}
                </div>
            </div>
        </td>

        <td class="px-2 py-2 text-center font-medium align-top">
            <div class="flex items-center justify-center gap-1">
                <button data-action="decrease-qty" data-item-id="${item.id}" class="w-5 h-5 rounded bg-gray-200 text-gray-600 hover:bg-gray-300 flex items-center justify-center font-bold text-xs">-</button>
                <span class="w-5 text-center font-bold text-gray-700">${item.totalQuantity}</span>
                <button data-action="increase-qty" data-item-id="${item.id}" class="w-5 h-5 rounded bg-gray-200 text-gray-600 hover:bg-gray-300 flex items-center justify-center font-bold text-xs">+</button>
            </div>
        </td>

        <td class="text-right px-2 py-2 text-gray-400 text-xs">${item.unitPriceHT.toFixed(2)}€</td>
        
        <td class="text-right px-2 py-2 text-gray-500 ${isClubPriceApplied ? 'line-through text-xs' : ''}">
            ${publicPriceTTC.toFixed(2)}€
        </td>
        
        <td class="text-right px-2 py-2">
             <span class="${priceClass}">
                ${isClubPriceApplied ? item.unitPriceTTC.toFixed(2) + '€' : '-'}
             </span>
        </td>

        <td class="text-right px-2 py-2 text-gray-600 font-medium">${item.totalPriceHT.toFixed(2)}€</td>
        <td class="text-right px-2 py-2 font-bold text-gray-900">${item.totalPriceTTC.toFixed(2)}€</td>
        
        <td class="px-2 py-2 text-center align-middle whitespace-nowrap sticky right-0 bg-white shadow-[-5px_0_5px_-5px_rgba(0,0,0,0.1)]">
            <div class="flex items-center justify-center gap-1">
                <button type="button" data-action="edit-item" data-item-id="${item.id}" class="text-[11px] bg-blue-50 text-blue-600 px-2 py-1 rounded hover:bg-blue-100 border border-blue-200 transition font-medium">
                    Modif.
                </button>
                <button type="button" data-action="remove-item" data-item-id="${item.id}" class="text-[11px] bg-red-50 text-red-600 px-2 py-1 rounded hover:bg-red-100 border border-red-200 transition font-bold" title="Supprimer">
                    X
                </button>
            </div>
        </td>
    </tr>
    `;
};const renderTotals = () => {
    const totals = calculateAllTotals();
    dom.subtotalHT.textContent = `${totals.subtotalHT.toFixed(2)}€`;
    dom.subtotalTTC.textContent = `${totals.subtotalTTC.toFixed(2)}€`;
    dom.discountAmountHT.textContent = `-${totals.discountAmountHT.toFixed(2)}€`;
    dom.discountAmountTTC.textContent = `-${totals.discountAmountTTC.toFixed(2)}€`;
    dom.shippingHT.textContent = `${totals.shippingHT.toFixed(2)}€`;
    dom.shippingTTC.textContent = `${totals.shippingTTC.toFixed(2)}€`;

    dom.graphicFeeContainer.classList.toggle('hidden', !state.isCustomCreation);
    dom.graphicFeeHT.textContent = `${totals.graphicFeeHT.toFixed(2)}€`;
    dom.graphicFeeTTC.textContent = `${totals.graphicFeeTTC.toFixed(2)}€`;

    dom.grandTotalHT.textContent = `${totals.grandTotalHT.toFixed(2)}€`;
    dom.grandTotalTTC.textContent = `${totals.grandTotalTTC.toFixed(2)}€`;
    dom.downPayment.textContent = `${totals.downPayment.toFixed(2)}€`;
    dom.totalArticlesDisplay.textContent = `Total des articles : ${state.currentOrderLineItems.reduce((acc, item) => acc + item.totalQuantity, 0)}`;
};
const renderProductForm = () => {
    // --- 1. GESTION DU BOUTON TOGGLE (AFFICHAGE) ---
    if (state.clubProductRange.length > 0) {
        dom.toggleProductsViewContainer.classList.remove('hidden');
        if (state.showAllProducts) {
            dom.toggleProductsBtn.textContent = 'Afficher seulement la gamme';
            dom.toggleProductsBtn.classList.remove('bg-gray-200', 'text-gray-700');
            dom.toggleProductsBtn.classList.add('bg-orange-100', 'text-orange-800', 'border-orange-200', 'border');
        } else {
            dom.toggleProductsBtn.textContent = 'Afficher tous les articles';
            dom.toggleProductsBtn.classList.add('bg-gray-200', 'text-gray-700');
            dom.toggleProductsBtn.classList.remove('bg-orange-100', 'text-orange-800', 'border-orange-200', 'border');
        }
    } else {
        dom.toggleProductsViewContainer.classList.add('hidden');
    }

    let formHtml = `
        <div class="flex border-b mb-4">
            <button id="toggle-add-article" class="py-2 px-4 font-medium text-sm ${state.addMode === 'article' ? 'border-b-2 border-indigo-500 text-indigo-600' : 'text-gray-500'}">Ajouter un Article</button>
            <button id="toggle-add-pack" class="py-2 px-4 font-medium text-sm ${state.addMode === 'pack' ? 'border-b-2 border-indigo-500 text-indigo-600' : 'text-gray-500'}">Ajouter un Pack</button>
        </div>
    `;

    if (state.addMode === 'article') {
        let sourceProducts = [];
        if (state.showAllProducts || state.clubProductRange.length === 0) {
            sourceProducts = allAvailableProducts;
        } else {
            sourceProducts = allAvailableProducts.filter(p => {
                const rangeItem = state.clubProductRange.find(r => r.name === p.name);
                if (!rangeItem) return false; 
                if (state.isReassort && rangeItem.isIncludedInRestock === false) return false; 
                return true;
            });
        }
        
        const getBtnClass = (cat) => {
            return state.activeCategory === cat 
                ? "px-3 py-2 text-xs font-bold rounded shadow bg-indigo-600 text-white transform scale-105 transition" 
                : "px-3 py-2 text-xs font-medium rounded border border-gray-300 bg-white hover:bg-indigo-50 text-gray-700 transition";
        };

        formHtml += `
            <div id="product-selection-step" class="p-4 rounded-xl bg-gray-50 border border-gray-200">
                <div class="flex justify-between items-end mb-2">
                    <label class="block text-sm font-bold text-gray-700">1. Choisissez une catégorie (Cliquer pour ouvrir/fermer) :</label>
                    ${state.showAllProducts ? '<span class="text-xs text-orange-600 font-bold animate-pulse">Mode Catalogue Complet</span>' : ''}
                </div>

                <div class="flex flex-wrap gap-2 mb-4">
                    <button type="button" class="${getBtnClass('CYCLISME')}" onclick="state.activeCategory = state.activeCategory === 'CYCLISME' ? null : 'CYCLISME'; state.currentProduct=''; renderProductForm();">🚴 Cyclisme</button>
                    <button type="button" class="${getBtnClass('RUNNING')}" onclick="state.activeCategory = state.activeCategory === 'RUNNING' ? null : 'RUNNING'; state.currentProduct=''; renderProductForm();">🏃 Running</button>
                    <button type="button" class="${getBtnClass('ACCESSOIRES')}" onclick="state.activeCategory = state.activeCategory === 'ACCESSOIRES' ? null : 'ACCESSOIRES'; state.currentProduct=''; renderProductForm();">🧢 Accessoires</button>
                    <button type="button" class="${getBtnClass('ENFANTS')}" onclick="state.activeCategory = state.activeCategory === 'ENFANTS' ? null : 'ENFANTS'; state.currentProduct=''; renderProductForm();">👶 Enfants</button>
                    <button type="button" class="${getBtnClass('BRODELEC')}" onclick="state.activeCategory = state.activeCategory === 'BRODELEC' ? null : 'BRODELEC'; state.currentProduct=''; renderProductForm();">🛡️ Brodelec</button>
                    
                    <button type="button" class="px-3 py-2 text-xs font-medium rounded border border-gray-300 bg-gray-200 hover:bg-gray-300 text-gray-700 transition ml-auto" 
                        onclick="state.activeCategory=null; state.currentProduct=''; renderProductForm();">
                        Tout voir (Liste)
                    </button>
                </div>
        `;

        if (state.activeCategory) {
            const filteredProducts = sourceProducts.filter(p => p.category === state.activeCategory && p.category !== 'option');
            const groupedBySubtype = filteredProducts.reduce((acc, p) => {
                const sub = p.subtype || 'Divers';
                if(!acc[sub]) acc[sub] = [];
                acc[sub].push(p);
                return acc;
            }, {});

            formHtml += `<div class="space-y-4 animate-fade-in bg-white p-2 rounded border border-gray-100 shadow-inner">`;
            if (Object.keys(groupedBySubtype).length === 0) {
                 formHtml += `<p class="text-sm text-gray-500 italic p-4 text-center">Aucun produit disponible.</p>`;
            }

            for (const [subtype, products] of Object.entries(groupedBySubtype)) {
                formHtml += `
                    <div>
                        <h5 class="text-sm font-extrabold text-indigo-700 uppercase tracking-wide mb-3 border-b-2 border-indigo-100 pb-2 mt-6">${subtype}</h5>
                        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-2">
                            ${products.map(p => {
                                const isSelected = state.currentProduct === p.name;
                                return `
                                <button type="button" 
                                    class="text-left px-3 py-2 rounded-lg border text-sm transition-all duration-200 flex items-center justify-between group
                                    ${isSelected ? 'bg-indigo-600 text-white border-indigo-600 shadow-md ring-2 ring-indigo-300' : 'bg-white border-gray-300 text-gray-700 hover:border-indigo-400 hover:shadow-sm'}
                                    "
                                    onclick="state.currentProduct='${p.name.replace(/'/g, "\\'")}'; calculateCurrentFormPrice(); renderProductForm(); setTimeout(() => document.getElementById('product-details-form').scrollIntoView({behavior:'smooth'}), 50);"
                                >
                                    <span class="font-medium truncate mr-2" title="${p.name}">${p.name}</span>
                                    ${isSelected ? '<span>✔</span>' : '<span class="opacity-0 group-hover:opacity-100 text-indigo-400">➔</span>'}
                                </button>
                                `;
                            }).join('')}
                        </div>
                    </div>
                `;
            }
            formHtml += `
                <div class="flex justify-center mt-4 pt-2 border-t border-gray-100">
                    <button type="button" class="text-xs font-bold text-gray-500 hover:text-indigo-600 flex items-center gap-1 transition-colors" 
                        onclick="state.activeCategory=null; renderProductForm();">
                        🔼 Replier la liste
                    </button>
                </div>
            `;
            formHtml += `</div>`;
        } else {
            const productsToShow = sourceProducts.filter(p => p.category !== 'option');
            const grouped = productsToShow.reduce((acc, p) => {
                const groupKey = `${p.category} - ${p.subtype}`;
                acc[groupKey] = [...(acc[groupKey] || []), p];
                return acc;
            }, {});
            const productSelectorOptions = Object.entries(grouped).map(([groupLabel, productList]) =>
                `<optgroup label="${groupLabel}">${productList.map(p => `<option value="${p.name}" ${state.currentProduct === p.name ? 'selected' : ''}>${p.name}</option>`).join('')}</optgroup>`
            ).join('');
            formHtml += `
                <label class="block text-xs font-bold text-gray-500 mb-1">Ou sélectionnez dans la liste complète :</label>
                <select id="current-product-select" class="block w-full rounded-md border-gray-300 shadow-sm py-2 text-sm focus:ring-indigo-500 focus:border-indigo-500">
                    <option value="">-- Sélectionner un produit --</option>
                    ${productSelectorOptions}
                </select>
            `;
        }
        formHtml += `</div>`; 
        
        const productData = productMap.get(state.currentProduct);
        
        if (productData) {
            const isTop = productData.type === 'haut' || productData.type === 'enfant'; 
            const isBas = productData.isCuissardOrCollant || productData.subtype.includes('Bas') || productData.subtype.includes('Cuissard') || productData.subtype.includes('Corsaire') || productData.subtype.includes('Collant') || productData.subtype.includes('Short');
            
            // On vérifie si Sur-Mesure est activé DANS LE STATE
            const isSurMesureActive = state.currentSelectedOptions.includes('SUR-MESURE');

            formHtml += `<div id="product-details-form" class="space-y-4 mt-4 bg-white p-4 rounded-xl border border-gray-200 shadow-sm border-l-4 border-l-indigo-500">`;
            formHtml += `<div class="flex flex-col md:flex-row md:items-center justify-between mb-2 gap-2">
                            <h4 class="font-bold text-indigo-900 text-lg">${productData.name}</h4>
                            <span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-indigo-100 text-indigo-800 w-fit">
                                ${productData.category} - ${productData.subtype}
                            </span>
                         </div>`;

            formHtml += `<div id="size-selection-step" class="space-y-4">`;
            
            // --- LOGIQUE D'AFFICHAGE DES TAILLES (BASCULE) ---
            const showSizeGrid = productData.type === 'haut' || productData.type === 'enfant' || (productData.type === 'accessoire' && productData.sizeType && productData.sizeType !== 'unique');
            
            if (showSizeGrid) {
                // 1. BLOC STANDARD (Caché si Sur-Mesure actif)
                formHtml += `<div id="standard-size-grid" class="${isSurMesureActive ? 'hidden' : ''}">`;
                const sizes = productSizes[productData.sizeType || productData.type] || [];
                const sizeInputs = sizes.map(size => {
                    const stockQty = state.clubStock[productData.name]?.[size] ?? 0;
                    let stockColor = 'text-gray-300';
                    let stockLabel = '-';
                    let bgClass = parseInt(state.currentQuantities[size] || 0) > 0 ? 'bg-indigo-50 border-indigo-300 ring-2 ring-indigo-200' : '';
                    if (stockQty > 0) { stockColor = 'text-orange-500 font-bold'; stockLabel = `Stock: ${stockQty}`; }
                    return `<div class="text-center">
                                <label for="size-${size}" class="block text-xs font-bold text-gray-600 mb-1">${size}</label>
                                <input type="number" id="size-${size}" data-size="${size}" class="size-input block w-full rounded-md border-gray-300 shadow-sm text-center font-medium ${bgClass}" placeholder="-" value="${state.currentQuantities[size] || ''}">
                                <span class="stock-info ${stockColor} text-[10px] block mt-0.5">${stockLabel}</span>
                            </div>`;
                }).join('');
                formHtml += `<div class="grid grid-cols-4 sm:grid-cols-6 md:grid-cols-8 gap-3">${sizeInputs}</div>`;
                formHtml += `</div>`; // Fin bloc standard

                // 2. BLOC SUR-MESURE QUANTITÉ (Visible uniquement si Sur-Mesure actif)
                formHtml += `
                    <div id="sur-mesure-qty-wrapper" class="${isSurMesureActive ? '' : 'hidden'} bg-yellow-50 p-4 rounded border border-yellow-200 text-center">
                        <label class="block text-sm font-bold text-yellow-900 mb-2">Quantité en Sur-Mesure</label>
                        <div class="flex justify-center items-center gap-2">
                            <input type="number" id="sm-qty-input" min="1" value="${state.currentQuantities['SUR-MESURE'] || 1}" class="block w-24 rounded-md border-yellow-400 shadow-sm text-center font-bold text-lg text-yellow-900 focus:ring-yellow-500 focus:border-yellow-500">
                            <span class="text-sm text-yellow-700">pièce(s)</span>
                        </div>
                        <p class="text-xs text-yellow-600 mt-2">La taille "SUR-MESURE" sera appliquée à l'article.</p>
                    </div>
                `;
            }

            if (productData.type === 'accessoire' && (!productData.sizeType || productData.sizeType === 'unique')) {
                 const stockQty = state.clubStock[productData.name]?.['U'] ?? 0;
                 formHtml += `<div><label for="accessory-qty" class="block text-sm font-medium text-gray-700">Quantité</label><input type="number" id="accessory-qty" value="${state.currentAccessoryQuantity}" min="1" class="mt-1 block w-32 rounded-md border-gray-300 shadow-sm"><p class="text-xs text-gray-500 mt-1">Stock disponible : ${stockQty}</p></div>`;
            }
            if (productData.colors && productData.colors.length > 0) {
                const colorOptions = productData.colors.map(c => `<option value="${c}" ${state.currentColor === c ? 'selected' : ''}>${c}</option>`).join('');
                formHtml += `<div class="mt-4 bg-gray-50 p-3 rounded-lg border border-gray-100"><div><label class="block text-sm font-medium text-gray-700">Coloris (Standard)</label><select id="current-color-select" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm"><option value="">-- Sélectionner --</option>${colorOptions}</select></div></div>`;
            }
            formHtml += `</div>`; 
            
            // --- OPTIONS ET SUR-MESURE ---
            formHtml += `<div class="mt-4 pt-4 border-t border-gray-200 grid grid-cols-1 md:grid-cols-2 gap-6">`;
            
            let optionsHtml = '';
            let lengthOptions = [];
            const isLongSleeved = productData.type === 'haut' && (productData.name.includes('ML') || productData.name.includes('MANCHES LONGUES'));
            if ((productData.isCuissardOrCollant || isLongSleeved)) { lengthOptions = allAvailableProducts.filter(p => p.optionGroup === 'length'); }
            
            let normalOptions = [];
            if (productData.hasOptions !== false && productData.type !== 'accessoire' && !productData.isCuissardOrCollant) { 
                normalOptions = allAvailableProducts.filter(p => p.category === 'option' && !p.optionGroup && p.name !== 'Majoration Taille Décalée' && p.name !== 'AJOUT NOM/PRENOM' && p.name !== 'SUR-MESURE' && !productData.excludedOptions?.includes(p.name)); 
            }

            // GESTION BLOC SUR-MESURE (Cocher)
            if ((isTop || isBas) && productData.supplier !== 'BRODELEC' && productData.type !== 'accessoire') {
                const surMesureOption = allAvailableProducts.find(p => p.name === 'SUR-MESURE');
                if (surMesureOption) {
                    optionsHtml += `
                    <div class="col-span-1 md:col-span-2 bg-yellow-50 p-4 rounded-lg border border-yellow-200 mb-4">
                        <div class="flex items-center">
                            <input id="option-sur-mesure" type="checkbox" data-option-name="SUR-MESURE" class="option-checkbox h-5 w-5 text-yellow-600 rounded border-gray-300" ${isSurMesureActive ? 'checked' : ''}>
                            <label for="option-sur-mesure" class="ml-3 font-bold text-yellow-900 flex items-center">
                                ✂️ Option SUR-MESURE (+${surMesureOption.fixedPriceTTC.toFixed(2)}€)
                            </label>
                        </div>
                        <div id="sur-mesure-fields" class="${isSurMesureActive ? '' : 'hidden'} mt-3 pt-3 border-t border-yellow-200 grid grid-cols-2 sm:grid-cols-3 gap-3">
                            <p class="col-span-full text-xs text-yellow-800 mb-1 italic">Saisissez les mensurations (en cm) - Voir fiche PDF</p>
                            
                            <div><label class="block text-[10px] font-bold text-gray-600">1. Hauteur Totale</label><input type="number" class="sm-input block w-full text-xs border-gray-300 rounded" placeholder="cm" data-label="Hauteur(1)"></div>
                            
                            ${isTop ? `
                                <div><label class="block text-[10px] font-bold text-gray-600">2. Tour de cou</label><input type="number" class="sm-input block w-full text-xs border-gray-300 rounded" placeholder="cm" data-label="Cou(2)"></div>
                                <div><label class="block text-[10px] font-bold text-gray-600">3. Tour poitrine</label><input type="number" class="sm-input block w-full text-xs border-gray-300 rounded" placeholder="cm" data-label="Poitrine(3)"></div>
                                <div><label class="block text-[10px] font-bold text-gray-600">5. Longueur bras</label><input type="number" class="sm-input block w-full text-xs border-gray-300 rounded" placeholder="cm" data-label="Bras(5)"></div>
                                <div><label class="block text-[10px] font-bold text-gray-600">6. Tour biceps</label><input type="number" class="sm-input block w-full text-xs border-gray-300 rounded" placeholder="cm" data-label="Biceps(6)"></div>
                            ` : ''}

                            <div><label class="block text-[10px] font-bold text-gray-600">4. Tour taille</label><input type="number" class="sm-input block w-full text-xs border-gray-300 rounded" placeholder="cm" data-label="Taille(4)"></div>

                            ${isBas ? `
                                <div><label class="block text-[10px] font-bold text-gray-600">10. Tour bassin</label><input type="number" class="sm-input block w-full text-xs border-gray-300 rounded" placeholder="cm" data-label="Bassin(10)"></div>
                                <div><label class="block text-[10px] font-bold text-gray-600">7. Entrejambe</label><input type="number" class="sm-input block w-full text-xs border-gray-300 rounded" placeholder="cm" data-label="Entrejambe(7)"></div>
                                <div><label class="block text-[10px] font-bold text-gray-600">8. Cuisse (Haut)</label><input type="number" class="sm-input block w-full text-xs border-gray-300 rounded" placeholder="cm" data-label="CuisseHt(8)"></div>
                                <div><label class="block text-[10px] font-bold text-gray-600">9. Cuisse (Bas)</label><input type="number" class="sm-input block w-full text-xs border-gray-300 rounded" placeholder="cm" data-label="CuisseBs(9)"></div>
                            ` : ''}
                        </div>
                    </div>`;
                }
            }

            if (lengthOptions.length > 0 || normalOptions.length > 0) {
                optionsHtml += `<div><label class="block text-sm font-bold text-gray-700 mb-2">Autres Options</label><div class="space-y-2 bg-gray-50 p-3 rounded border border-gray-200">`;
                if (lengthOptions.length > 0) {
                     optionsHtml += lengthOptions.map(opt => `<div class="flex items-center"><input id="option-${opt.name}" type="checkbox" data-option-name="${opt.name}" data-option-group="length" class="option-checkbox h-4 w-4 text-indigo-600 rounded border-gray-300" ${state.currentSelectedOptions.includes(opt.name) ? 'checked' : ''}><label for="option-${opt.name}" class="ml-2 text-sm text-gray-700">${opt.name.replace('Ajustement Longueur ', '')} (+${opt.fixedPriceTTC.toFixed(2)}€)</label></div>`).join('');
                }
                if (normalOptions.length > 0) {
                    optionsHtml += normalOptions.map(opt => `<div class="flex items-center"><input id="option-${opt.name}" type="checkbox" data-option-name="${opt.name}" class="option-checkbox h-4 w-4 text-indigo-600 rounded border-gray-300" ${state.currentSelectedOptions.includes(opt.name) ? 'checked' : ''}><label for="option-${opt.name}" class="ml-2 text-sm text-gray-700">${opt.name}</label></div>`).join('');
                }
                optionsHtml += `</div></div>`;
            }
            formHtml += optionsHtml;

            const isShortVTT = productData.name.includes('SHORT VTT'); 
            if (isBas && !isShortVTT && productData.category === 'CYCLISME') {
                const currentMarkVal = state.currentMarking || '';
                formHtml += `<div class="mt-4 p-3 bg-gray-50 border border-gray-200 rounded"><label class="block text-xs font-bold text-gray-700 mb-1">Type de Marquage (Bas)</label><select id="bas-marking-select" class="block w-full text-sm rounded-md border-gray-300 shadow-sm"><option value="">-- Sélectionner --</option><option value="UNI" ${currentMarkVal === 'UNI' ? 'selected' : ''}>UNI (Sans marquage)</option><option value="DTF" ${currentMarkVal === 'DTF' ? 'selected' : ''}>DTF (Transfert)</option><option value="SUBLI" ${currentMarkVal === 'SUBLI' ? 'selected' : ''}>SUBLI (Sublimation)</option></select></div>`;
            }

            formHtml += `<div><label class="block text-sm font-bold text-gray-700 mb-2">Personnalisation</label><div class="space-y-3 bg-gray-50 p-3 rounded border border-gray-200">`;
            const isBrodelec = productData.supplier === 'BRODELEC';
            if ((productData.type === 'haut' && productData.subtype !== 'Accessoires') || isBrodelec) {
                if (isBrodelec) {
                    const logoOption = productMap.get('Personnalisation logo club');
                    if (logoOption) {
                        const isChecked = state.currentSelectedOptions.includes(logoOption.name);
                        formHtml += `<div class="flex flex-col mt-2 p-3 bg-blue-50 border border-blue-200 rounded-md"><div class="flex items-center mb-2"><input id="option-logo-club" type="checkbox" data-option-name="${logoOption.name}" class="option-checkbox h-4 w-4 text-indigo-600 rounded border-gray-300" ${isChecked ? 'checked' : ''}><label for="option-logo-club" class="ml-2 text-sm font-bold text-blue-900">Personnalisation (Broderie/Transfert...)</label></div></div>`;
                    }
                } else {
                    const nameOption = productMap.get('AJOUT NOM/PRENOM');
                    if (nameOption) {
                        const isChecked = state.currentSelectedOptions.includes(nameOption.name);
                        formHtml += `<div class="flex flex-col mt-2"><div class="flex items-center mb-2"><input id="option-ajout-nom" type="checkbox" data-option-name="${nameOption.name}" class="option-checkbox h-4 w-4 text-indigo-600 rounded border-gray-300" ${isChecked ? 'checked' : ''}><label for="option-ajout-nom" class="ml-2 text-sm font-medium text-gray-900">Marquage Nom/Prénom (+${nameOption.fixedPriceTTC.toFixed(2)}€)</label></div><div id="marquage-input-container" class="${isChecked ? '' : 'hidden'} pl-6"><input type="text" id="marquage-text" placeholder="Ex: J. DUPONT" class="block w-full rounded-md border-gray-300 shadow-sm text-sm"></div></div>`;
                    }
                }
            }
            const canSplitSize = productData.name.includes('COMBINAISON ROUTE MANCHES COURTES');
            if (canSplitSize) {
                const splitOption = productMap.get('Majoration Taille Décalée');
                formHtml += `<div class="flex items-center pt-2 border-t border-gray-200"><input id="option-taille-decalee" type="checkbox" data-option-name="${splitOption.name}" class="h-4 w-4 text-indigo-600 rounded border-gray-300"><label for="option-taille-decalee" class="ml-2 text-sm text-gray-900">Taille Décalée (+6€)</label></div>`;
            }
            formHtml += `<div class="mt-4 grid grid-cols-1 md:grid-cols-2 gap-4"><div><label for="specificity" class="block text-xs font-bold text-gray-700">Spécificité Technique (Visible par NORET)</label><textarea id="specificity" rows="2" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm text-sm bg-white" placeholder="Ex: Manches +2cm, Coupe Femme...">${state.currentSpecificity}</textarea></div><div><label for="internal-note" class="block text-xs font-bold text-purple-700">Note Interne Club (Invisible pour NORET)</label><textarea id="internal-note" rows="2" class="mt-1 block w-full rounded-md border-purple-200 shadow-sm text-sm bg-purple-50 placeholder-purple-300" placeholder="Ex: Offert par le club, Acompte réglé en espèces...">${state.currentInternalNote || ''}</textarea></div></div>`;
            formHtml += `</div></div></div>`; 

            const hasManualPrice = state.manualUnitPrice !== '' && parseFloat(state.manualUnitPrice) > 0;
            formHtml += `<div id="add-button-step" class="mt-6 p-4 bg-gray-50 border-t border-gray-200 rounded-b-xl flex flex-col md:flex-row items-center justify-between gap-4">
                            <div class="flex flex-col gap-2">
                                <div class="flex items-center gap-4">
                                    <div class="flex items-center"><input id="force-manual-price-check" type="checkbox" class="h-4 w-4 rounded border-gray-300 text-orange-600 focus:ring-orange-500" ${hasManualPrice ? 'checked' : ''}><label for="force-manual-price-check" class="ml-2 text-xs font-medium text-gray-600">Prix manuel</label></div>
                                    <div class="flex items-center"><input id="add-to-stock-check" type="checkbox" class="h-4 w-4 rounded border-gray-300 text-green-600 focus:ring-green-500" ${state.isAddingForStock ? 'checked' : ''}><label for="add-to-stock-check" class="ml-2 text-xs font-bold text-green-700">📦 Pour le stock</label></div>
                                </div>
                                <div class="flex items-center"><input id="ignore-stock-check" type="checkbox" class="h-4 w-4 rounded border-gray-300 text-red-600 focus:ring-red-500"><label for="ignore-stock-check" class="ml-2 text-xs font-bold text-red-600">🏭 Ignorer stock (Forcer Cde)</label></div>
                                <div id="manual-price-container" class="${hasManualPrice ? '' : 'hidden'} mt-1"><input type="number" id="manual-price-input" value="${state.manualUnitPrice}" class="w-24 rounded-md border-gray-300 shadow-sm text-sm" placeholder="Prix TTC"></div>
                            </div>
                            <div class="flex items-center gap-4 w-full md:w-auto">
                                ${state.currentCalculatedUnitPrice > 0 ? `<div class="bg-white border border-indigo-200 px-4 py-2 rounded-lg text-center"><p class="text-xs text-indigo-600 font-semibold">Prix Unitaire TTC</p><p class="text-lg font-bold text-indigo-800">${state.currentCalculatedUnitPrice.toFixed(2)}€</p></div>` : ''}
                                <button id="add-product-btn" class="flex-grow md:flex-grow-0 inline-flex items-center justify-center px-8 py-3 border border-transparent text-base font-bold rounded-md shadow-md text-white bg-indigo-600 hover:bg-indigo-700 transform transition hover:scale-105 disabled:bg-gray-300 disabled:scale-100 disabled:cursor-not-allowed">AJOUTER</button>
                            </div>
                        </div>`;
            formHtml += `</div>`; 
        }
    } else if (state.addMode === 'pack') {
        if (state.clubPacks.length === 0) { formHtml += `<p class="text-center text-gray-500 p-4">Aucun pack n'a été créé pour ce club.</p>`; } else { const packOptions = state.clubPacks.map(pack => `<option value="${pack.name}" ${state.currentPackSelection === pack.name ? 'selected' : ''}>${pack.name} - ${pack.priceTTC.toFixed(2)}€</option>`).join(''); formHtml += `<div class="p-4 space-y-4"><div><label for="pack-select" class="block text-sm font-medium text-gray-700">Choisir un Pack</label><select id="pack-select" class="mt-1 block w-full md:w-1/2 rounded-md border-gray-300 shadow-sm"><option value="">-- Sélectionner --</option>${packOptions}</select></div>`; const selectedPack = state.clubPacks.find(p => p.name === state.currentPackSelection); if (selectedPack) { formHtml += '<div class="space-y-6 pt-4 border-t">'; selectedPack.productNames.forEach(productName => { const product = productMap.get(productName); if(product) { const config = state.currentPackItemConfigs[productName] || { size: '', visual: '', options: [] }; const sizes = productSizes[product.sizeType || product.type] || []; const sizeOptions = sizes.map(size => `<option value="${size}" ${config.size === size ? 'selected' : ''}>${size}</option>`).join(''); formHtml += `<div class="p-3 border rounded bg-gray-5"><h5 class="font-bold">${productName}</h5><select data-config-type="size" data-product-name="${productName}" class="pack-item-config-select block w-full mt-1 border-gray-300 rounded shadow-sm"><option value="">Taille</option>${sizeOptions}</select></div>`; } }); formHtml += `</div><div class="mt-6 flex justify-end"><button id="add-pack-btn" class="px-6 py-3 bg-indigo-600 text-white rounded shadow hover:bg-indigo-700">Ajouter Pack</button></div>`; } formHtml += `</div>`; }
    }

    dom.productFormContainer.innerHTML = formHtml;
    
    // --- GESTIONNAIRES D'EVENEMENTS SUR-MESURE ---
    const smContainer = document.getElementById('sur-mesure-fields');
    if (smContainer) {
        // Fonction pour mettre à jour la spécificité
        const updateSpecificityFromSM = () => {
            const inputs = smContainer.querySelectorAll('.sm-input');
            const parts = [];
            inputs.forEach(inp => {
                if (inp.value.trim() !== '') parts.push(`${inp.dataset.label}: ${inp.value}`);
            });
            const smString = parts.length > 0 ? `SUR-MESURE [${parts.join(', ')}]` : '';
            
            // On met à jour la textarea 'specificity' sans effacer les autres notes
            const specArea = document.getElementById('specificity');
            let currentText = specArea.value;
            if (currentText.includes('SUR-MESURE [')) {
                currentText = currentText.replace(/SUR-MESURE \[.*?\]/g, smString);
            } else if (smString !== '') {
                currentText = currentText ? `${currentText} | ${smString}` : smString;
            }
            specArea.value = currentText;
            state.currentSpecificity = currentText;
        };

        smContainer.querySelectorAll('.sm-input').forEach(inp => {
            inp.addEventListener('input', updateSpecificityFromSM);
        });

        const smQtyInput = document.getElementById('sm-qty-input');
        if (smQtyInput) {
            smQtyInput.addEventListener('input', (e) => {
                 state.currentQuantities = { 'SUR-MESURE': parseInt(e.target.value) || 1 };
                 calculateCurrentFormPrice();
                 updateButtonStates();
                 
                 // Mise à jour de l'affichage du prix sans tout redessiner
                 const priceDisplayContainer = document.querySelector('#add-button-step .bg-indigo-50'); 
                 if (priceDisplayContainer) {
                      const priceSpan = priceDisplayContainer.querySelector('.text-xl'); // Ou .text-lg selon votre CSS
                      if (priceSpan && state.currentCalculatedUnitPrice > 0) {
                           priceSpan.textContent = `${state.currentCalculatedUnitPrice.toFixed(2)}€`;
                      }
                 }
            });
        }

        const smCheck = document.getElementById('option-sur-mesure');
        if (smCheck) {
            smCheck.addEventListener('change', (e) => {
                if (e.target.checked) {
                    state.currentSelectedOptions.push('SUR-MESURE');
                    state.currentQuantities = { 'SUR-MESURE': parseInt(document.getElementById('sm-qty-input')?.value) || 1 };
                } else {
                    state.currentSelectedOptions = state.currentSelectedOptions.filter(o => o !== 'SUR-MESURE');
                    state.currentQuantities = {}; // On vide les quantités car on revient au mode standard
                    
                    const specArea = document.getElementById('specificity');
                    specArea.value = specArea.value.replace(/SUR-MESURE \[.*?\]/g, '').replace(/ \| $/, '').trim();
                    state.currentSpecificity = specArea.value;
                }
                // Important : on relance le rendu pour basculer les grilles
                calculateCurrentFormPrice();
                renderProductForm(); 
            });
        }
    }
    
    const marquageInput = document.getElementById('marquage-text');
    if (marquageInput && state.currentSpecificity.startsWith('NOM/PRENOM:')) {
        marquageInput.value = state.currentSpecificity.replace('NOM/PRENOM: ', '');
    }
    updateButtonStates();
};// ▲▲▲ FIN DU REMPLACEMENT COMPLET ▲▲▲// ▲▲▲ FIN DU REMPLACEMENT COMPLET ▲▲▲// ▲▲▲ FIN DU REMPLACEMENT COMPLET ▲▲▲// ▲▲▲ FIN DU REMPLACEMENT COMPLET ▲▲▲
// *** FIN DU BLOC À REMPLACER ***// =================================================================================
// --- STOCK MANAGEMENT ---
// =================================================================================

const saveStock = () => {
    if (!state.clubName) return;
    const stockKey = `stock_${state.clubName.replace(/[\s/\\?%*:|"<>]/g, '_')}`;
    localStorage.setItem(stockKey, JSON.stringify(state.clubStock));
    showToast('Stock sauvegardé !');
    renderDashboard();
};

const loadStock = () => {
    if (!state.clubName) {
        state.clubStock = {};
    } else {
        const stockKey = `stock_${state.clubName.replace(/[\s/\\?%*:|"<>]/g, '_')}`;
        const savedStock = localStorage.getItem(stockKey);
        state.clubStock = savedStock ? JSON.parse(savedStock) : {};
    }
    renderAll();
};
const savePacks = () => {
    if (!state.clubName) return;
    const packsKey = `packs_${state.clubName.replace(/[\s/\\?%*:|"<>]/g, '_')}`;
    localStorage.setItem(packsKey, JSON.stringify(state.clubPacks));
};

const loadPacks = () => {
    if (!state.clubName) {
        state.clubPacks = [];
    } else {
        const packsKey = `packs_${state.clubName.replace(/[\s/\\?%*:|"<>]/g, '_')}`;
        const savedPacks = localStorage.getItem(packsKey);
        state.clubPacks = savedPacks ? JSON.parse(savedPacks) : [];
    }
};

const showStockManagerModal = () => {
    if (!state.clubName) {
        showToast("Veuillez d'abord renseigner un nom de club.", 'error');
        return;
    }

    const container = document.createElement('div');
    container.className = 'space-y-4';
    container.innerHTML = `
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-4 pb-4 border-b border-gray-200">
            <button id="export-stock-btn" class="w-full px-4 py-2 bg-blue-600 text-white font-medium rounded-md hover:bg-blue-700">Exporter (JSON)</button>
            <label for="import-stock-input" class="w-full text-center cursor-pointer px-4 py-2 bg-gray-600 text-white font-medium rounded-md hover:bg-gray-700">Importer (JSON)</label>
            <button id="clear-stock-btn" class="w-full px-4 py-2 bg-red-600 text-white font-medium rounded-md hover:bg-red-700">Effacer le Stock</button>
        </div>
        <input type="text" id="stock-search-input" placeholder="Rechercher un produit..." class="w-full p-2 border rounded-md">
        <div id="stock-list-container" class="space-y-4 max-h-[50vh] overflow-y-auto pr-2"></div>
    `;

    const stockListContainer = container.querySelector('#stock-list-container');
    
    const renderStockList = (filter = '') => {
        stockListContainer.innerHTML = '';
        const productsWithSizes = allAvailableProducts.filter(p => p.category !== 'option' && (productSizes[p.sizeType || p.type] || p.sizeType === 'unique'));
        const filteredProducts = productsWithSizes.filter(p => p.name.toLowerCase().includes(filter.toLowerCase()));

        const grouped = filteredProducts.reduce((acc, p) => {
            const groupKey = `${p.category} - ${p.subtype}`;
            acc[groupKey] = [...(acc[groupKey] || []), p];
            return acc;
        }, {});

        for (const groupLabel in grouped) {
            const groupDiv = document.createElement('div');
            groupDiv.innerHTML = `<h4 class="font-bold text-gray-800 border-b pb-1 mb-2">${groupLabel}</h4>`;
            
            grouped[groupLabel].forEach(p => {
                const productDiv = document.createElement('div');
                productDiv.className = 'p-2 border-b';
                productDiv.innerHTML = `<p class="font-semibold">${p.name}</p>`;
                
                const sizes = productSizes[p.sizeType || p.type] || [];
                const sizeGrid = document.createElement('div');
                sizeGrid.className = 'grid grid-cols-3 sm:grid-cols-4 gap-x-4 gap-y-2 mt-2';
                
                sizes.forEach(size => {
                    const stockValue = state.clubStock[p.name]?.[size] ?? '';
                    sizeGrid.innerHTML += `
                        <div class="flex items-center gap-2">
                            <label class="text-sm w-16 text-right">${size}:</label>
                            <input type="number" class="stock-input w-20 rounded-md border-gray-300 shadow-sm text-center" 
                                   data-product-name="${p.name}" data-size="${size}" value="${stockValue}" placeholder="0">
                        </div>
                    `;
                });
                productDiv.appendChild(sizeGrid);
                groupDiv.appendChild(productDiv);
            });
            stockListContainer.appendChild(groupDiv);
        }
    };
    
    renderStockList();

    container.querySelectorAll('.stock-input').forEach(input => {
        input.addEventListener('input', (e) => {
            const { productName, size } = e.target.dataset;
            const qty = parseInt(e.target.value, 10);

            if (!state.clubStock[productName]) {
                state.clubStock[productName] = {};
            }

            if (!isNaN(qty) && qty > 0) {
                state.clubStock[productName][size] = qty;
            } else {
                delete state.clubStock[productName][size];
                if (Object.keys(state.clubStock[productName]).length === 0) {
                    delete state.clubStock[productName];
                }
            }
        });
    });

    container.querySelector('#export-stock-btn').addEventListener('click', handleExportStock);

    // ▼▼▼ DÉBUT DE LA MODIFICATION ▼▼▼
    container.querySelector('#clear-stock-btn').addEventListener('click', () => {
        const p = document.createElement('p');
        p.innerHTML = `Êtes-vous sûr de vouloir <strong>supprimer définitivement</strong> tout le stock pour le club <strong>${state.clubName}</strong> ?<br><br>Cette action est irréversible.`;
        showModal(dom.mainModal, "Confirmer la suppression du stock", p, [
            { label: 'Annuler', onClick: () => {
                hideModal(dom.mainModal);
                // On réaffiche la fenêtre de gestion du stock par dessus
                setTimeout(() => showStockManagerModal(), 50);
            }, className: 'bg-gray-400' },
            { label: 'Oui, tout effacer', onClick: () => {
                state.clubStock = {};
                saveStock(); // Sauvegarde le stock vide
                hideModal(dom.mainModal);
                showToast("Le stock a été effacé avec succès.", "success");
                // On réaffiche la fenêtre de gestion (maintenant vide)
                setTimeout(() => showStockManagerModal(), 50);
            }, className: 'bg-red-600'}
        ]);
    });
    // ▲▲▲ FIN DE LA MODIFICATION ▲▲▲

    container.querySelector('#stock-search-input').addEventListener('input', (e) => {
        renderStockList(e.target.value);
    });

    showModal(dom.mainModal, `Gestion du stock pour ${state.clubName}`, container, [
        { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-400' },
        { label: 'Enregistrer le stock', onClick: () => {
            document.querySelectorAll('.stock-input').forEach(input => {
                const { productName, size } = input.dataset;
                const qty = parseInt(input.value, 10);
                if (!state.clubStock[productName]) {
                    state.clubStock[productName] = {};
                }
                if (!isNaN(qty) && qty > 0) {
                    state.clubStock[productName][size] = qty;
                } else {
                    delete state.clubStock[productName][size];
                }
            });
            saveStock();
            hideModal(dom.mainModal);
        }, className: 'bg-green-600' }
    ], 'max-w-4xl');
};
const showVisualManagerModal = () => {
    if (!state.clubName) {
        showToast("Veuillez d'abord renseigner un nom de club.", 'error');
        return;
    }

    const renderManagerContent = () => {
        const container = document.createElement('div');
        container.className = 'space-y-4';
        
        container.innerHTML = `
            <p class="text-sm text-gray-600">Gérez ici les différents noms de visuels pour le club <strong>${state.clubName}</strong>. Ils seront ensuite disponibles lors de la saisie des articles.</p>
            <div class="flex gap-2 pt-4 border-t">
                <input type="text" id="new-visual-name" placeholder="Ajouter un nom de visuel..." class="block w-full rounded-md border-gray-300 shadow-sm">
                <button id="add-new-visual-btn" class="px-4 py-2 bg-indigo-600 text-white rounded-md hover:bg-indigo-700">Ajouter</button>
            </div>
            <div id="visual-list-container" class="space-y-2 max-h-40 overflow-y-auto"></div>
        `;

        const listContainer = container.querySelector('#visual-list-container');
        state.clubVisuals.sort((a,b) => a.localeCompare(b)).forEach(name => {
            const itemDiv = document.createElement('div');
            itemDiv.className = 'flex justify-between items-center p-2 border rounded-md bg-gray-50';
            itemDiv.innerHTML = `<span>${name}</span>
                                 <button data-visual-name="${name}" class="ml-4 text-xs bg-red-500 text-white px-2 py-1 rounded hover:bg-red-600">Supprimer</button>`;
            listContainer.appendChild(itemDiv);
        });

        container.querySelector('#add-new-visual-btn').onclick = () => {
            const input = container.querySelector('#new-visual-name');
            const newName = input.value.trim();
            if (newName && !state.clubVisuals.includes(newName)) {
                state.clubVisuals.push(newName);
                dom.mainModalBody.innerHTML = '';
                dom.mainModalBody.appendChild(renderManagerContent());
            }
            input.value = '';
            input.focus();
        };
        
        listContainer.querySelectorAll('button').forEach(btn => {
            btn.onclick = () => {
                state.clubVisuals = state.clubVisuals.filter(v => v !== btn.dataset.visualName);
                dom.mainModalBody.innerHTML = '';
                dom.mainModalBody.appendChild(renderManagerContent());
            };
        });

        return container;
    };

    showModal(dom.mainModal, 'Gérer les Visuels du Club', renderManagerContent(), [
        { label: 'Fermer', onClick: () => {
            hideModal(dom.mainModal);
            const visualsKey = `visuals_${state.clubName.replace(/[\s/\\?%*:|"<>]/g, '_')}`;
            localStorage.setItem(visualsKey, JSON.stringify(state.clubVisuals));
            renderProductForm();
        }, className: 'bg-gray-500' }
    ]);
};
const showClubRangeSelectorModal = () => {
    if (!state.clubName.trim() || !state.departement.trim()) {
        showToast("Veuillez d'abord renseigner le Nom du Club et le Département.", 'error');
        return;
    }

    const container = document.createElement('div');
    container.className = 'space-y-4';

    let showOnlyRange = state.clubProductRange.length > 0;

    const renderContent = () => {
        container.innerHTML = ''; 

        // --- Boutons Filtres ---
        if (state.clubProductRange.length > 0) {
            container.innerHTML += `
                <div class="flex justify-center gap-4 mb-4 p-2 bg-gray-100 rounded-lg">
                    <button id="show-range-btn" class="px-4 py-2 text-sm rounded-md ${showOnlyRange ? 'bg-indigo-600 text-white' : 'bg-white text-gray-700'}">Afficher la Gamme (${state.clubProductRange.length})</button>
                    <button id="show-all-btn" class="px-4 py-2 text-sm rounded-md ${!showOnlyRange ? 'bg-indigo-600 text-white' : 'bg-white text-gray-700'}">Afficher Tout</button>
                </div>
            `;
        }

        // --- Actions ---
        container.innerHTML += `
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-2 pb-4 border-b">
                <button id="export-range-btn" class="w-full px-4 py-2 bg-blue-600 text-white font-medium rounded-md hover:bg-blue-700">Exporter la Gamme</button>
                <label for="import-club-range-input" class="w-full text-center cursor-pointer px-4 py-2 bg-gray-600 text-white font-medium rounded-md hover:bg-gray-700">Importer une Gamme</label>
                <button id="clear-range-btn" class="w-full px-4 py-2 bg-red-600 text-white font-medium rounded-md hover:bg-red-700">Tout Effacer</button>
            </div>
        `;
        
        // --- BARRE D'OUTILS REMISE ---
        container.innerHTML += `
            <div class="flex justify-between items-center bg-yellow-50 p-2 rounded mb-2 border border-yellow-100">
                <span class="text-xs text-yellow-800 font-bold flex items-center">
                   <span class="mr-2 text-lg">💡</span> Gestion des Remises (%)
                </span>
                <div class="space-x-2">
                    <button id="btn-check-all-discount" class="text-xs bg-white border border-yellow-300 text-yellow-700 px-2 py-1 rounded hover:bg-yellow-100">Tout Cocher</button>
                    <button id="btn-uncheck-all-discount" class="text-xs bg-white border border-yellow-300 text-yellow-700 px-2 py-1 rounded hover:bg-yellow-100">Tout Décocher</button>
                </div>
            </div>
        `;

        // --- Liste Produits ---
        const sourceProducts = showOnlyRange
            ? allAvailableProducts.filter(p => state.clubProductRange.some(rangeItem => rangeItem.name === p.name))
            : allAvailableProducts;
        
        const productsToShow = sourceProducts.filter(p => p.category !== 'option');

        const grouped = productsToShow.reduce((acc, p) => {
            const groupKey = `${p.category} - ${p.subtype}`;
            acc[groupKey] = [...(acc[groupKey] || []), p];
            return acc;
        }, {});

        let contentHtml = '<div class="space-y-3 max-h-[50vh] overflow-y-auto pr-2">';
        
        for (const groupLabel in grouped) {
            contentHtml += `<div class="pt-2"><h4 class="font-bold text-gray-800 border-b pb-1 mb-2">${groupLabel}</h4>`;
            
            grouped[groupLabel].forEach(p => {
                const rangeItem = state.clubProductRange.find(item => item.name === p.name);
                const isChecked = !!rangeItem;
                const licenseePrice = rangeItem ? rangeItem.licenseePrice : null;
                const priceValue = (licenseePrice !== null && licenseePrice !== undefined) ? licenseePrice.toFixed(2) : '';
                
                const hasFixedPrice = (priceValue !== '' && parseFloat(priceValue) > 0);

                const isDiscountAllowed = rangeItem ? (rangeItem.allowDiscount === true) : false;
                const isPriceDisabled = !isChecked || isDiscountAllowed;

                // --- LOGIQUE FILTRES ---
                const isBottom = p.isCuissardOrCollant === true || p.subtype === 'Bas' || p.subtype.includes('Cuissard') || p.subtype.includes('Collant') || p.subtype.includes('Short');
                const isAccessory = p.type === 'accessoire' || p.category === 'ACCESSOIRES';
                const isCombination = p.name.includes('COMBINAISON'); 

                // 1. Logique Poche Zip
                const hasZipOption = !isBottom && !isAccessory && !isCombination && p.hasOptions !== false && !p.excludedOptions?.includes('POCHE DOS ZIPPEE');
                const isZipChecked = rangeItem && rangeItem.defaultOptions && rangeItem.defaultOptions.includes('POCHE DOS ZIPPEE');

                // 2. Logique Coloris
                const savedColor = rangeItem ? (rangeItem.defaultColor || '') : '';
                let colorInputHtml = '';
                
                let availableColors = [];
                if (p.colors && p.colors.length > 0) availableColors = p.colors;
                else if (state.clubVisuals && state.clubVisuals.length > 0) availableColors = state.clubVisuals;

                if (availableColors.length > 0) {
                    const options = availableColors.map(c => `<option value="${c}" ${c === savedColor ? 'selected' : ''}>${c}</option>`).join('');
                    colorInputHtml = `<select id="def-color-${sanitizeForId(p.name)}" class="range-color-input w-24 rounded-md border-gray-300 shadow-sm text-xs py-1" ${isChecked ? '' : 'disabled'}><option value="">Coloris...</option>${options}</select>`;
                } else {
                    colorInputHtml = `<input type="text" id="def-color-${sanitizeForId(p.name)}" class="range-color-input w-24 rounded-md border-gray-300 shadow-sm text-xs py-1" value="${savedColor}" placeholder="Coloris..." ${isChecked ? '' : 'disabled'}>`;
                }

                const sanitizedId = sanitizeForId(p.name);
                const checkboxId = `range-prod-${sanitizedId}`;
                const priceInputId = `price-lic-${sanitizedId}`;
                const zipCheckboxId = `opt-zip-${sanitizedId}`;
                const discountCheckboxId = `opt-disc-${sanitizedId}`;

                contentHtml += `
                    <div class="flex items-center justify-between my-1 py-1 border-b border-gray-100 gap-2 hover:bg-gray-50 transition-colors">
                        <div class="flex items-center flex-grow min-w-0">
                            <input id="${checkboxId}" type="checkbox" data-product-name="${p.name}" class="range-product-checkbox h-4 w-4 rounded border-gray-300 text-indigo-600 flex-shrink-0" ${isChecked ? 'checked' : ''}>
                            <label for="${checkboxId}" class="ml-3 block text-sm text-gray-900 truncate cursor-pointer" title="${p.name}">${p.name}</label> 
                        </div>

                        <div class="flex items-center gap-2">
                            ${colorInputHtml}

                            <div class="flex items-center bg-yellow-50 px-2 py-1 rounded border border-yellow-200" title="Activer la remise % sur cet article">
                                <input id="${discountCheckboxId}" type="checkbox" data-prod-id="${sanitizedId}" class="range-discount-checkbox h-3 w-3 rounded border-gray-300 text-yellow-600 cursor-pointer" 
                                       ${isDiscountAllowed ? 'checked' : ''} 
                                       ${isChecked && !hasFixedPrice ? '' : 'disabled'}>
                                <label for="${discountCheckboxId}" class="ml-1 text-xs text-gray-700 font-bold cursor-pointer">%</label>
                            </div>

                            ${hasZipOption ? `
                                <div class="flex items-center bg-gray-50 px-2 py-1 rounded border border-gray-200">
                                    <input id="${zipCheckboxId}" type="checkbox" class="range-zip-checkbox h-3 w-3 rounded border-gray-300 text-blue-600" ${isZipChecked ? 'checked' : ''} ${isChecked ? '' : 'disabled'}>
                                    <label for="${zipCheckboxId}" class="ml-1 text-xs text-gray-500">Zip</label>
                                </div>
                            ` : ''}

                            <div class="flex items-center w-24 justify-end" title="Prix Fixe Licencié (Désactivé si Remise cochée)">
                                <input type="number" step="0.01" min="0" id="${priceInputId}" data-prod-id="${sanitizedId}"
                                       class="range-licensee-price w-16 rounded-md border-gray-300 shadow-sm text-sm p-1 text-right ${isPriceDisabled ? 'bg-gray-200 text-gray-400' : 'bg-white font-bold text-gray-900'}" 
                                       value="${priceValue}" placeholder="-" ${isPriceDisabled ? 'disabled' : ''}>
                                <span class="ml-1 text-xs text-gray-500">€</span>
                            </div>
                        </div>
                    </div>`;
            });
            contentHtml += `</div>`;
        }
        contentHtml += `</div>`;
        container.innerHTML += contentHtml;

        // --- GESTIONNAIRES ---

        const handleBulkDiscount = (check) => {
            container.querySelectorAll('.range-discount-checkbox').forEach(chk => {
                if (!chk.disabled) { 
                    chk.checked = check;
                    chk.dispatchEvent(new Event('change'));
                }
            });
        };

        container.querySelector('#btn-check-all-discount').addEventListener('click', () => handleBulkDiscount(true));
        container.querySelector('#btn-uncheck-all-discount').addEventListener('click', () => handleBulkDiscount(false));

        container.querySelectorAll('.range-discount-checkbox').forEach(chk => {
            chk.addEventListener('change', (e) => {
                const prodId = e.target.dataset.prodId;
                const priceInput = container.querySelector(`#price-lic-${prodId}`);
                
                if (e.target.checked) {
                    if (priceInput) {
                        priceInput.disabled = true;
                        priceInput.classList.add('bg-gray-200', 'text-gray-400');
                        priceInput.classList.remove('bg-white', 'font-bold', 'text-gray-900');
                    }
                } else {
                    if (priceInput) {
                        priceInput.disabled = false;
                        priceInput.classList.remove('bg-gray-200', 'text-gray-400');
                        priceInput.classList.add('bg-white', 'font-bold', 'text-gray-900');
                    }
                }
            });
        });

        // 3. Gestion Globale NON DESTRUCTIVE (La correction est ici)
        container.querySelectorAll('.range-product-checkbox').forEach(checkbox => {
            checkbox.addEventListener('change', (e) => {
                const sanitizedId = sanitizeForId(e.target.dataset.productName);
                const priceInput = container.querySelector(`#price-lic-${sanitizedId}`);
                const zipCheckbox = container.querySelector(`#opt-zip-${sanitizedId}`);
                const colorInput = container.querySelector(`#def-color-${sanitizedId}`);
                const discCheckbox = container.querySelector(`#opt-disc-${sanitizedId}`);
                
                const isEnabled = e.target.checked;

                // ON NE VIDE PLUS LES VALEURS, ON LES DÉSACTIVE JUSTE
                if (zipCheckbox) { zipCheckbox.disabled = !isEnabled; }
                if (colorInput) { colorInput.disabled = !isEnabled; }
                if (discCheckbox) { discCheckbox.disabled = !isEnabled; }

                if (priceInput) {
                    if (!isEnabled) {
                        priceInput.disabled = true;
                        priceInput.classList.add('bg-gray-200', 'text-gray-400');
                        priceInput.classList.remove('bg-white', 'font-bold', 'text-gray-900');
                    } else {
                        // Si on réactive la ligne, on vérifie si la remise était cochée
                        if (discCheckbox && discCheckbox.checked) {
                            priceInput.disabled = true;
                        } else {
                            priceInput.disabled = false;
                            priceInput.classList.remove('bg-gray-200', 'text-gray-400');
                            priceInput.classList.add('bg-white', 'font-bold', 'text-gray-900');
                        }
                    }
                }
            });
        });

        const showRangeBtn = container.querySelector('#show-range-btn');
        if (showRangeBtn) showRangeBtn.onclick = () => { showOnlyRange = true; renderContent(); };
        const showAllBtn = container.querySelector('#show-all-btn');
        if (showAllBtn) showAllBtn.onclick = () => { showOnlyRange = false; renderContent(); };
        
        const clearRangeBtn = container.querySelector('#clear-range-btn');
        if (clearRangeBtn) clearRangeBtn.onclick = () => {
             if (confirm("Effacer toute la gamme ?")) {
                 state.clubProductRange = [];
                 localStorage.removeItem(`range_${state.clubName.replace(/[\s/\\?%*:|"<>]/g, '_')}`);
                 showOnlyRange = false;
                 renderContent();
             }
        };
        const exportRangeBtn = container.querySelector('#export-range-btn');
        if (exportRangeBtn) exportRangeBtn.onclick = handleExportClubRange;
    };

    renderContent();

    showModal(dom.mainModal, `Gamme pour ${state.clubName}`, container, [
        { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-400' },
        {
            label: 'Enregistrer la Gamme',
            onClick: () => {
                const newClubProductRange = [];
                container.querySelectorAll('.range-product-checkbox').forEach(checkbox => {
                    if (checkbox.checked) {
                        const productName = checkbox.dataset.productName;
                        const sanitizedId = sanitizeForId(productName);
                        
                        const priceInput = container.querySelector(`#price-lic-${sanitizedId}`);
                        let licenseePrice = null;
                        
                        // On ne prend le prix que s'il est activé et rempli (ou si la case remise est décochée)
                        const discCheckbox = container.querySelector(`#opt-disc-${sanitizedId}`);
                        const allowDiscount = discCheckbox ? discCheckbox.checked : false;

                        // Si remise active, on ignore le prix (même s'il y a une valeur résiduelle)
                        if (!allowDiscount && priceInput && priceInput.value.trim() !== '') {
                            const parsedPrice = parseFloat(priceInput.value);
                            if (!isNaN(parsedPrice) && parsedPrice >= 0) licenseePrice = parsedPrice;
                        }

                        const zipCheckbox = container.querySelector(`#opt-zip-${sanitizedId}`);
                        const defaultOptions = [];
                        if (zipCheckbox && zipCheckbox.checked) defaultOptions.push('POCHE DOS ZIPPEE');

                        const colorInput = container.querySelector(`#def-color-${sanitizedId}`);
                        const defaultColor = colorInput ? colorInput.value.trim() : '';
                        
                        const existingItem = state.clubProductRange.find(r => r.name === productName);

                        newClubProductRange.push({ 
                            name: productName, 
                            licenseePrice: licenseePrice, 
                            defaultOptions: defaultOptions,
                            defaultColor: defaultColor,
                            allowDiscount: allowDiscount, 
                            
                            markingType: existingItem ? existingItem.markingType : null,
                            isIncludedInRestock: existingItem ? existingItem.isIncludedInRestock : true,
                            lastRestockPrice: existingItem ? existingItem.lastRestockPrice : undefined,
                            lastRestockRefQty: existingItem ? existingItem.lastRestockRefQty : undefined
                        });
                    }
                });

                state.clubProductRange = newClubProductRange;
                const rangeKey = `range_${state.clubName.replace(/[\s/\\?%*:|"<>]/g, '_')}`;
                localStorage.setItem(rangeKey, JSON.stringify(state.clubProductRange));

                hideModal(dom.mainModal);
                showToast("Gamme mise à jour !");
                state.showAllProducts = false;
                
                // IMPORTANT : On rafraichit les remises existantes
                refreshExistingCartDiscounts();
                
                renderProductForm();
            },
            className: 'bg-green-600'
        }
    ], 'max-w-5xl');
};const showPackManagerModal = () => {
    if (!state.clubName) {
        showToast("Veuillez d'abord renseigner un nom de club.", 'error');
        return;
    }

    const renderManagerContent = () => {
        const container = document.createElement('div');
        container.className = 'space-y-4';
        
        container.innerHTML = `
            <div class="p-4 border rounded-lg bg-gray-50 space-y-3">
                <h4 class="font-bold text-lg text-gray-800">Créer ou Modifier un Pack</h4>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-3">
                    <div>
                        <label for="new-pack-name" class="block text-sm font-medium text-gray-700">Nom du Pack</label>
                        <input type="text" id="new-pack-name" placeholder="Ex: PACK ADULTE" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                    </div>
                    <div>
                        <label for="new-pack-price" class="block text-sm font-medium text-gray-700">Prix TTC (€)</label>
                        <input type="number" id="new-pack-price" placeholder="150.00" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                    </div>
                    <div class="md:self-end">
                         <button id="create-pack-btn" class="w-full px-4 py-2 bg-green-600 text-white rounded-md hover:bg-green-700">Ajouter ce Pack</button>
                    </div>
                </div>
            </div>
            <div id="pack-list-container" class="space-y-3 max-h-[40vh] overflow-y-auto p-1"></div>
        `;

        const packListContainer = container.querySelector('#pack-list-container');
        
        state.clubPacks.forEach((pack, index) => {
            const packDiv = document.createElement('div');
            packDiv.className = 'p-4 border rounded-lg';
          // NOUVEAU BLOC À COLLER
packDiv.innerHTML = `
    <div class="flex justify-between items-start">
        <div>
            <h5 class="font-bold text-indigo-700">${pack.name}</h5>
            <p class="text-sm text-gray-600">Prix TTC actuel : <strong id="pack-price-display-${index}">${pack.priceTTC.toFixed(2)}€</strong></p>
        </div>
        <button data-action="delete-pack" data-index="${index}" class="text-xs bg-red-500 text-white px-2 py-1 rounded hover:bg-red-600">Supprimer</button>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-3 gap-3 mt-4 p-3 bg-gray-100 rounded-md">
               <div class="self-end">
            <button data-action="calculate-pack-price" data-index="${index}" class="w-full px-4 py-2 bg-blue-600 text-white text-sm rounded-md hover:bg-blue-700">Calculer le prix du pack</button>
        </div>
        <div>
            <label for="new-pack-price-${index}" class="block text-xs font-medium text-gray-700">Prix TTC final (éditable)</label>
            <input type="number" id="new-pack-price-${index}" value="${pack.priceTTC.toFixed(2)}" class="pack-price-input mt-1 block w-full rounded-md border-gray-300 shadow-sm">
        </div>
    </div>

    <div class="mt-4">
        <label class="block text-sm font-medium text-gray-700 mb-2">Articles inclus dans ce pack :</label>
        <div class="grid grid-cols-1 md:grid-cols-2 gap-x-4 gap-y-1">
            ${allAvailableProducts
                .filter(p => p.category !== 'option')
                .map(p => `
                    <div class="flex items-center">
                        <input type="checkbox" id="pack-${index}-prod-${p.name.replace(/[^a-zA-Z0-9]/g, '_')}" data-action="toggle-pack-product" data-pack-index="${index}" data-product-name="${p.name}" class="h-4 w-4 rounded border-gray-300 text-indigo-600" ${pack.productNames.includes(p.name) ? 'checked' : ''}>
                        <label for="pack-${index}-prod-${p.name.replace(/[^a-zA-Z0-9]/g, '_')}" class="ml-2 text-sm text-gray-800">${p.name}</label>
                    </div>
                `).join('')
            }
        </div>
    </div>
`;
            packListContainer.appendChild(packDiv);
        });
        return container;
    };

    showModal(dom.mainModal, `Gestion des Packs pour ${state.clubName}`, renderManagerContent(), [
        { label: 'Fermer et Enregistrer', onClick: () => {
            savePacks();
            hideModal(dom.mainModal);
            showToast("Les packs ont été enregistrés.");
        }, className: 'bg-indigo-600' }
    ], 'max-w-4xl');

    const modalBody = dom.mainModalBody;
    modalBody.addEventListener('click', e => {
        const target = e.target;
        const action = target.dataset.action;

        if (target.id === 'create-pack-btn') {
            const nameInput = modalBody.querySelector('#new-pack-name');
            const priceInput = modalBody.querySelector('#new-pack-price');
            const name = nameInput.value.trim();
            const price = parseFloat(priceInput.value);

            if (name && !isNaN(price) && price > 0) {
                if (state.clubPacks.some(p => p.name.toLowerCase() === name.toLowerCase())) {
                    showToast("Un pack avec ce nom existe déjà.", "error");
                    return;
                }
                state.clubPacks.push({ name: name, priceTTC: price, productNames: [] });
                modalBody.innerHTML = '';
                modalBody.appendChild(renderManagerContent());
            } else {
                showToast("Veuillez saisir un nom et un prix valide.", 'error');
            }
        }
 else if (action === 'calculate-pack-price') {
    e.preventDefault();
    const index = parseInt(target.dataset.index, 10);
    const pack = state.clubPacks[index];
    if (!pack) return;

    // On utilise maintenant la quantité de référence globale de l'état
    const estimatedQuantity = state.packBaseQuantity;

    if (!estimatedQuantity || estimatedQuantity <= 0) {
        showToast("Veuillez d'abord définir une 'Quantité de Réf. pour Packs' dans les informations du document.", 'error');
        return;
    }

        let calculatedPriceTTC = 0;
        let missingPricing = false;

        pack.productNames.forEach(productName => {
            const product = productMap.get(productName);
            if (product && product.pricingTiers) {
                // On utilise la fonction existante pour trouver le prix de chaque article pour la quantité estimée
                calculatedPriceTTC += getPriceForQuantity(product.pricingTiers, estimatedQuantity);
            } else if (product && product.price) {
                // Pour les articles à prix fixe (accessoires permanents)
                 calculatedPriceTTC += product.price;
            } else {
                missingPricing = true;
            }
        });

        if (missingPricing) {
            showToast("Certains articles du pack n'ont pas de grille tarifaire et n'ont pas pu être inclus dans le calcul.", "error");
        }

        // On met à jour le champ de saisie du prix et l'affichage
        const priceInput = modalBody.querySelector(`#new-pack-price-${index}`);
        const priceDisplay = modalBody.querySelector(`#pack-price-display-${index}`);
        
        priceInput.value = calculatedPriceTTC.toFixed(2);
        priceDisplay.textContent = `${calculatedPriceTTC.toFixed(2)}€`;
        
        // On met aussi à jour le prix dans l'état de l'application
        state.clubPacks[index].priceTTC = calculatedPriceTTC;

        showToast(`Prix du pack calculé pour une quantité de ${estimatedQuantity} : ${calculatedPriceTTC.toFixed(2)}€`);
    }
        
        if (action === 'delete-pack') {
            if (confirm("Êtes-vous sûr de vouloir supprimer ce pack ?")) {
                state.clubPacks.splice(target.dataset.index, 1);
                modalBody.innerHTML = '';
                modalBody.appendChild(renderManagerContent());
            }
        }
    });
 // Il faut aussi mettre à jour le prix si l'utilisateur le modifie manuellement
    modalBody.addEventListener('input', e => {
        if (e.target.classList.contains('pack-price-input')) {
            const index = parseInt(e.target.id.split('-').pop(), 10);
            const newPrice = parseFloat(e.target.value) || 0;
            if (state.clubPacks[index]) {
                state.clubPacks[index].priceTTC = newPrice;
                const priceDisplay = modalBody.querySelector(`#pack-price-display-${index}`);
                if(priceDisplay) { // Sécurité pour éviter les erreurs
                    priceDisplay.textContent = `${newPrice.toFixed(2)}€`;
                }
            }
        }
    });

    modalBody.addEventListener('change', e => {
        const target = e.target;
        if (target.dataset.action === 'toggle-pack-product') {
            const { packIndex, productName } = target.dataset;
            const pack = state.clubPacks[packIndex];
            if (target.checked) {
                if (!pack.productNames.includes(productName)) {
                    pack.productNames.push(productName);
                }
            } else {
                pack.productNames = pack.productNames.filter(name => name !== productName);
            }
        }
    });
};
const showArticleDetailModal = (subtype) => {
    // ▼▼▼ DÉBUT DE LA MODIFICATION ▼▼▼
    // On filtre différemment si on a cliqué sur "Accessoires"
    const relevantItems = state.currentOrderLineItems.filter(item => {
        const product = productMap.get(item.productName);
        if (!product) return false;

        if (subtype === 'Accessoires') {
            // Cas spécial : on regroupe tous les accessoires
            return product.category === 'ACCESSOIRES';
        } else {
            // Cas normal : on filtre par sous-type
            return product.subtype === subtype;
        }
    });
    // ▲▲▲ FIN DE LA MODIFICATION ▲▲▲

    const totalsByName = relevantItems.reduce((acc, item) => {
        acc[item.productName] = (acc[item.productName] || 0) + item.totalQuantity;
        return acc;
    }, {});

    const sortedNames = Object.keys(totalsByName).sort();
    const content = document.createElement('div');
    
    let tableHtml = `<table class="min-w-full text-left text-sm">
                        <thead class="border-b">
                            <tr>
                                <th class="font-semibold p-2">Article (cliquable pour voir les licenciés)</th>
                                <th class="font-semibold p-2 text-right">Quantité</th>
                            </tr>
                        </thead>
                        <tbody>`;
    
    sortedNames.forEach(name => {
        tableHtml += `<tr class="border-b hover:bg-indigo-50 cursor-pointer clickable-article-row" data-product-name="${name}">
                <td class="p-2">${name}</td>
                <td class="p-2 text-right font-bold">${totalsByName[name]}</td>
              </tr>`;
    });
    
    tableHtml += `</tbody></table>`;
    content.innerHTML = tableHtml;

    const onModalOpen = () => {
        dom.mainModalBody.querySelectorAll('.clickable-article-row').forEach(row => {
            row.addEventListener('click', () => {
                const productName = row.dataset.productName;
                const itemsForThisProduct = state.currentOrderLineItems.filter(item => item.productName === productName);
                const licensees = itemsForThisProduct.reduce((acc, item) => {
                    const licensee = item.licencieName;
                    if (licensee && licensee !== 'Commande Globale' && licensee !== 'Stock Club') {
                        acc[licensee] = (acc[licensee] || 0) + item.totalQuantity;
                    }
                    return acc;
                }, {});
                const sortedLicensees = Object.keys(licensees).sort();
                const licenseeContent = document.createElement('div');

                if (sortedLicensees.length > 0) {
                    let licenseeTableHtml = `<table class="min-w-full text-left text-sm">
                                            <thead class="border-b"><tr><th class="font-semibold p-2">Licencié (cliquable)</th><th class="font-semibold p-2 text-right">Quantité</th></tr></thead>
                                            <tbody>`;
                    sortedLicensees.forEach(name => {
                        licenseeTableHtml += `<tr class="border-b hover:bg-indigo-50 cursor-pointer clickable-licensee-row" data-licensee-name="${name}">
                                                <td class="p-2">${name}</td>
                                                <td class="p-2 text-right font-bold">${licensees[name]}</td>
                                           </tr>`;
                    });
                    licenseeTableHtml += `</tbody></table>`;
                    licenseeContent.innerHTML = licenseeTableHtml;
                } else {
                    licenseeContent.innerHTML = `<p class="text-center text-gray-500 p-4">Cet article a été commandé en mode global ou pour le stock.</p>`;
                }

                const onLicenseeModalOpen = () => {
                    dom.mainModalBody.querySelectorAll('.clickable-licensee-row').forEach(licenseeRow => {
                        licenseeRow.addEventListener('click', () => {
                            const licenseeName = licenseeRow.dataset.licenseeName;
                            if (licenseeName) {
                                hideModal(dom.mainModal); 
                                scrollToLicensee(licenseeName);
                            }
                        });
                    });
                };

                showModal(dom.mainModal, `Licenciés pour : ${productName}`, licenseeContent, [
                    {
                        label: 'Retour',
                        className: 'bg-gray-500 hover:bg-gray-600 text-white',
                        onClick: () => {
                            showArticleDetailModal(subtype);
                        }
                    }
                ], 'max-w-md', onLicenseeModalOpen);
            });
        });
    };

    showModal(dom.mainModal, `Détail pour : ${subtype}`, content, [], 'max-w-md', onModalOpen);
};// =================================================================================
// --- EVENT HANDLERS & LOGIC ---
// =================================================================================
// ▼▼▼ NOUVELLES FONCTIONS POUR LA SAISIE DÉCALÉE ▼▼▼

const handleAddSplitSizeFromModal = () => {
    const product = productMap.get(state.currentProduct);
    if (!product) return;

    const topSize = document.getElementById('modal-split-size-top').value;
    const bottomSize = document.getElementById('modal-split-size-bottom').value;
    const qty = parseInt(document.getElementById('modal-split-quantity').value, 10) || 0;

    if (!topSize || !bottomSize || qty === 0) {
        showToast("Veuillez sélectionner les deux tailles et une quantité.", 'error');
        return; // Ne pas fermer la modale
    }

    const sizes = productSizes[product.sizeType || 'aero'];
    const topIndex = sizes.indexOf(topSize);
    const bottomIndex = sizes.indexOf(bottomSize);

    if (Math.abs(topIndex - bottomIndex) > 1) {
        showToast("L'écart entre les tailles ne peut pas dépasser 1.", 'error');
        return; // Ne pas fermer la modale
    }

    // Préparation du nouvel article
    const quantitiesPerSize = { [`Haut ${topSize} / Bas ${bottomSize}`]: qty };
    const totalQuantity = qty;
    // On récupère les autres options cochées et on s'assure que la majoration est incluse
    let finalOptions = [...state.currentSelectedOptions];
    if (!finalOptions.includes('Majoration Taille Décalée')) {
        finalOptions.push('Majoration Taille Décalée');
    }

    const licensee = (state.orderScope === 'licensee' && state.activeLicensee) ? state.activeLicensee : 'Commande Globale';
    
    const existingItem = state.currentOrderLineItems.find(item =>
        item.productName === product.name &&
        item.licencieName === licensee &&
        JSON.stringify(item.options) === JSON.stringify(finalOptions) &&
        item.specificity === state.currentSpecificity &&
        item.visual === state.currentVisual
    );

    if (existingItem) {
        const sizeKey = Object.keys(quantitiesPerSize)[0];
        existingItem.quantitiesPerSize[sizeKey] = (existingItem.quantitiesPerSize[sizeKey] || 0) + qty;
        existingItem.totalQuantity += qty;
    } else {
        state.currentOrderLineItems.push({
            id: Date.now() + Math.random(),
            productName: product.name,
            quantitiesPerSize,
            totalQuantity,
            options: finalOptions,
            specificity: state.currentSpecificity,
            isFromStock: false, isStockOrder: false,
            licencieName: licensee,
            isManualPrice: false, initialManualPrice: 0,
            visual: state.currentVisual,
            applyDiscount: true,
        });
    }

    hideModal(dom.mainModal);
    renderAll();
    showToast('Taille décalée ajoutée avec succès.');
};

const showSplitSizingModal = () => {
    const product = productMap.get(state.currentProduct);
    if (!product) {
        showToast("Veuillez d'abord sélectionner un produit.", 'error');
        return;
    }
     if (state.orderScope === 'licensee' && !state.activeLicensee) {
        showToast("Veuillez sélectionner un licencié avant d'ajouter une taille décalée.", 'error');
        return;
    }

    const container = document.createElement('div');
    container.className = 'space-y-4';
    const sizes = productSizes[product.sizeType || 'aero'] || [];
    const sizeOptionsHtml = sizes.map(size => `<option value="${size}">${size}</option>`).join('');

    container.innerHTML = `
        <p class="text-sm">Veuillez sélectionner la taille pour le haut et le bas, ainsi que la quantité. Une majoration de 6€ HT sera appliquée.</p>
        <div>
            <label for="modal-split-size-top" class="block text-sm font-medium text-gray-700">Taille Haut</label>
            <select id="modal-split-size-top" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                <option value="">-- Choisir --</option>
                ${sizeOptionsHtml}
            </select>
        </div>
        <div>
            <label for="modal-split-size-bottom" class="block text-sm font-medium text-gray-700">Taille Bas</label>
            <select id="modal-split-size-bottom" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                <option value="">-- Choisir --</option>
                ${sizeOptionsHtml}
            </select>
        </div>
        <div>
            <label for="modal-split-quantity" class="block text-sm font-medium text-gray-700">Quantité</label>
            <input type="number" id="modal-split-quantity" value="1" min="1" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
        </div>
    `;

    showModal(dom.mainModal, `Saisie Taille Décalée`, container, [
        { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-400' },
        { label: 'Valider', onClick: handleAddSplitSizeFromModal, className: 'bg-indigo-600' }
    ]);
};
const handleExportStock = () => {
    if (!state.clubName) {
        showToast("Veuillez sélectionner un club pour exporter son stock.", "error");
        return;
    }
    if (Object.keys(state.clubStock).length === 0) {
        showToast("Le stock est vide, rien à exporter.", "error");
        return;
    }

    // Création d'un objet structuré pour l'export
    const exportData = {
        nomClub: state.clubName,
        dateEnregistrement: new Date().toISOString().split('T')[0],
        stock: state.clubStock
    };

    try {
let baseName = state.clubName.replace(/[^a-zA-Z0-9]/g, '_'); // Par défaut : Nom du club nettoyé
        
        // Si un code est connecté (ex: ACSP56), on l'utilise en priorité
        if (window.currentClubCode) {
            baseName = window.currentClubCode; 
        }

        const jsonFilename = `COMMANDE_${baseName}_${todayStr}.json`;
        const dataStr = JSON.stringify(exportData, null, 2);
        const blob = new Blob([dataStr], { type: "application/json" });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = `stock_${state.clubName.replace(/ /g, '_')}.json`;
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        URL.revokeObjectURL(url);
        showToast('Fichier de stock exporté avec succès !');
    } catch (error) {
        console.error("Erreur lors de l'export du stock", error);
        showToast("Une erreur est survenue lors de l'exportation.", 'error');
    }
};
const handleImportStock = (event) => {
    const file = event.target.files[0];
    if (!file) return;

    const reader = new FileReader();
    reader.onload = (e) => {
        try {
            const loadedData = JSON.parse(e.target.result);
            let stockToLoad;
            let fileInfo = '';

            // Vérifie si le fichier a la nouvelle structure (avec date) ou l'ancienne
            if (loadedData.stock && loadedData.dateEnregistrement) {
                stockToLoad = loadedData.stock;
                fileInfo = ` (Fichier de ${loadedData.nomClub} du ${loadedData.dateEnregistrement})`;
            } else {
                stockToLoad = loadedData; // Compatibilité avec l'ancien format
            }

            if (typeof stockToLoad !== 'object' || stockToLoad === null || Array.isArray(stockToLoad)) {
                throw new Error("Les données de stock dans le fichier ne sont pas valides.");
            }

            const p = document.createElement('p');
            p.innerHTML = `Êtes-vous sûr de vouloir remplacer le stock actuel par le contenu de ce fichier${fileInfo} ?<br><br>Cette action est irréversible.`;
            
            showModal(dom.mainModal, "Confirmer l'importation du stock", p, [
                { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-400' },
                { label: "Oui, importer", onClick: () => {
                    state.clubStock = stockToLoad;
                    saveStock();
                    hideModal(dom.mainModal);
                    showToast("Stock importé et sauvegardé avec succès !", "success");
                    showStockManagerModal();
                }, className: 'bg-green-600'}
            ]);

        } catch (error) {
            console.error("Erreur d'importation du stock", error);
            showToast("Fichier invalide ou corrompu.", 'error');
        } finally {
            event.target.value = '';
        }
    };
    reader.readAsText(file);
};
const handleExportClubRange = () => {
    if (!state.clubName || state.clubProductRange.length === 0) {
        showToast("Aucune gamme à exporter pour ce club.", "error");
        return;
    }

    // Structure de l'export incluant le tableau d'objets
    const exportData = {
        clubName: state.clubName,
        exportDate: new Date().toISOString().split('T')[0],
        type: "clubProductRange",
        productRange: state.clubProductRange // Exporte directement le tableau d'objets
    };

    try {
        const dataStr = JSON.stringify(exportData, null, 2);
        const blob = new Blob([dataStr], { type: "application/json" });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = `gamme_${state.clubName.replace(/ /g, '_')}.json`;
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        URL.revokeObjectURL(url);
        showToast('Fichier de la gamme exporté avec succès !');
    } catch (error) {
        console.error("Erreur lors de l'export de la gamme", error);
        showToast("Une erreur est survenue lors de l'exportation.", 'error');
    }
};
const handleImportClubRange = (event) => {
    const file = event.target.files[0];
    if (!file) return;

    const reader = new FileReader();
    reader.onload = (e) => {
        try {
            const loadedData = JSON.parse(e.target.result);
            let validatedRange = [];
            let importWarning = null;

            // --- Validation de la structure ---
            if (loadedData.type !== "clubProductRange" || !Array.isArray(loadedData.productRange)) {
                 throw new Error("Le fichier n'est pas un fichier de gamme valide.");
            }

            // --- Gestion Ancienne vs Nouvelle Structure ---
            if (loadedData.productRange.length > 0) {
                if (typeof loadedData.productRange[0] === 'string') {
                    // Ancien format (tableau de strings) -> Conversion
                    importWarning = "Format de gamme ancien détecté. Les prix et options n'ont pas été importés.";
                    validatedRange = loadedData.productRange.map(name => ({
                        name: name,
                        licenseePrice: null,
                        defaultOptions: [],
                        markingType: null
                    }));
                } else if (typeof loadedData.productRange[0] === 'object') {
                    // Nouveau format (tableau d'objets) -> Validation
                    validatedRange = loadedData.productRange.map(item => {
                        // Vérifie la présence du nom
                        const name = item ? item.name : null;
                        const price = item ? item.licenseePrice : null;
                        
                        if (!name) {
                            console.warn("Item de gamme importé sans nom:", item);
                            return null; // Exclure les items sans nom
                        }

                        // ▼▼▼ C'EST ICI QUE LA CORRECTION EST APPLIQUÉE ▼▼▼
                        return {
                            name: String(name), 
                            licenseePrice: (typeof price === 'number' && price >= 0) ? price : null,
                            // On récupère correctement les options (ex: Poche Zip)
                            defaultOptions: Array.isArray(item.defaultOptions) ? item.defaultOptions : [],
                            // On récupère correctement le type de marquage (ex: DTF, SUBLI)
                            markingType: item.markingType || null
                        };
                        // ▲▲▲ ----------------------------------------- ▲▲▲

                    }).filter(item => item !== null); // Filtre les items invalides

                    if (validatedRange.length !== loadedData.productRange.length) {
                         importWarning = "Certains articles de la gamme importée étaient invalides et ont été ignorés.";
                    }
                } else {
                    throw new Error("Format des éléments de la gamme non reconnu dans le fichier.");
                }
            }
            // Si le tableau est vide, validatedRange reste []

            // --- Confirmation Modale ---
            const p = document.createElement('p');
            p.innerHTML = `Voulez-vous remplacer la gamme actuelle par celle du fichier pour le club <b>${loadedData.clubName || state.clubName}</b> ?<br>(${validatedRange.length} articles seront importés).`;
             if (importWarning) {
                 p.innerHTML += `<br><br><strong class="text-orange-600">${importWarning}</strong>`;
             }

            showModal(dom.mainModal, "Confirmer l'importation", p, [
                { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-400' },
                { label: "Oui, importer", onClick: () => {
                    state.clubProductRange = validatedRange; // Utilise le tableau complet avec options
                    const rangeKey = `range_${state.clubName.replace(/[\s/\\?%*:|"<>]/g, '_')}`;
                    localStorage.setItem(rangeKey, JSON.stringify(state.clubProductRange));

                    hideModal(dom.mainModal);
                    showToast("Gamme importée (avec options) sauvegardée !", "success");
                    
                    state.showAllProducts = false;
refreshExistingCartDiscounts();
                    renderProductForm(); // Met à jour la liste déroulante principale
                    
                    // Si la fenêtre de gestion est ouverte, on pourrait la rafraîchir, 
                    // mais l'utilisateur doit généralement la rouvrir pour voir les changements.
                }, className: 'bg-green-600'}
            ]);

        } catch (error) {
            console.error("Erreur d'importation de la gamme", error);
            showToast(`Erreur d'importation: ${error.message}`, 'error');
        } finally {
            event.target.value = ''; // Réinitialise l'input
        }
    };
    reader.readAsText(file);
};
const calculateCurrentFormPrice = () => {
    const product = productMap.get(state.currentProduct);
    if (!product) {
        state.currentCalculatedUnitPrice = 0;
        return;
    }
    
    // 1. Quantité en cours de saisie (dans le formulaire)
    let currentFormQuantity = 0;
    if (state.documentType === 'devis' || (product.type === 'accessoire' && (!product.sizeType || product.sizeType === 'unique'))) {
        currentFormQuantity = parseInt(state.currentAccessoryQuantity, 10) || 0;
    } else {
        currentFormQuantity = Object.values(state.currentQuantities).reduce((sum, q) => sum + (parseInt(q, 10) || 0), 0);
    }

    // --- CORRECTION : CALCUL DU TOTAL GLOBAL ---
    // On regarde ce qu'il y a déjà dans le panier pour cet article précis (même nom)
    const quantityAlreadyInCart = state.currentOrderLineItems
        .filter(item => item.productName === product.name)
        .reduce((sum, item) => sum + item.totalQuantity, 0);

    const totalEffectiveQuantity = currentFormQuantity + quantityAlreadyInCart;
    // -------------------------------------------

    // 2. Gestion INTELLIGENTE du Prix Réassort
    if (state.isReassort) {
        const rangeItem = state.clubProductRange.find(r => r.name === product.name);
        const savedRestockPrice = rangeItem ? rangeItem.lastRestockPrice : null;
        
        const manualCheck = document.getElementById('force-manual-price-check');
        const manualInput = document.getElementById('manual-price-input');
        const manualContainer = document.getElementById('manual-price-container');

        if (savedRestockPrice && manualCheck && manualInput) {
            
            // Si le total global >= 2, on active le prix mémorisé
            if (totalEffectiveQuantity >= 2) {
                if (!manualCheck.checked || parseFloat(manualInput.value) !== savedRestockPrice) {
                    manualCheck.checked = true;
                    manualInput.value = savedRestockPrice.toFixed(2);
                    state.manualUnitPrice = savedRestockPrice;
                    manualContainer.classList.remove('hidden');
                    
                    manualInput.style.backgroundColor = "#dcfce7"; 
                    manualInput.title = `Prix maintien actif car cumul global = ${totalEffectiveQuantity} pièces`;
                }
            } 
            // Sinon on désactive
            else {
                if (manualCheck.checked && parseFloat(manualInput.value) === savedRestockPrice) {
                    manualCheck.checked = false;
                    manualContainer.classList.add('hidden');
                    state.manualUnitPrice = '';
                }
            }
        }
    }

    // 3. Calcul standard (Prix catalogue)
    // On utilise la quantité famille pour le tarif dégressif
    let totalPricingQuantity = currentFormQuantity;
    if (product.pricingGroup) {
         // Si groupe de prix, on compte tout le groupe
         const quantityInCartForGroup = state.currentOrderLineItems
            .filter(item => {
                const itemProd = productMap.get(item.productName);
                return itemProd && itemProd.pricingGroup === product.pricingGroup;
            })
            .reduce((sum, item) => sum + item.totalQuantity, 0);
         totalPricingQuantity += quantityInCartForGroup;
    } else {
         // Sinon juste l'article
         totalPricingQuantity += quantityAlreadyInCart;
    }

    // Petit hack pour le réassort : si on a < 2 au total, on force la tranche 1
    if (state.isReassort && totalEffectiveQuantity < 2) {
        totalPricingQuantity = 1; 
    }

    state.currentCalculatedUnitPrice = getUnitPriceTTC(state.currentProduct, totalPricingQuantity, state.currentSelectedOptions);
};
const resetProductForm = () => {
    state.currentProduct = '';
    state.currentQuantities = {};
    state.currentCalculatedUnitPrice = 0;
    state.manualUnitPrice = '';
    state.currentSelectedOptions = [];
    state.currentSpecificity = '';
    state.currentAccessoryQuantity = '';
    state.currentColor = ''; 
    state.currentVisual = '';
    state.currentMarking = '';
   // On ne remet PAS activeCategory à null ici, sinon la liste disparait quand on change de produit
    // state.activeCategory = null; <--- NE PAS METTRE ÇA
    state.isAddingForStock = false; 
    
    renderProductForm();
};const handleNextLicensee = () => {
    const newName = state.licencieName.trim();
    if (!newName) {
        showToast("Veuillez saisir un nom de licencié.", 'error');
        return;
    }
    if (!state.licenseeList.includes(newName)) {
        state.licenseeList.push(newName);
        showToast(`'${newName}' ajouté à la liste.`);
    }
    state.activeLicensee = newName;
    state.licencieName = '';
    renderAll(); // C'est ici que updateSectionHighlights() est appelée
    dom.licencieNameInput.focus();

    document.getElementById('add-article-section').scrollIntoView({ behavior: 'smooth', block: 'start' });
};
// ▼▼▼ REMPLACEZ L'ANCIENNE FONCTION handlePackAdd PAR CELLE-CI ▼▼▼
const handlePackAdd = () => {
    const licensee = state.orderScope === 'licensee' ? state.activeLicensee : 'Commande Globale';
    if (state.orderScope === 'licensee' && !licensee) {
        showToast("Veuillez sélectionner un licencié.", 'error'); return;
    }
    const selectedPack = state.clubPacks.find(p => p.name === state.currentPackSelection);
    if (!selectedPack) {
        showToast("Aucun pack sélectionné.", 'error'); return;
    }

    // Vérifier si toutes les tailles sont sélectionnées
    for (const productName of selectedPack.productNames) {
        if (!state.currentPackItemConfigs[productName] || !state.currentPackItemConfigs[productName].size) {
            showToast(`Veuillez sélectionner une taille pour "${productName}".`, 'error');
            return;
        }
    }

    const packItems = selectedPack.productNames.map(productName => {
        const config = state.currentPackItemConfigs[productName] || { size: '', visual: '', options: [] };
        return {
            productName: productName,
            size: config.size,
            visual: config.visual,
            options: config.options,
            quantity: 1
        };
    });

    state.currentOrderLineItems.push({
        id: Date.now() + Math.random(),
        type: 'pack',
        productName: selectedPack.name,
        licencieName: licensee,
        totalQuantity: 1,
        unitPriceTTC: selectedPack.priceTTC,
        totalPriceTTC: selectedPack.priceTTC,
        packItems: packItems
    });

    state.currentPackSelection = '';
    state.currentPackItemConfigs = {};
    renderAll();
    showToast(`Le "${selectedPack.name}" a été ajouté pour ${licensee}.`);
};
const handleProductAdd = (forceValidation = false) => {
    const product = productMap.get(state.currentProduct);
    if (!product) return;

    if (state.orderScope === 'licensee' && !state.activeLicensee && !state.isAddingForStock) {
        showToast("Veuillez sélectionner un licencié ou cocher 'Pour le stock'.", 'error');
        return;
    }
    
    let totalQuantity = 0;
    let quantitiesPerSize = {};
    
    if (product.type === 'accessoire' && (!product.sizeType || product.sizeType === 'unique')) {
        totalQuantity = parseInt(state.currentAccessoryQuantity, 10) || 0;
        if (totalQuantity > 0) quantitiesPerSize = { 'U': totalQuantity };
    } else {
        for (const size in state.currentQuantities) {
            const qty = parseInt(state.currentQuantities[size], 10) || 0;
            if (qty > 0) {
                quantitiesPerSize[size] = qty;
                totalQuantity += qty;
            }
        }
    }

    if (totalQuantity === 0) {
        showToast("Veuillez entrer une quantité valide.", 'error');
        return;
    }
    const isManualOverride = document.getElementById('force-manual-price-check')?.checked;

    const rangeItem = state.clubProductRange.find(r => r.name === product.name);
    const wasInInitialOrder = rangeItem && (rangeItem.lastRestockRefQty !== undefined || rangeItem.lastRestockPrice);

    // --- CORRECTION ICI : CALCUL DU TOTAL GLOBAL DE CET ARTICLE DANS LE PANIER ---
    const quantityAlreadyInCart = state.currentOrderLineItems
        .filter(item => item.productName === product.name) // On cherche exactement le même produit
        .reduce((sum, item) => sum + item.totalQuantity, 0);

    // Le total effectif = ce que j'ajoute + ce qu'il y a déjà pour tout le monde
    const totalEffectiveQuantity = totalQuantity + quantityAlreadyInCart;
    // -------------------------------------------------------------------------

    
    // ... (Le reste de la fonction est inchangé, mais je le remets pour être sûr que tout suive)
    let finalSpecificity = state.currentSpecificity;
    const marquageInput = document.getElementById('marquage-text');
    if (marquageInput && state.currentSelectedOptions.includes('AJOUT NOM/PRENOM')) {
        const marquageValue = marquageInput.value.trim();
        if (marquageValue) finalSpecificity = `NOM/PRENOM: ${marquageValue}`;
    }
    const markingSelect = document.getElementById('bas-marking-select');
    if (markingSelect && markingSelect.value) {
        const markingLabel = `FINITION : ${markingSelect.value}`;
        finalSpecificity = finalSpecificity ? `${finalSpecificity} | ${markingLabel}` : markingLabel;
    }

    const manualPriceInput = document.getElementById('manual-price-input');
    const actualManualPrice = (manualPriceInput && manualPriceInput.value.trim() !== '') ? parseFloat(manualPriceInput.value) : 0;
    const isManualActive = document.getElementById('force-manual-price-check')?.checked;

    const isForStockOrder = state.isAddingForStock;
    const ignoreStock = document.getElementById('ignore-stock-check')?.checked || false;

    let stockQuantities = {};
    let productionQuantities = {};
    let totalFromStock = 0;
    let totalForProduction = 0;

    if (!isForStockOrder && !ignoreStock) { 
        for (const size in quantitiesPerSize) {
            const requestedQty = parseInt(quantitiesPerSize[size], 10) || 0;
            if (requestedQty > 0) {
                const stockAvailable = state.clubStock[product.name]?.[size] ?? 0;
                const takenFromStock = Math.min(requestedQty, stockAvailable);
                if (takenFromStock > 0) {
                    stockQuantities[size] = takenFromStock;
                    totalFromStock += takenFromStock;
                    state.clubStock[product.name][size] -= takenFromStock;
                }
                const neededForProduction = requestedQty - takenFromStock;
                if (neededForProduction > 0) {
                    productionQuantities[size] = neededForProduction;
                    totalForProduction += neededForProduction;
                }
            }
        }
    } else {
        productionQuantities = quantitiesPerSize;
        totalForProduction = totalQuantity;
    }

    const mergeQuantities = (targetItem, newQuantities) => {
        for (const size in newQuantities) {
            const newQty = parseInt(newQuantities[size], 10) || 0;
            if (newQty > 0) targetItem.quantitiesPerSize[size] = (parseInt(targetItem.quantitiesPerSize[size], 10) || 0) + newQty;
        }
        targetItem.totalQuantity = Object.values(targetItem.quantitiesPerSize).reduce((sum, q) => sum + q, 0);
    };

    const brodelecContainer = document.getElementById('brodelec-details-container');
    if (brodelecContainer && !brodelecContainer.classList.contains('hidden')) {
        const markType = document.getElementById('brodelec-mark-type').value;
        const locations = Array.from(document.querySelectorAll('.brodelec-loc-check:checked')).map(cb => cb.value);
        if (locations.length === 0) { showToast("Veuillez sélectionner au moins un emplacement.", "error"); return; }
        const brodelecInfo = `[${markType} : ${locations.join(' + ')}]`;
        finalSpecificity = finalSpecificity ? `${finalSpecificity} | ${brodelecInfo}` : brodelecInfo;
    }

// On utilise un Set pour éliminer les doublons d'options
    const uniqueOptions = [...new Set(state.currentSelectedOptions)];

    const commonProperties = {
        options: uniqueOptions, // <-- C'est ici qu'on corrige
        specificity: finalSpecificity,
        internalNote: document.getElementById('internal-note')?.value.trim() || '',
        visual: state.currentVisual,
        color: state.currentColor,
        isManualPrice: isManualActive,
        initialManualPrice: isManualActive ? actualManualPrice : 0
    };
    if (totalFromStock > 0) {
        const licenseeForStockItem = (state.orderScope === 'licensee' && state.activeLicensee) ? state.activeLicensee : 'Commande Globale';
        const existingStockItem = state.currentOrderLineItems.find(item => 
            item.productName === product.name && item.isFromStock === true && item.licencieName === licenseeForStockItem &&
            JSON.stringify(item.options) === JSON.stringify(commonProperties.options) &&
            item.specificity === commonProperties.specificity && item.visual === commonProperties.visual &&
            item.color === commonProperties.color
        );
        if (existingStockItem) mergeQuantities(existingStockItem, stockQuantities);
        else state.currentOrderLineItems.push({
            id: Date.now() + Math.random(), productName: product.name, quantitiesPerSize: stockQuantities,
            totalQuantity: totalFromStock, ...commonProperties,
            isFromStock: true, isStockOrder: false, licencieName: licenseeForStockItem,
        });
    }
// --- LOGIQUE REMISE AUTOMATIQUE ---
    let specificDiscountRate = undefined;
    if (state.applyDiscount && state.discountType === 'item' && rangeItem && rangeItem.allowDiscount) {
        specificDiscountRate = state.clubDiscount;
    }
    // ----------------------------------
    if (totalForProduction > 0) {
        let finalLicenseeName;
        if (isForStockOrder) finalLicenseeName = 'STOCK CLUB'; 
        else if (state.orderScope === 'licensee' && state.activeLicensee) finalLicenseeName = state.activeLicensee;
        else finalLicenseeName = 'Commande Globale';

        const existingProductionItem = state.currentOrderLineItems.find(item =>
            item.productName === product.name && item.isFromStock === false && item.isStockOrder === isForStockOrder && item.licencieName === finalLicenseeName &&
            JSON.stringify(item.options) === JSON.stringify(commonProperties.options) &&
            item.specificity === commonProperties.specificity && item.visual === commonProperties.visual &&
            item.color === commonProperties.color && 
            item.isManualPrice === commonProperties.isManualPrice
        );

        if (existingProductionItem) mergeQuantities(existingProductionItem, productionQuantities);
        else state.currentOrderLineItems.push({
            id: Date.now() + Math.random(), productName: product.name, quantitiesPerSize: productionQuantities,
            totalQuantity: totalForProduction, ...commonProperties,
specificDiscountRate: specificDiscountRate,
            isFromStock: false, isStockOrder: isForStockOrder, licencieName: finalLicenseeName,
        });
    }
    
   // --- MISE À JOUR DYNAMIQUE DE LA GAMME ---
    // Si on est en mode "Catalogue Complet" et qu'on ajoute un produit hors-gamme
    if (state.showAllProducts) {
        // On vérifie si le produit est déjà dans la gamme (comparaison par nom)
        const isInRange = state.clubProductRange.some(r => r.name === product.name);
        
        if (!isInRange) {
            // On l'ajoute proprement avec la structure complète
            state.clubProductRange.push({
                name: product.name,
                licenseePrice: (state.isManualPrice ? state.initialManualPrice : null), // On mémorise le prix si saisi
                defaultOptions: [],
                markingType: null,
                isIncludedInRestock: true
            });

            // Sauvegarde locale de sécurité
            if (state.clubName) {
                const rangeKey = `range_${state.clubName.replace(/[\s/\\?%*:|"<>]/g, '_')}`;
                localStorage.setItem(rangeKey, JSON.stringify(state.clubProductRange));
            }
            
            console.log(`Produit "${product.name}" ajouté à la gamme du club.`);
        }
    }
    // -----------------------------------------

    const licenseeNameForModal = state.activeLicensee;
    resetProductForm();
    renderAll();
    showToast('Article(s) ajouté(s).');
if (currentClubCode) window.syncToCloud(true);
    
    // FORCE LA SYNCHRO CLOUD IMMEDIATEMENT
    if (typeof currentClubCode !== 'undefined' && currentClubCode) {
        syncToCloud();
    }

    if (state.orderScope === 'licensee' && licenseeNameForModal && !isForStockOrder) {
        const content = document.createElement('p');
        content.textContent = `Voulez-vous ajouter un autre article pour ${licenseeNameForModal} ?`;
        showModal(dom.mainModal, 'Continuer la saisie ?', content, [
            { label: 'Non, changer de licencié', className: 'bg-gray-500 hover:bg-gray-600 text-white', onClick: () => { hideModal(dom.mainModal); state.activeLicensee = ''; renderAll(); dom.licencieNameInput.scrollIntoView({behavior: 'smooth', block: 'center'}); dom.licencieNameInput.focus(); } },
            { label: '👁️ Voir détail', className: 'bg-teal-600 hover:bg-teal-700 text-white', onClick: () => { hideModal(dom.mainModal); scrollToLicensee(licenseeNameForModal); } },
            { label: 'Oui, ajouter un autre', className: 'bg-indigo-600 hover:bg-indigo-700 text-white', onClick: () => { hideModal(dom.mainModal); document.getElementById('add-article-section').scrollIntoView({behavior: 'smooth', block: 'start'}); } }
        ]);
    } else if (state.orderScope === 'global' && !isForStockOrder) {
         const content = document.createElement('p');
         content.textContent = "L'article a été ajouté. Ajouter un autre ?";
         showModal(dom.mainModal, 'Continuer ?', content, [
            { label: 'Non, voir le tableau', className: 'bg-gray-500 hover:bg-gray-600 text-white', onClick: () => { hideModal(dom.mainModal); document.getElementById('summary-and-actions-section').scrollIntoView({behavior: 'smooth', block: 'start'}); } },
            { label: 'Oui', className: 'bg-indigo-600 hover:bg-indigo-700 text-white', onClick: () => { hideModal(dom.mainModal); document.getElementById('add-article-section').scrollIntoView({behavior: 'smooth', block: 'start'}); setTimeout(() => { const s = document.getElementById('current-product-select'); if(s) s.focus(); }, 100); } }
         ]);
    }
};const promptForLastDeliveryDate = () => {
    const content = document.createElement('div');
    content.innerHTML = `
        <label for="last-delivery-date" class="block text-sm font-medium text-gray-700">Veuillez indiquer la date de livraison de la commande précédente :</label>
        <input type="date" id="last-delivery-date" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
    `;
    
    showModal(dom.mainModal, 'Date de dernière livraison', content, [
        { 
            label: 'Annuler', 
            className: 'bg-gray-400',
            onClick: () => {
                dom.docTypeReassortCheck.checked = false; // On décoche la case si annulation
                hideModal(dom.mainModal);
            } 
        },
        { 
            label: 'Valider la date', 
            className: 'bg-green-600',
            onClick: () => {
                const dateInput = document.getElementById('last-delivery-date');
                if (!dateInput.value) {
                    showToast("Veuillez entrer une date.", 'error');
                    return;
                }

                const lastDelivery = new Date(dateInput.value);
                const deadline = new Date(lastDelivery);
                deadline.setMonth(deadline.getMonth() + 2); // Ajoute 2 mois
                
                const today = new Date();
                today.setHours(0, 0, 0, 0);

                if (today > deadline) {
                    showToast(`Le délai de réassort de 2 mois est dépassé (date limite : ${deadline.toLocaleDateString('fr-FR')}).`, 'error');
                    dom.docTypeReassortCheck.checked = false;
                } else {
                    // --- MODIFICATION ICI : ON NE VIDE PLUS LE PANIER ---
                    state.isReassort = true;
                    state.lastDeliveryDate = dateInput.value;
                    showToast('Mode Réassort activé. Les tarifs mémorisés (si existants) seront appliqués.', 'success');
                    
                    // On force le recalcul de tous les prix du panier avec les nouvelles règles de réassort
                    // (ex: application du prix manuel mémorisé si qté >= 2)
                    state.currentOrderLineItems = state.currentOrderLineItems.map(item => {
                        // On réinitialise les prix manuels "forcés" pour laisser la logique de réassort agir,
                        // sauf si l'utilisateur avait explicitement saisi un prix manuel lui-même.
                        return item; 
                    });

                    renderAll();
                }
                hideModal(dom.mainModal);
            }
        }
    ]);
};
const showReassortInfoModal = () => {
    const content = document.createElement('div');
    content.innerHTML = `
        <p class="text-sm">Vous activez le mode "Réassort 2 mois".</p>
        <ul class="list-disc list-inside mt-3 space-y-2 text-sm">
            <li>Ce mode permet de commander des articles manquants en conservant les tarifs de votre commande précédente (saisie manuelle des prix).</li>
            <li><strong>Minimum requis :</strong> 2 pièces par référence et 10 pièces au total (hors accessoires).</li>
        </ul>
    `;
    showModal(dom.mainModal, 'Information Réassort', content, [
        { 
            label: 'Annuler',
            className: 'bg-gray-400',
            onClick: () => {
                dom.docTypeReassortCheck.checked = false; // On décoche la case
                hideModal(dom.mainModal);
            }
        },
        { 
            label: 'Continuer',
            className: 'bg-indigo-600',
            onClick: () => {
                hideModal(dom.mainModal);
                setTimeout(promptForLastDeliveryDate, 100); // On lance la 2ème fenêtre
            }
        }
    ]);
};
const resetOrderDetails = () => {
    // 1. On VIDE uniquement ce qui concerne la commande en cours
    state.currentOrderLineItems = []; // Vide le panier
state.isLoadedFromBackup = false; // C'est une nouvelle saisie
    state.activeLicensee = '';        // Plus de licencié actif
    state.licencieName = '';
    state.orderSpecificity = '';      // Vide les notes globales
    state.preOrderNumber = '';
    state.validatedLicensees = [];    // Oublie qui a été validé (coches vertes)
    
    // 2. On remet les paramètres de saisie à zéro
    state.isReassort = false;         // Décoche réassort par défaut
    state.lastDeliveryDate = '';
    state.currentProduct = '';
    state.currentQuantities = {};
    state.currentAccessoryQuantity = '';
    state.currentSpecificity = '';
state.currentInternalNote = '';
    state.manualUnitPrice = '';
    state.isAddingForStock = false;
    
    // 3. On remet la date à aujourd'hui
    state.orderDate = new Date().toISOString().split('T')[0];

    // 4. IMPORTANT : On ne touche PAS à :
    // - state.clubStock (Le stock reste ce qu'il est actuellement)
    // - state.clubName, clientNumber... (L'identité du club reste)
    // - state.clubProductRange (La gamme reste)
    // - state.licenseeList (La liste des gens reste)

    // 5. Nettoyage technique
    localStorage.removeItem('autosavedOrder');

    // 6. Rafraîchissement
    renderAll();
    showToast("Nouvelle commande initialisée. Le stock et les infos du club sont conservés.");
};const handleNewOrder = () => {
    const p = document.createElement('p');
    p.textContent = `Êtes-vous sûr de vouloir commencer une nouvelle commande ? Les articles du panier actuel seront supprimés. Les informations du club (nom, licenciés, acomptes, stock) seront conservées.`;
    showModal(dom.mainModal, 'Confirmation', p, [
        { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300 hover:bg-gray-400 text-gray-800' },
        { label: 'Commencer une nouvelle commande', onClick: () => { 
            resetOrderDetails();
            localStorage.removeItem('autosavedOrder');
            hideModal(dom.mainModal); 
        }, className: 'bg-red-600 hover:bg-red-700 text-white' }
    ]);
};
    
const handleSaveOrderToFile = () => {
    if (!state.clubName.trim()) {
        showToast('Veuillez entrer un nom de club avant de sauvegarder.', 'error');
        return;
    }
    try {
        const dataStr = JSON.stringify(state, null, 2);
        const blob = new Blob([dataStr], { type: "application/json" });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
const todayStr = new Date().toISOString().split('T')[0];
        a.download = `SAUVEGARDE_DATA_${state.clubName.replace(/ /g, '_')}_${todayStr}.json`;
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        URL.revokeObjectURL(url);
        showToast('Fichier de sauvegarde (.json) exporté !');
    } catch (error) {
        console.error("Error saving order to file", error);
        showToast("Erreur lors de l'exportation du fichier .json.", 'error');
    }
};
const handleLoadOrderFromFile = (event) => {
    const file = event.target.files[0];
    if (!file) return;

    const reader = new FileReader();
    reader.onload = async (e) => {
        try {
            let loadedData = JSON.parse(e.target.result);
            let importMessage = "Fichier chargé.";

            // --- 1. DÉTECTION DU TYPE DE FICHIER ---
            // Est-ce une "Fiche Identité / Cockpit" ? (Champs spécifiques)
            const isIdentityFile = loadedData.nomClub || loadedData.adresseLivraison;

            if (isIdentityFile) {
                // Mapping des champs pour compatibilité
                const mappedData = {};
                if(loadedData.nomClub) mappedData.clubName = loadedData.nomClub;
                if(loadedData.departement) mappedData.departement = loadedData.departement;
                if(loadedData.numClient) mappedData.clientNumber = loadedData.numClient;
                if(loadedData.numPrecommande) mappedData.preOrderNumber = loadedData.numPrecommande;
                if(loadedData.departUsine) mappedData.factoryDepartureDate = loadedData.departUsine;
                if(loadedData.portable) mappedData.deliveryContact = loadedData.portable;
                if(loadedData.email) mappedData.deliveryEmail = loadedData.email;
                if(loadedData.adresseLivraison) mappedData.deliveryAddress = loadedData.adresseLivraison.replace(/,/g, '\n');
                
                // On fusionne tout
                loadedData = { ...loadedData, ...mappedData };
                importMessage = "Fiche identité importée.";
            } else if (loadedData.currentState && loadedData.appVersion) {
                // C'est une sauvegarde complète
                loadedData = loadedData.currentState;
                importMessage = "Sauvegarde complète restaurée.";
            }

            // --- 2. INTELLIGENCE DE CONNEXION (C'est ici que ça change tout) ---
            
            // On détermine quel code utiliser : soit celui déjà actif, soit celui trouvé dans le fichier
            let codeToUse = currentClubCode;
            if (!codeToUse && loadedData.preOrderNumber) {
                codeToUse = loadedData.preOrderNumber.trim().toUpperCase(); // On récupère le code du fichier !
            }

            // CAS A : ON A UN CODE (Connecté ou trouvé dans le fichier) -> MODE ÉDITION
            if (codeToUse) {
                const confirmMsg = `Données trouvées pour le code "${codeToUse}".\nVoulez-vous charger ces données, vous connecter et SAUVEGARDER immédiatement ?`;
                
                if (confirm(confirmMsg)) {
                    // 1. Mise à jour de la variable globale de connexion
                    currentClubCode = codeToUse;

                    // 2. On applique les données du fichier
                    Object.assign(state, loadedData);
                    
                    // 3. ON FORCE LE MODE ÉDITION (Très important)
                    state.isReadOnly = false; 
                    state.isLoadedFromBackup = false;
                    state.preOrderNumber = currentClubCode; // On s'assure que ça matche

                    // 4. On rafraîchit l'affichage
                    saveClientInfo();
                    renderAll();
                    
                    // 5. SAUVEGARDE IMMÉDIATE SUR LE CLOUD
                    await window.syncToCloud(false); 
                    
                    // 6. Nettoyage interface
                    const banner = document.getElementById('readonly-banner');
                    if (banner) banner.remove();
                    if (dom.validateOrderBtn) dom.validateOrderBtn.classList.remove('hidden');

                    showToast("✅ Connecté sur " + codeToUse + " et synchronisé !");
                }
            } 
            // CAS B : PAS DE CODE TROUVÉ -> MODE ARCHIVE (Lecture Seule)
            else {
                Object.assign(state, loadedData);
                state.isLoadedFromBackup = true; 
                state.isReadOnly = true; // Active le bandeau orange
                currentClubCode = null;  // Pas de code = pas de cloud
                saveClientInfo();
                renderAll();
                showToast("📂 Mode Lecture Seule (Aucun code club détecté)");
            }

        } catch (error) {
            console.error("Erreur chargement:", error);
            showToast("Erreur : Fichier invalide", 'error');
        } finally {
            event.target.value = ''; // Reset l'input
        }
    };
    reader.readAsText(file); 
};// REMPLACEMENT DE LA FONCTION D'IMPORT EXCEL
const handleImportLicensees = (event) => {
    const file = event.target.files[0];
    if (!file) return;

    const reader = new FileReader();
    reader.onload = (e) => {
        try {
            const data = new Uint8Array(e.target.result);
            const workbook = XLSX.read(data, { type: 'array' });
            const firstSheetName = workbook.SheetNames[0];
            const worksheet = workbook.Sheets[firstSheetName];
            const json = XLSX.utils.sheet_to_json(worksheet, { header: 1 });
            
            let importedCount = 0;
            json.forEach(row => {
                const name = row[0] ? String(row[0]).trim() : ''; // Colonne A : Nom
                const email = row[1] ? String(row[1]).trim() : ''; // Colonne B : Email (Optionnel)

                if (name && !state.licenseeList.includes(name)) {
                    state.licenseeList.push(name);
                    if (email) {
                        state.licenseeEmails[name] = email;
                    }
                    importedCount++;
                } else if (name && state.licenseeList.includes(name) && email) {
                    // Si le licencié existe déjà, on met à jour son email
                    state.licenseeEmails[name] = email;
                }
            });
            
            renderLicenseeDatalist();
            showLicenseeManagerModal(); // Rafraîchit la fenêtre
            showToast(`${importedCount} licencié(s) importé(s) (avec emails si présents) !`);
        } catch (error) {
            console.error("Error importing licensees", error);
            showToast("Erreur lors de l'importation du fichier Excel.", 'error');
        } finally {
            event.target.value = '';
        }
    };
    reader.readAsArrayBuffer(file);
};
const showEditItemModal = (itemId) => {
    // 1. Trouver l'article initial par son ID
    let currentItemIndex = state.currentOrderLineItems.findIndex(i => i.id == itemId);
    if (currentItemIndex === -1) return;
    
    let itemToEdit = JSON.parse(JSON.stringify(state.currentOrderLineItems[currentItemIndex]));
    let isManualModeActive = itemToEdit.isManualPrice === true;

    // Détection état remise existante
    let currentSpecificRate = itemToEdit.specificDiscountRate;
    let isDiscountActive = (currentSpecificRate !== undefined && currentSpecificRate >= 0) || (itemToEdit.appliedDiscountRate > 0);
    
    // Valeur par défaut affichée
    let displayRate = currentSpecificRate !== undefined ? currentSpecificRate : state.clubDiscount;

    const renderModalContent = (container) => {
        const product = productMap.get(itemToEdit.productName);
        if (!product) return;

        let contentHtml = '';

        // --- 0. SÉLECTEUR DE PRODUIT ---
        let sourceProducts = allAvailableProducts;
        if (state.clubProductRange.length > 0 && !state.showAllProducts) {
             sourceProducts = allAvailableProducts.filter(p => state.clubProductRange.some(r => r.name === p.name));
        }
        const productsToShow = sourceProducts.filter(p => p.category !== 'option');
        const grouped = productsToShow.reduce((acc, p) => {
            const groupKey = `${p.category} - ${p.subtype}`;
            if (!acc[groupKey]) acc[groupKey] = [];
            acc[groupKey].push(p);
            return acc;
        }, {});
        
        const productOptions = Object.entries(grouped).map(([groupLabel, productList]) =>
            `<optgroup label="${groupLabel}">
                ${productList.map(p => 
                    `<option value="${p.name}" ${p.name === itemToEdit.productName ? 'selected' : ''}>${p.name}</option>`
                ).join('')}
            </optgroup>`
        ).join('');

        contentHtml += `
            <div class="bg-indigo-50 p-3 rounded-lg border border-indigo-100 mb-4">
                <label class="block text-xs font-bold text-indigo-800 mb-1">Article</label>
                <select id="modal-product-change" class="block w-full rounded-md border-indigo-300 shadow-sm text-sm font-semibold">
                    ${productOptions}
                </select>
            </div>
        `;

        // --- 1. Quantités (STANDARD + SPÉCIALES) ---
        const sizes = productSizes[product.sizeType || product.type] || [];
        
        // On identifie les tailles qui sont dans la commande MAIS PAS dans la grille standard (ex: "Haut L / Bas XL" ou "SUR-MESURE")
        const currentSizes = Object.keys(itemToEdit.quantitiesPerSize);
        const specialSizes = currentSizes.filter(s => !sizes.includes(s) && s !== 'U');

        if (sizes.length > 0) {
            contentHtml += `<div><h4 class="text-md font-semibold text-gray-800 mb-2">Quantités par Taille</h4>`;
            const sizeGrid = sizes.map(size => `
                <div>
                    <label class="block text-sm font-medium text-gray-700">${size}</label>
                    <input type="number" data-size="${size}" class="block w-full rounded-md border-gray-300 shadow-sm modal-size-input text-center" value="${itemToEdit.quantitiesPerSize[size] || ''}">
                </div>
            `).join('');
            contentHtml += `<div class="grid grid-cols-4 md:grid-cols-6 gap-x-3 gap-y-2 mb-4">${sizeGrid}</div>`;
        } 
        
        // --- BLOC TAILLES SPÉCIALES (C'est ici la nouveauté) ---
        if (specialSizes.length > 0) {
            contentHtml += `<div class="bg-yellow-50 p-3 rounded border border-yellow-200 mb-4">
                <h4 class="text-sm font-bold text-yellow-800 mb-2">Tailles Spéciales / Décalées</h4>
                <div class="space-y-2">`;
            
            specialSizes.forEach(sizeKey => {
                contentHtml += `
                <div class="flex items-center justify-between">
                    <label class="text-sm font-medium text-gray-700 w-2/3 truncate" title="${sizeKey}">${sizeKey}</label>
                    <input type="number" data-size="${sizeKey}" class="block w-24 rounded-md border-yellow-400 shadow-sm modal-size-input text-center font-bold bg-white" value="${itemToEdit.quantitiesPerSize[sizeKey]}">
                </div>`;
            });
            
            contentHtml += `</div></div>`;
        }
        
        if (product.sizeType === 'unique' && specialSizes.length === 0) {
            const qty = itemToEdit.quantitiesPerSize['U'] || itemToEdit.totalQuantity || 0;
            contentHtml += `<div>
               <h4 class="text-md font-semibold text-gray-800 mb-2">Quantité</h4>
               <input type="number" data-size="U" class="block w-24 rounded-md border-gray-300 shadow-sm modal-size-input text-center" value="${qty}">
            </div>`;
        }
        
        contentHtml += `</div>`; // Fin bloc quantités

        // --- 2. Autres champs ---
        if (product.colors && product.colors.length > 0) {
            const colorOptions = product.colors.map(c => `<option value="${c}" ${itemToEdit.color === c ? 'selected' : ''}>${c}</option>`).join('');
            contentHtml += `<div class="mt-4"><label class="block text-sm font-bold text-gray-700">Coloris</label><select id="modal-color-select" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm"><option value="">-- Sélectionner --</option>${colorOptions}</select></div>`;
        }
        
        if (state.clubVisuals.length > 0 && product.supplier !== 'BRODELEC') {
            const visualOptions = state.clubVisuals.map(v => `<option value="${v}" ${itemToEdit.visual === v ? 'selected' : ''}>${v}</option>`).join('');
            contentHtml += `<div class="mt-4"><label class="block text-sm font-medium text-gray-700">Visuel</label><select id="modal-visual-select" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm"><option value="">Standard</option>${visualOptions}</select></div>`;
        }

        if (product.hasOptions !== false) {
            const applicableOptions = allAvailableProducts.filter(p => p.category === 'option' && !product.excludedOptions?.includes(p.name));
            if (applicableOptions.length > 0) {
                const optionCheckboxes = applicableOptions.map(opt => `
                    <div class="flex items-center"><input id="modal-option-${opt.name}" type="checkbox" data-option-name="${opt.name}" class="modal-option-checkbox h-4 w-4 rounded border-gray-300 text-indigo-600" ${(itemToEdit.options || []).includes(opt.name) ? 'checked' : ''}><label for="modal-option-${opt.name}" class="ml-3 block text-sm text-gray-900">${opt.name}</label></div>
                `).join('');
                contentHtml += `<div class="mt-4"><h4 class="text-md font-semibold text-gray-800 mb-2">Options</h4><div class="space-y-2">${optionCheckboxes}</div></div>`;
            }
        }
        
        contentHtml += `<div class="mt-4 grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div>
                                <label class="block text-xs font-bold text-gray-700">Spécificité Technique (Visible NORET)</label>
                                <textarea id="modal-specificity" rows="2" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm text-sm">${itemToEdit.specificity || ''}</textarea>
                            </div>
                            <div>
                                <label class="block text-xs font-bold text-purple-700">Note Interne Club 🔒</label>
                                <textarea id="modal-internal-note" rows="2" class="mt-1 block w-full rounded-md border-purple-200 shadow-sm text-sm bg-purple-50 placeholder-purple-300">${itemToEdit.internalNote || ''}</textarea>
                            </div>
                        </div>`;

        // ▼▼▼ GESTION REMISE SÉCURISÉE ▼▼▼
        contentHtml += `
            <div class="mt-6 border-t border-gray-200 pt-4">
                <div class="bg-green-50 p-3 rounded-lg border border-green-200">
                    <div class="flex items-center justify-between">
                        <div>
                            <label for="modal-apply-discount" class="text-sm font-bold text-green-800">Activer une remise</label>
                            <p class="text-xs text-green-600">Force le calcul sur le prix public (ignore Réassort).</p>
                        </div>
                        <input type="checkbox" id="modal-apply-discount" class="h-6 w-6 text-green-600 rounded focus:ring-green-500 border-green-300" ${isDiscountActive ? 'checked' : ''}>
                    </div>
                    
                    <div id="modal-discount-options" class="${isDiscountActive ? '' : 'hidden'} mt-3 pt-3 border-t border-green-200 space-y-3">
                        
                        <div class="flex items-center gap-2 mb-2">
                             <input type="checkbox" id="modal-is-noret-gesture" class="h-4 w-4 text-blue-600 rounded border-gray-300">
                             <label for="modal-is-noret-gesture" class="text-xs font-bold text-blue-800">Geste Commercial Exceptionnel NORET ?</label>
                        </div>
                        
                        <div id="modal-noret-code-block" class="hidden pl-6 mb-2">
                            <label class="block text-xs font-bold text-red-700">Code Autorisation NORET <span class="text-red-500">*</span></label>
                            <input type="text" id="modal-noret-auth-code" placeholder="Ex: 58xxxx" class="block w-32 rounded-md border-red-300 shadow-sm text-sm font-mono mt-1">
                        </div>

                        <div class="grid grid-cols-2 gap-4">
                            <div>
                                <label class="block text-xs font-bold text-green-800 mb-1">Taux de remise (%)</label>
                                <input type="number" id="modal-custom-rate" min="0" max="100" step="0.5" value="${displayRate}" class="block w-full rounded-md border-green-300 shadow-sm text-center font-bold text-green-900 focus:ring-green-500 focus:border-green-500">
                            </div>

                            <div>
                                <label class="block text-xs font-bold text-green-800 mb-1">Sur qté :</label>
                                <div class="flex items-center gap-2">
                                    <input type="number" id="modal-discount-qty" min="1" value="${itemToEdit.totalQuantity}" class="block w-full rounded-md border-green-300 shadow-sm text-center font-bold text-green-900 focus:ring-green-500 focus:border-green-500">
                                    <span class="text-xs text-green-600 whitespace-nowrap">/ <span id="modal-total-qty-display">${itemToEdit.totalQuantity}</span> total</span>
                                </div>
                            </div>
                        </div>
                    </div>
                    <p id="modal-split-warning" class="${isDiscountActive ? '' : 'hidden'} text-[10px] text-green-600 mt-2 italic">
                        Si quantité partielle, une 2ème ligne sera créée pour le reste au prix normal.
                    </p>
                </div>
            </div>
        `;

        // --- PRIX ADMIN ---
        const manualPriceVal = (itemToEdit.initialManualPrice || 0).toFixed(2);
        contentHtml += `
            <div class="mt-4">
                <div class="bg-red-50 border border-red-200 p-3 rounded-lg">
                    <div class="flex items-center justify-between">
                        <label class="text-sm font-bold text-red-800 flex items-center gap-2">
                            <span>🛡️ Admin : Forcer le prix unitaire</span>
                        </label>
                        <input type="checkbox" id="modal-force-price-check" class="h-5 w-5 text-red-600 rounded border-red-300 focus:ring-red-500" ${isManualModeActive ? 'checked' : ''}>
                    </div>
                    <div id="modal-force-price-container" class="${isManualModeActive ? '' : 'hidden'} mt-3">
                        <label class="block text-xs text-red-700 mb-1">Nouveau Prix Unitaire TTC (€)</label>
                        <input type="number" id="modal-force-price-input" value="${manualPriceVal}" placeholder="Ex: 45.00" class="block w-32 rounded-md border-red-300 shadow-sm text-red-900 font-bold focus:ring-red-500 focus:border-red-500">
                    </div>
                </div>
            </div>
        `;
        
        container.innerHTML = contentHtml;
        
        // --- LISTENERS ---
        // (Identique à avant, mais adapté)
        const updateTotalQtyDisplay = () => {
            let tempTotal = 0;
            // On compte les inputs standards ET les inputs spéciaux
            container.querySelectorAll('.modal-size-input').forEach(inp => tempTotal += (parseInt(inp.value)||0));
            const disp = container.querySelector('#modal-total-qty-display');
            const discInput = container.querySelector('#modal-discount-qty');
            if(disp) disp.textContent = tempTotal;
            if(discInput) {
                if (parseInt(discInput.value) > tempTotal || discInput.dataset.touched !== 'true') {
                    discInput.value = tempTotal;
                }
                discInput.max = tempTotal;
            }
        };

        container.querySelectorAll('.modal-size-input').forEach(inp => {
            inp.addEventListener('input', updateTotalQtyDisplay);
        });

        // RESTE DES LISTENERS (Remise, Password, etc.) IDENTIQUE...
        // ... (J'inclus le code pour être sûr)
        
        const discQtyInput = container.querySelector('#modal-discount-qty');
        if(discQtyInput) discQtyInput.addEventListener('input', () => { discQtyInput.dataset.touched = 'true'; });

        const productSelect = container.querySelector('#modal-product-change');
        if (productSelect) {
            productSelect.addEventListener('change', (e) => {
                itemToEdit.productName = e.target.value;
                itemToEdit.quantitiesPerSize = {};
                itemToEdit.totalQuantity = 0;
                itemToEdit.isManualPrice = false;
                isManualModeActive = false;
                renderModalContent(container);
            });
        }

        const discountCheck = container.querySelector('#modal-apply-discount');
        const discountOptions = container.querySelector('#modal-discount-options');
        const splitWarning = container.querySelector('#modal-split-warning');
        const noretGestureCheck = container.querySelector('#modal-is-noret-gesture');
        const noretCodeBlock = container.querySelector('#modal-noret-code-block');

        if (discountCheck) {
            discountCheck.addEventListener('change', (e) => {
                isDiscountActive = e.target.checked;
                if(e.target.checked) {
                    discountOptions.classList.remove('hidden');
                    splitWarning.classList.remove('hidden');
                    updateTotalQtyDisplay();
                } else {
                    discountOptions.classList.add('hidden');
                    splitWarning.classList.add('hidden');
                    if(noretGestureCheck) {
                        noretGestureCheck.checked = false;
                        noretCodeBlock.classList.add('hidden');
                    }
                }
            });
        }

        if (noretGestureCheck) {
            noretGestureCheck.addEventListener('change', (e) => {
                if (e.target.checked) {
                    noretCodeBlock.classList.remove('hidden');
                    document.getElementById('modal-noret-auth-code').focus();
                } else {
                    noretCodeBlock.classList.add('hidden');
                    document.getElementById('modal-noret-auth-code').value = '';
                }
            });
        }

        const checkBtn = container.querySelector('#modal-force-price-check');
        const inputContainer = container.querySelector('#modal-force-price-container');
        const priceInput = container.querySelector('#modal-force-price-input');

        if(checkBtn) {
            checkBtn.addEventListener('click', (e) => {
                if (e.target.checked) {
                    e.preventDefault(); 
                    const password = prompt("Mot de passe Administrateur :");
                    if (password === ADMIN_PASSWORD) {
                        e.target.checked = true;
                        isManualModeActive = true; 
                        inputContainer.classList.remove('hidden');
                        if(priceInput) { priceInput.disabled = false; priceInput.focus(); }
                        if (discountCheck) { 
                            discountCheck.checked = false; 
                            discountOptions.classList.add('hidden');
                            splitWarning.classList.add('hidden');
                        }
                        isDiscountActive = false;
                        showToast("Mode forçage activé.", "success");
                    } else {
                        if(password !== null) showToast("Mot de passe incorrect.", "error");
                        e.target.checked = false;
                        isManualModeActive = false;
                    }
                } else {
                    isManualModeActive = false;
                    inputContainer.classList.add('hidden');
                    if(priceInput) priceInput.value = '';
                }
            });
        }
    };

    const container = document.createElement('div');
    container.className = 'space-y-6 max-h-[70vh] overflow-y-auto pr-2';
    
    renderModalContent(container);

    showModal(dom.mainModal, "Modifier la ligne", container, [
        { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300' },
        { 
            label: 'Enregistrer', 
            className: 'bg-green-600 text-white',
            onClick: () => {
                const indexToUpdate = state.currentOrderLineItems.findIndex(i => i.id == itemId);
                if (indexToUpdate === -1) return;

                const newQuantities = {};
                let newTotalQuantity = 0;
                const sizeInputs = container.querySelectorAll('.modal-size-input');
                const enteredSizes = []; 

                sizeInputs.forEach(input => {
                    const qty = parseInt(input.value, 10) || 0;
                    if (qty > 0) {
                        newQuantities[input.dataset.size] = qty;
                        newTotalQuantity += qty;
                        for(let k=0; k<qty; k++) enteredSizes.push(input.dataset.size);
                    }
                });
                
                if (newTotalQuantity === 0) {
                    if(confirm("Quantité totale à 0. Supprimer l'article ?")) {
                        state.currentOrderLineItems.splice(indexToUpdate, 1);
                        hideModal(dom.mainModal);
                        renderAll();
                        showToast('Article supprimé.');
                        return;
                    } else { return; }
                }

                const newColor = container.querySelector('#modal-color-select')?.value || '';
                const newVisual = container.querySelector('#modal-visual-select')?.value || '';
                const newOptions = Array.from(container.querySelectorAll('.modal-option-checkbox:checked')).map(cb => cb.dataset.optionName);
                const newSpecificity = container.querySelector('#modal-specificity')?.value || '';
                let newInternalNote = container.querySelector('#modal-internal-note')?.value.trim() || '';
                const isForced = isManualModeActive;
                const forcedPrice = parseFloat(container.querySelector('#modal-force-price-input')?.value) || 0;

                const applyDiscount = document.getElementById('modal-apply-discount')?.checked || false;
                const customRate = applyDiscount ? (parseFloat(document.getElementById('modal-custom-rate')?.value) || 0) : 0;
                const qtyToDiscount = applyDiscount ? (parseInt(document.getElementById('modal-discount-qty')?.value, 10) || newTotalQuantity) : 0;

                // Validation Noret
                const isNoretGesture = document.getElementById('modal-is-noret-gesture')?.checked;
                if (applyDiscount && isNoretGesture) {
                    const authCode = document.getElementById('modal-noret-auth-code')?.value.trim();
                    if (authCode !== '582069') {
                        alert("⛔ Erreur : Le Code Autorisation NORET est incorrect.\n\nVous ne pouvez pas appliquer ce geste commercial sans le code de validation.");
                        document.getElementById('modal-noret-auth-code').focus();
                        return; 
                    }
                    if (!newInternalNote.includes('Code 582069')) {
                        newInternalNote = (newInternalNote ? newInternalNote + ' | ' : '') + 'Accord NORET (Code 582069)';
                    }
                }

                // Cas 1 : Pas de remise ou Remise totale
                if (!applyDiscount || qtyToDiscount >= newTotalQuantity) {
                    const item = state.currentOrderLineItems[indexToUpdate];
                    item.productName = itemToEdit.productName;
                    item.quantitiesPerSize = newQuantities;
                    item.totalQuantity = newTotalQuantity;
                    item.color = newColor;
                    item.visual = newVisual;
                    item.options = newOptions;
                    item.specificity = newSpecificity;
                    item.internalNote = newInternalNote; 
                    
                    item.specificDiscountRate = applyDiscount ? customRate : undefined;

                    item.isManualPrice = isForced;
                    item.initialManualPrice = isForced ? forcedPrice : 0;
                    if (isForced) item.unitPriceTTC = forcedPrice;
                }
                // Cas 2 : Remise partielle (SCISSION) - Un peu plus complexe avec des tailles spéciales, mais on simplifie
                else {
                     // Simplification : si on scinde avec des tailles spéciales, on prend les premières trouvées pour la remise
                     // C'est un cas rare, donc cette logique simple suffit souvent.
                    const discountedSizes = {};
                    for (let i = 0; i < qtyToDiscount; i++) {
                        const size = enteredSizes[i];
                        discountedSizes[size] = (discountedSizes[size] || 0) + 1;
                    }

                    const itemDiscounted = state.currentOrderLineItems[indexToUpdate];
                    itemDiscounted.productName = itemToEdit.productName;
                    itemDiscounted.quantitiesPerSize = discountedSizes;
                    itemDiscounted.totalQuantity = qtyToDiscount;
                    itemDiscounted.color = newColor;
                    itemDiscounted.visual = newVisual;
                    itemDiscounted.options = newOptions;
                    itemDiscounted.specificity = newSpecificity;
                    itemDiscounted.internalNote = newInternalNote + " (Remisé)";
                    itemDiscounted.specificDiscountRate = customRate; 
                    itemDiscounted.isManualPrice = false;

                    const normalSizes = {};
                    for (let i = qtyToDiscount; i < enteredSizes.length; i++) {
                        const size = enteredSizes[i];
                        normalSizes[size] = (normalSizes[size] || 0) + 1;
                    }
                    const normalQty = newTotalQuantity - qtyToDiscount;

                    const newItemNormal = {
                        ...JSON.parse(JSON.stringify(itemDiscounted)),
                        id: Date.now(), 
                        quantitiesPerSize: normalSizes,
                        totalQuantity: normalQty,
                        internalNote: newInternalNote.replace('Accord NORET (Code 582069)', '').trim(), 
                        specificDiscountRate: undefined, // PAS DE REMISE
                        appliedDiscountRate: 0,
                        isManualPrice: isForced, 
                        initialManualPrice: isForced ? forcedPrice : 0
                    };

                    state.currentOrderLineItems.push(newItemNormal);
                    showToast(`Article scindé : ${qtyToDiscount} à -${customRate}%, ${normalQty} au prix normal.`);
                }
                
                hideModal(dom.mainModal);
                renderAll(); 
                if(!applyDiscount || qtyToDiscount >= newTotalQuantity) showToast('Article modifié');
            } 
        }
    ]);
};// REMPLACEMENT DE LA FONCTION DE GESTION DES LICENCIÉS (MODALE)
const showLicenseeManagerModal = () => {
    const renderManagerContent = () => {
        const container = document.createElement('div');
        container.className = 'space-y-4';
        
        container.innerHTML = `
            <div class="grid grid-cols-2 gap-2">
                <label for="import-licensees-input" class="w-full text-center block px-4 py-2 bg-green-600 text-white rounded-md hover:bg-green-700 cursor-pointer">Importer Excel (Nom | Email)</label>
                <button id="export-licensees-btn" class="w-full text-center block px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700">Exporter Excel</button>
            </div>
            <p class="text-xs text-gray-500 text-center -mt-2">Format Excel : Colonne A = Nom, Colonne B = Email (optionnel)</p>
            
            <div class="flex gap-2 pt-4 border-t items-end">
                <div class="flex-grow">
                    <label class="text-xs text-gray-600">Nom Prénom</label>
                    <input type="text" id="new-licensee-name" placeholder="Nom..." class="block w-full rounded-md border-gray-300 shadow-sm text-sm">
                </div>
                <div class="flex-grow">
                    <label class="text-xs text-gray-600">Email (Facultatif)</label>
                    <input type="text" id="new-licensee-email" placeholder="Email..." class="block w-full rounded-md border-gray-300 shadow-sm text-sm">
                </div>
                <button id="add-new-licensee-btn" class="px-4 py-2 bg-indigo-600 text-white rounded-md hover:bg-indigo-700 text-sm font-bold">Ajouter</button>
            </div>
            <div id="licensee-list-container" class="space-y-2 max-h-60 overflow-y-auto mt-2 bg-gray-50 p-2 rounded border"></div>
        `;

        const listContainer = container.querySelector('#licensee-list-container');
        const licenseesWithOrders = new Set(state.currentOrderLineItems.map(item => item.licencieName));

        state.licenseeList.sort((a,b) => a.localeCompare(b)).forEach(name => {
            const email = state.licenseeEmails[name] || '';
            const itemDiv = document.createElement('div');
            itemDiv.className = 'flex justify-between items-center p-2 border-b border-gray-200 bg-white last:border-0';
            
            let nameClass = 'font-medium text-gray-800';
            if (licenseesWithOrders.has(name)) nameClass = 'font-bold text-indigo-700';

            itemDiv.innerHTML = `
                <div class="flex flex-col">
                    <span class="${nameClass}">${name}</span>
                    ${email ? `<span class="text-xs text-gray-500">📧 ${email}</span>` : '<span class="text-[10px] text-gray-400 italic">Pas d\'email</span>'}
                </div>
                <div class="flex gap-2">
                    <button class="edit-email-btn text-xs bg-blue-100 text-blue-700 px-2 py-1 rounded hover:bg-blue-200">✉️</button>
                    <button class="delete-lic-btn text-xs bg-red-100 text-red-700 px-2 py-1 rounded hover:bg-red-200">X</button>
                </div>
            `;

            // Bouton Supprimer
            itemDiv.querySelector('.delete-lic-btn').onclick = () => {
                if(confirm(`Supprimer ${name} ?`)) {
                    state.licenseeList = state.licenseeList.filter(n => n !== name);
                    delete state.licenseeEmails[name];
                    renderLicenseeDatalist();
                    refreshView();
                }
            };

            // Bouton Modifier Email
            itemDiv.querySelector('.edit-email-btn').onclick = () => {
                const newEmail = prompt(`Modifier l'email pour ${name} :`, email);
                if (newEmail !== null) {
                    state.licenseeEmails[name] = newEmail.trim();
                    refreshView();
                }
            };

            listContainer.appendChild(itemDiv);
        });

        const refreshView = () => {
            dom.mainModalBody.innerHTML = '';
            dom.mainModalBody.appendChild(renderManagerContent());
        };

        container.querySelector('#add-new-licensee-btn').onclick = () => {
            const nameInput = container.querySelector('#new-licensee-name');
            const emailInput = container.querySelector('#new-licensee-email');
            const newName = nameInput.value.trim();
            const newEmail = emailInput.value.trim();

            if (newName && !state.licenseeList.includes(newName)) {
                state.licenseeList.push(newName);
                if (newEmail) state.licenseeEmails[newName] = newEmail;
                renderLicenseeDatalist();
                refreshView();
            } else if (newName && state.licenseeList.includes(newName)) {
                alert("Ce nom existe déjà.");
            }
            nameInput.focus();
        };

        // Export (inclut les emails)
        container.querySelector('#export-licensees-btn').onclick = () => {
             if (typeof XLSX === 'undefined') return;
             const data = [["Nom", "Email"]];
             state.licenseeList.forEach(n => data.push([n, state.licenseeEmails[n] || ""]));
             const wb = XLSX.utils.book_new();
             const ws = XLSX.utils.aoa_to_sheet(data);
             ws['!cols'] = [{wch:30}, {wch:40}];
             XLSX.utils.book_append_sheet(wb, ws, "Licenciés");
             XLSX.writeFile(wb, `LISTE_MEMBRES_${state.clubName || 'CLUB'}.xlsx`);
        };

        return container;
    };

    showModal(dom.mainModal, 'Gérer les Licenciés & Emails', renderManagerContent(), [
        { label: 'Fermer', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-500' }
    ]);
};
const showDepositModal = (licenseeName) => {
    const container = document.createElement('div');
    container.className = 'space-y-3';
    container.innerHTML = `
        <p>Saisir le montant de l'acompte pour <b>${licenseeName}</b>.</p>
        <div>
            <label for="deposit-amount" class="block text-sm font-medium text-gray-700">Montant de l'acompte (€)</label>
            <input type="number" id="deposit-amount" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm" placeholder="0.00" value="${state.licenseeDeposits[licenseeName] || ''}">
        </div>
    `;
    
    showModal(dom.mainModal, `Acompte pour ${licenseeName}`, container, [
        { label: 'Supprimer Acompte', onClick: () => {
            delete state.licenseeDeposits[licenseeName];
            hideModal(dom.mainModal);
            renderAll();
            // SYNCHRO CLOUD AUTO
            if (currentClubCode) window.syncToCloud(true);
            showToast(`Acompte pour ${licenseeName} supprimé.`);
        }, className: 'bg-red-600 hover:bg-red-700 text-white' },
        { label: 'Enregistrer', onClick: () => {
            const amount = parseFloat(document.getElementById('deposit-amount').value) || 0;
            if (amount > 0) {
                state.licenseeDeposits[licenseeName] = amount;
            } else {
                delete state.licenseeDeposits[licenseeName];
            }
            hideModal(dom.mainModal);
            renderAll();
            // SYNCHRO CLOUD AUTO
            if (currentClubCode) window.syncToCloud(true);
            showToast(`Acompte pour ${licenseeName} mis à jour.`);
        }, className: 'bg-green-600 hover:bg-green-700 text-white' }
    ]);
};    
const promptForAdminPassword = (callbackOnSuccess) => {
    const container = document.createElement('div');
    container.className = 'space-y-4';
    container.innerHTML = `
        <p>Veuillez saisir le mot de passe administrateur.</p>
        <input type="password" id="admin-password-input" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
        <p id="admin-password-error" class="text-red-500 text-sm hidden">Mot de passe incorrect.</p>
    `;

    const checkPassword = () => {
        const input = document.getElementById('admin-password-input');
        if (input.value === ADMIN_PASSWORD) {
            hideModal(dom.mainModal);
            callbackOnSuccess();
        } else {
            document.getElementById('admin-password-error').classList.remove('hidden');
            input.classList.add('border-red-500');
        }
    };

    showModal(dom.mainModal, 'Accès Administrateur', container, [
        { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300' },
        { label: 'Confirmer', onClick: checkPassword, className: 'bg-green-600 hover:bg-green-700' }
    ]);
};

const showHistoryModal = () => {
    const displayHistoryList = () => {
        const history = JSON.parse(localStorage.getItem('documentHistory') || '[]');
        const historyContainer = document.createElement('div');
        historyContainer.className = 'space-y-2 max-h-64 overflow-y-auto';

        if (history.length === 0) {
            historyContainer.textContent = 'Aucun document dans l\'historique.';
        } else {
            history.sort((a, b) => new Date(b.orderDate) - new Date(a.orderDate)).forEach((docState, index) => {
                const itemDiv = document.createElement('div');
                itemDiv.className = 'flex justify-between items-center p-2 border rounded-md';
                itemDiv.innerHTML = `
                    <div>
                        <p class="font-semibold">${docState.documentType === 'devis' ? 'Devis' : 'Commande'} - ${docState.clubName}</p>
                        <p class="text-xs text-gray-500">Date: ${docState.orderDate}</p>
                    </div>
                    <div class="flex gap-2">
                        <button data-action="load-history" data-index="${index}" class="text-xs bg-blue-500 text-white px-2 py-1 rounded hover:bg-blue-600">Charger</button>
                        <button data-action="duplicate-history" data-index="${index}" class="text-xs bg-green-500 text-white px-2 py-1 rounded hover:bg-green-600">Dupliquer</button>
                        <button data-action="delete-history" data-index="${index}" class="text-xs bg-red-500 text-white px-2 py-1 rounded hover:bg-red-600">Suppr.</button>
                    </div>
                `;
                historyContainer.appendChild(itemDiv);
            });
        }
        
        const newModalBody = dom.mainModalBody.cloneNode(false);
        dom.mainModalBody.parentNode.replaceChild(newModalBody, dom.mainModalBody);
        dom.mainModalBody = newModalBody;

        dom.mainModalBody.addEventListener('click', (e) => {
            const target = e.target.closest('button');
            if (!target) return;
            const { action, index } = target.dataset;
            let history = JSON.parse(localStorage.getItem('documentHistory') || '[]');
            history.sort((a, b) => new Date(b.orderDate) - new Date(a.orderDate));
            const doc = history[index];

            if (action === 'load-history' && doc) {
                Object.assign(state, doc);
                renderAll();
                hideModal(dom.mainModal);
                showToast('Document chargé depuis l\'historique.');
            } else if (action === 'duplicate-history' && doc) {
                Object.assign(state, doc);
                state.orderDate = new Date().toISOString().split('T')[0];
                renderAll();
                hideModal(dom.mainModal);
                showToast('Document dupliqué. La date a été mise à jour.');
            } else if (action === 'delete-history' && doc) {
                history.splice(index, 1);
                localStorage.setItem('documentHistory', JSON.stringify(history));
                displayHistoryList();
            }
        });

        showModal(dom.mainModal, 'Historique des Documents', historyContainer, [
            { label: 'Fermer', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-500' }
        ]);
    };

    promptForAdminPassword(displayHistoryList);
};

const promptForForceCode = (callbackOnSuccess) => {
    hideModal(dom.mainModal); 
    const container = document.createElement('div');
    container.className = 'space-y-4';
    container.innerHTML = `
        <p>Pour forcer cette action, veuillez saisir le code d'autorisation.</p>
        <input type="password" id="force-code-input" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
        <p id="force-code-error" class="text-red-500 text-sm hidden">Code incorrect.</p>
    `;
    showModal(dom.mainModal, 'Code d\'Autorisation Requis', container, [
        { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300' },
        { label: 'Confirmer', onClick: () => {
            if (document.getElementById('force-code-input').value === '582069') {
                hideModal(dom.mainModal);
                callbackOnSuccess();
            } else {
                document.getElementById('force-code-error').classList.remove('hidden');
                document.getElementById('force-code-input').classList.add('border-red-500');
            }
        }, className: 'bg-green-600 hover:bg-green-700' }
    ]);
};
 // ▼▼▼ FONCTION MANQUANTE À AJOUTER ▼▼▼
const handleExportBrodelecPdf = () => {
    // 1. Filtrer uniquement les articles BRODELEC
    const brodelecItems = state.currentOrderLineItems.filter(item => {
        const product = productMap.get(item.productName);
        return product && product.supplier === 'BRODELEC';
    });

    if (brodelecItems.length === 0) return; 

    // 2. CONSOLIDATION (Regroupement)
    const consolidatedItems = {};
    brodelecItems.forEach(item => {
        const key = `${item.productName}|${item.color || ''}`;
        if (!consolidatedItems[key]) {
            consolidatedItems[key] = {
                ...item,
                quantitiesPerSize: { ...item.quantitiesPerSize },
                totalQuantity: item.totalQuantity
            };
        } else {
            const existing = consolidatedItems[key];
            existing.totalQuantity += item.totalQuantity;
            if (item.quantitiesPerSize) {
                for (const [size, qty] of Object.entries(item.quantitiesPerSize)) {
                    existing.quantitiesPerSize[size] = (existing.quantitiesPerSize[size] || 0) + qty;
                }
            }
        }
    });
    const finalItems = Object.values(consolidatedItems);

    // 3. GÉNÉRATION DU PDF
    const { jsPDF } = window.jspdf;
    const doc = new jsPDF();
    const pageWidth = doc.internal.pageSize.width;
    const margin = 14;
    let currentY = 20;

    // --- TITRE (STANDARD BRODELEC) ---
   doc.setFontSize(20);
    doc.setFont(undefined, 'bold');
    doc.setTextColor(0, 100, 0); 
    // MODIFICATION TITRE
    doc.text("PRÉVISIONNEL DE COMMANDE BRODELEC", margin, currentY); 
    currentY += 10;
    
    // --- Infos Club ---
    doc.setFontSize(11);
    doc.setTextColor(0);
    doc.text(`Club: ${state.clubName}`, margin, currentY);
    currentY += 6;
    doc.text(`Date: ${new Date().toLocaleDateString('fr-FR')}`, margin, currentY);
    currentY += 10;

    // --- INFOS DE LIVRAISON ---
    doc.setFont(undefined, 'bold');
    doc.text("Adresse de Livraison :", margin, currentY);
    currentY += 5;
    
    doc.setFont(undefined, 'normal');
    doc.setFontSize(10);
    
    // Adresse (gestion multi-lignes)
    const addressLines = doc.splitTextToSize(state.deliveryAddress || 'Non renseignée', 100);
    doc.text(addressLines, margin, currentY);
    currentY += (addressLines.length * 5);

    // Tel et Email
    doc.text(`Tel : ${state.deliveryContact || 'Non renseigné'}`, margin, currentY);
    currentY += 5;
    doc.text(`Email : ${state.deliveryEmail || 'Non renseigné'}`, margin, currentY);
    currentY += 10;
if (state.orderSpecificity && state.orderSpecificity.trim() !== "") {
            // S'il y a une note, on descend un peu
            currentY += 5;
            
            // Titre en ROUGE pour attirer l'attention
            doc.setFont(undefined, 'bold');
            doc.setTextColor(220, 20, 60); // Rouge
            doc.text("⚠️ SPÉCIFICITÉ COMMANDE / NOTE NORET :", margin, currentY);
            currentY += 6;

            // Le texte de la note en noir, normal, avec retour à la ligne automatique
            doc.setFont(undefined, 'normal');
            doc.setTextColor(0); // Noir
            doc.setFontSize(10);
            
            // Gestion du texte long (coupe automatique si ça dépasse la largeur)
            const splitNote = doc.splitTextToSize(state.orderSpecificity, contentWidth);
            doc.text(splitNote, margin, currentY);
            
            // On calcule la place que ça a pris pour descendre le curseur
            currentY += (splitNote.length * 5) + 10; 
        }
    // --- Détection Personnalisation ---
    const hasCustomization = finalItems.some(item => item.options && item.options.includes('Personnalisation logo club'));

    doc.setFont(undefined, 'italic');
    
    if (hasCustomization) {
        doc.setTextColor(200, 0, 0); 
        doc.text("ATTENTION : Commande personnalisée - Frais de port NON INCLUS", margin, currentY);
        doc.text("Le prix unitaire ci-dessous INCLUT le coût du marquage.", margin, currentY + 5);
        currentY += 10;
    } else {
        doc.setTextColor(0, 100, 0); // Vert
        doc.text("Frais de port inclus - Pas de minimum de commande", margin, currentY);
        currentY += 5;
    }
    doc.setTextColor(0);

    // --- Tableau ---
    const head = [['Article', 'Couleur', 'Taille(s) & Qté', 'Total Qté', 'P.U. TTC', 'Total TTC']];
    let totalTTC = 0;

    const body = finalItems.map(item => {
        const sizesText = getSortedSizesText(item);
        
        // PRIX : Priorité au prix manuel si défini, sinon prix fixe gamme, sinon remise
        let displayPrice = item.unitPriceTTC || 0; 

        if (item.isManualPrice && item.initialManualPrice > 0) {
            displayPrice = item.initialManualPrice;
        }
        else {
            const rangeItem = state.clubProductRange.find(r => r.name === item.productName);
            if (rangeItem && typeof rangeItem.licenseePrice === 'number' && rangeItem.licenseePrice >= 0) {
                displayPrice = rangeItem.licenseePrice;
            }
            else if (item.appliedDiscountRate > 0) {
                displayPrice = displayPrice * (1 - (item.appliedDiscountRate / 100));
            }
        }

        const lineTotal = displayPrice * item.totalQuantity;
        totalTTC += lineTotal;

        let description = item.productName;
        if (item.specificity) {
            description += `\n${item.specificity}`;
        }

        return [
            description,
            item.color || '-',      
            sizesText,
            item.totalQuantity,
            `${displayPrice.toFixed(2)} €`,
            `${lineTotal.toFixed(2)} €`
        ];
    });

    doc.autoTable({
        startY: currentY,
        head: head,
        body: body,
        theme: 'grid',
        headStyles: { fillColor: [40, 167, 69] }, 
        styles: { fontSize: 9, valign: 'middle' },
        columnStyles: {
            0: { cellWidth: 60 }, 
            2: { cellWidth: 50 }
        }
    });

    // --- Totaux ---
    let finalY = doc.autoTable.previous.finalY + 10;
    doc.setFontSize(12);
    doc.setFont(undefined, 'bold');
    doc.text(`TOTAL BRODELEC TTC: ${totalTTC.toFixed(2)} €`, pageWidth - margin, finalY, { align: 'right' });

   const todayStr = new Date().toISOString().split('T')[0];
    doc.save(`PREVISIONNEL_BRODELEC_${state.clubName.replace(/ /g, '_')}_${todayStr}.pdf`);
    showToast("Prévisionnel Brodelec généré !");
};
const showRegenerationOptionsModal = () => {
    if (state.currentOrderLineItems.length === 0) {
        showToast("La commande est vide.", "error");
        return;
    }

    // --- 1. ANALYSE DU CONTENU ---
    const hasNoretItems = state.currentOrderLineItems.some(item => {
        const p = productMap.get(item.productName);
        return !p || p.supplier !== 'BRODELEC';
    });
    
    const hasBrodelecItems = state.currentOrderLineItems.some(item => {
        const p = productMap.get(item.productName);
        return p && p.supplier === 'BRODELEC';
    });

    const isLicenseeMode = state.orderScope === 'licensee';

    // --- 2. CONSTRUCTION DU FORMULAIRE ---
    const container = document.createElement('div');
    container.className = 'space-y-3';
    
    let html = `<p class="text-sm text-gray-600 mb-4">Sélectionnez les documents que vous souhaitez générer :</p>`;
    
    // Option 1 : Bon de Commande NORET
    if (hasNoretItems) {
        html += `
        <div class="flex items-center p-2 bg-blue-50 rounded border border-blue-100">
            <input type="checkbox" id="regen-noret" class="h-5 w-5 text-indigo-600 border-gray-300 rounded" checked>
            <label for="regen-noret" class="ml-3 block text-sm font-bold text-blue-900">📄 Bon de Commande NORET (PDF)</label>
        </div>`;
    }

    // Option 2 : Prévisionnel Club (Toujours dispo)
    html += `
    <div class="flex items-center p-2 bg-gray-50 rounded border border-gray-200">
        <input type="checkbox" id="regen-club" class="h-5 w-5 text-indigo-600 border-gray-300 rounded" checked>
        <label for="regen-club" class="ml-3 block text-sm font-bold text-gray-800">📊 Prévisionnel Club (PDF Interne)</label>
    </div>`;

    // Options Licenciés
    if (isLicenseeMode) {
        html += `
        <div class="mt-2 border-t pt-2 space-y-2">
            <p class="text-xs font-bold text-gray-500 uppercase">Documents Licenciés</p>
            
            <div class="flex items-center p-2 bg-purple-50 rounded border border-purple-100">
                <input type="checkbox" id="regen-detail" class="h-5 w-5 text-purple-600 border-gray-300 rounded" checked>
                <label for="regen-detail" class="ml-3 block text-sm font-bold text-purple-900">💰 Détail Financier par Licencié (PDF)</label>
            </div>
            
            <div class="flex items-center p-2 bg-purple-50 rounded border border-purple-100">
                <input type="checkbox" id="regen-distri" class="h-5 w-5 text-purple-600 border-gray-300 rounded" checked>
                <label for="regen-distri" class="ml-3 block text-sm font-bold text-purple-900">📦 Liste de Distribution (PDF)</label>
            </div>

            <div class="flex items-center p-2 bg-green-50 rounded border border-green-200">
                <input type="checkbox" id="regen-distri-excel" class="h-5 w-5 text-green-600 border-gray-300 rounded">
                <label for="regen-distri-excel" class="ml-3 block text-sm font-bold text-green-800">📊 Liste de Distribution (Format EXCEL)</label>
            </div>

            <div class="flex items-center p-2 bg-pink-50 rounded border border-pink-100">
                <input type="checkbox" id="regen-labels" class="h-5 w-5 text-pink-600 border-gray-300 rounded">
                <label for="regen-labels" class="ml-3 block text-sm font-bold text-pink-900">🏷️ Étiquettes Sacs (PDF)</label>
            </div>
        </div>`;
    }

    // Option Brodelec
    if (hasBrodelecItems) {
        html += `
        <div class="mt-2 border-t pt-2">
            <div class="flex items-center p-2 bg-green-50 rounded border border-green-100">
                <input type="checkbox" id="regen-brodelec" class="h-5 w-5 text-green-600 border-gray-300 rounded" checked>
                <label for="regen-brodelec" class="ml-3 block text-sm font-bold text-green-900">🛡️ Commande BRODELEC (PDF)</label>
            </div>
        </div>`;
    }

    container.innerHTML = html;

    // --- 3. ACTIONS ---
    const actions = [
        { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-400' },
        { 
            label: 'Lancer les éditions', 
            className: 'bg-indigo-600 text-white font-bold',
            onClick: () => {
                hideModal(dom.mainModal);
                let count = 0;

                // PDF NORET
                if (document.getElementById('regen-noret')?.checked) { 
                    handleExportPdf(); 
                    count++; 
                }
                
                // PDF CLUB
                if (document.getElementById('regen-club')?.checked) { 
                    setTimeout(() => handleExportClubOrderPdf(), 200); 
                    count++; 
                }
                
                if (isLicenseeMode) {
                    // PDF DETAIL FINANCIER
                    if (document.getElementById('regen-detail')?.checked) { 
                        setTimeout(() => handleExportDetailedPdf(), 400); 
                        count++; 
                    }
                    // PDF DISTRIBUTION
                    if (document.getElementById('regen-distri')?.checked) { 
                        setTimeout(() => handleExportDistributionList(), 600); 
                        count++; 
                    }
                    // EXCEL DISTRIBUTION (NOUVEAU)
                    if (document.getElementById('regen-distri-excel')?.checked) { 
                        setTimeout(() => handleExportDistributionExcel(), 700); 
                        count++; 
                    }
                    // PDF ETIQUETTES
                    if (document.getElementById('regen-labels')?.checked) { 
                        setTimeout(() => handleExportLabels(), 800); 
                        count++; 
                    }
                }

                // PDF BRODELEC
                if (document.getElementById('regen-brodelec')?.checked) { 
                    setTimeout(() => handleExportBrodelecPdf(), 1000); 
                    count++; 
                }

                if (count > 0) showToast(`${count} document(s) généré(s).`);
            } 
        }
    ];

    showModal(dom.mainModal, "Ré-édition de documents", container, actions);
};
// Affiche la fenêtre de demande du N° de précommande
const promptForPreOrderNumber = (requestedScope) => {
    const container = document.createElement('div');
    container.innerHTML = `
        <div class="space-y-4">
            <div class="bg-blue-50 p-4 rounded-lg border-l-4 border-blue-500 text-blue-900">
                <p class="font-bold">🔐 Authentification Commande</p>
                <p class="text-sm">Pour commencer ou reprendre une saisie, veuillez entrer le <strong>Numéro de Précommande</strong> fourni.</p>
            </div>
            <div>
                <label class="block text-sm font-bold text-gray-700 uppercase">N° Précommande</label>
                <input type="text" id="modal-preorder-input" class="mt-1 block w-full text-center text-xl font-black tracking-widest border-2 border-indigo-300 rounded-md p-2 uppercase focus:ring-indigo-500 focus:border-indigo-500" placeholder="EX: CDE-2024-01">
            </div>
        </div>
    `;

    showModal(dom.mainModal, 'Ouverture de session', container, [
        { label: 'Annuler', className: 'bg-gray-400', onClick: () => hideModal(dom.mainModal) },
        { 
            label: 'OUVRIR LA COMMANDE', 
            className: 'bg-indigo-600 text-white font-bold', 
            onClick: () => {
                const val = document.getElementById('modal-preorder-input').value.trim().toUpperCase();
                if(!val) { showToast("Veuillez saisir un numéro.", "error"); return; }
                
                hideModal(dom.mainModal);
                loadOrderSession(val, requestedScope);
            }
        }
    ]);

    // Focus automatique
    setTimeout(() => document.getElementById('modal-preorder-input').focus(), 100);
};

// Charge les données spécifiques à ce numéro de commande depuis Firebase
const loadOrderSession = async (orderId, scope) => {
    if (!currentClubCode) return;

    showToast("Chargement de la session...", "info");

    // 1. On définit le numéro dans l'état
    state.preOrderNumber = orderId;
    document.getElementById('preOrderNumber').value = orderId;

    // 2. On interroge Firebase pour voir si cette commande existe déjà
    // Structure : clubs/[CODE_CLUB]/orders/[NUMERO_COMMANDE]
    const orderRef = ref(db, `clubs/${currentClubCode}/orders/${orderId}`);
    
    try {
        const snapshot = await get(orderRef);
        
        if (snapshot.exists()) {
            // CAS A : La commande existe déjà -> ON RESTAURE
            const orderData = snapshot.val();
            state.currentOrderLineItems = orderData.items || [];
            state.orderSpecificity = orderData.specificity || '';
            state.orderDate = orderData.date || new Date().toISOString().split('T')[0];
            
            // Restaure le mode de saisie (si on avait commencé en global, on reste en global)
            state.orderScope = orderData.scope || scope; 
            
            showToast(`📂 Commande "${orderId}" retrouvée (${state.currentOrderLineItems.length} articles).`, "success");
        } else {
            // CAS B : C'est une nouvelle commande -> ON VIDE LE PANIER
            state.currentOrderLineItems = [];
            state.orderSpecificity = '';
            state.orderScope = scope; // On applique le mode choisi
            
            showToast(`✨ Nouvelle commande "${orderId}" initialisée.`, "success");
        }

        // 3. Mise à jour de l'interface
        // On coche le bon bouton radio
        if (state.orderScope === 'global') document.getElementById('scope-global').checked = true;
        if (state.orderScope === 'licensee') document.getElementById('scope-licensee').checked = true;
        
        dom.licencieNameContainer.classList.toggle('hidden', state.orderScope !== 'licensee');
        dom.orderSpecificity.value = state.orderSpecificity;
        
        renderAll();
        
        // Petite sauvegarde immédiate pour verrouiller le fichier sur le cloud
        syncToCloud(true);

    } catch (error) {
        console.error("Erreur chargement session", error);
        showToast("Erreur de connexion au serveur.", "error");
    }
};   const handleValidateOrder = () => {
    // 1. Vérifications de base
    if (!state.clubName || !state.departement) {
        showToast('Le nom du club et le département sont obligatoires.', 'error');
        return;
    }
    if (state.currentOrderLineItems.length === 0) {
        showToast('Impossible de valider une commande vide.', 'error');
        return;
    }

    // 2. Calculs des quantités NORET
    const noretItemCount = state.currentOrderLineItems.reduce((sum, item) => {
        const product = productMap.get(item.productName);
        const isBrodelec = product && product.supplier === 'BRODELEC';
        if (!isBrodelec && product && product.type !== 'accessoire') {
            return sum + item.totalQuantity;
        }
        return sum;
    }, 0);

    const hasNoretItems = state.currentOrderLineItems.some(item => {
        const product = productMap.get(item.productName);
        return !product || product.supplier !== 'BRODELEC';
    });

    // 3. Fonction relais pour la suite
    const nextStep = () => checkAccessoryMinimumsAndProceed();

    // --- A. VÉRIFICATION GLOBALE (10 pièces ou Réassort) ---
    if (state.isReassort) {
        if (noretItemCount < 10) {
            const p = document.createElement('div');
            p.innerHTML = `
                <div class="bg-yellow-50 border-l-4 border-yellow-400 p-4 text-yellow-800">
                    <p class="font-bold">⚠️ Minimum global de 10 pièces non atteint</p>
                    <p class="text-sm mt-1">Quantité actuelle : <strong>${noretItemCount}</strong> article(s).</p>
                    <p class="text-sm mt-2">Des frais de port s'appliqueront.</p>
                </div>`;
            
            showModal(dom.mainModal, 'Confirmation Exceptionnelle', p, [
                { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300' },
                { label: 'Valider quand même', className: 'bg-red-600 text-white font-bold', onClick: () => { hideModal(dom.mainModal); checkRestockPricesAndProceed(nextStep); } }
            ]);
            return;
        }
    } else {
        // Commande Initiale (Strict < 10)
        if (hasNoretItems && noretItemCount < 10) {
            const p = document.createElement('p');
            p.innerHTML = `<b>Validation Commande NORET :</b> Minimum 10 articles requis.<br>Actuel : <b>${noretItemCount}</b>.`;
            showModal(dom.mainModal, 'Validation Impossible', p, [
                { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300' },
                { label: 'Forcer (Admin)', onClick: () => promptForForceCode(() => checkRestockPricesAndProceed(nextStep)), className: 'bg-orange-500 hover:bg-orange-600' }
            ]);
            return;
        }
    }

    // Si tout est OK, on vérifie les prix réassort
    checkRestockPricesAndProceed(nextStep);
};

const checkRestockPricesAndProceed = (callback) => {
    const downgradedItems = [];

    if (state.isReassort) {
        // Calcul des totaux par produit
        const productTotals = {};
        state.currentOrderLineItems.forEach(i => {
             productTotals[i.productName] = (productTotals[i.productName] || 0) + i.totalQuantity;
        });

        // Recherche des articles qui perdent le tarif préférentiel (< 2 pièces)
        for (const [name, totalQty] of Object.entries(productTotals)) {
             const rangeItem = state.clubProductRange.find(r => r.name === name);
             if (rangeItem && rangeItem.lastRestockPrice) {
                 if (totalQty < 2) {
                     const hasManualOverride = state.currentOrderLineItems.some(i => i.productName === name && i.isManualPrice);
                     if (!hasManualOverride) {
                         downgradedItems.push({ name: name, qty: totalQty });
                     }
                 }
             }
        }
    }

    if (downgradedItems.length > 0) {
        const p = document.createElement('div');
        p.className = "space-y-3";
        const itemsList = downgradedItems.map(i => `<li><strong>${i.name}</strong> (Total: ${i.qty})</li>`).join('');
        
        p.innerHTML = `
            <div class="bg-orange-50 border-l-4 border-orange-500 p-4 text-orange-800">
                <p class="font-bold text-lg">⚠️ Attention : Perte du Tarif Réassort</p>
                <p class="text-sm mt-2">Les articles suivants ne respectent pas le minimum de <strong>2 pièces</strong> (tous licenciés confondus).</p>
                <p class="text-sm mt-1">Ils passeront au <strong>tarif standard</strong>.</p>
            </div>
            <ul class="list-disc pl-5 text-sm text-gray-700 mt-2 bg-gray-50 p-2 rounded border">
                ${itemsList}
            </ul>
        `;

        showModal(dom.mainModal, 'Ajustement Tarifaire', p, [
            { label: 'Corriger (Retour)', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-500 text-white' },
            { 
                label: 'Accepter & Continuer', 
                className: 'bg-indigo-600 text-white font-bold', 
                onClick: () => { 
                    hideModal(dom.mainModal); 
                    callback(); 
                } 
            }
        ]);
    } else {
        callback();
    }
};

const checkAccessoryMinimumsAndProceed = () => {
    const accessoryErrors = [];
    const keywords = ['GANTS', 'MANCHETTES', 'JAMBIERES', 'CHAUSSETTES', 'BANDEAU', 'TOUR DE COU', 'GAPETTE', 'SOUS-MAILLOT', 'COUVRE-CHAUSSURES', 'CAGOULE', 'SOUS CASQUE'];

    // On scanne tous les produits personnalisés qui ont un minimum requis > 1
    allAvailableProducts.filter(p => p.subtype === 'ACCESSOIRES PERSONNALISÉS' && p.minQuantity > 1).forEach(product => {
        let totalQuantityInCart = 0;
        
        // Calcul de la quantité dans le panier
        state.currentOrderLineItems.forEach(item => {
            if (!item.isFromStock) {
                if (item.type === 'pack') {
                    item.packItems.forEach(packItem => {
                        if (packItem.productName === product.name) totalQuantityInCart += packItem.quantity;
                    });
                } else {
                    if (item.productName === product.name) totalQuantityInCart += item.totalQuantity;
                }
            }
        });
        
        // Si on en commande, mais pas assez
        if (totalQuantityInCart > 0 && totalQuantityInCart < product.minQuantity) {
             let substitutes = [];
             const keyword = keywords.find(k => product.name.toUpperCase().includes(k));
             if (keyword) {
                 substitutes = allAvailableProducts.filter(p => 
                    p.subtype === 'ACCESSOIRES PERMANENTS' && 
                    p.name.toUpperCase().includes(keyword)
                 );
             }

             accessoryErrors.push({
                 product: product,
                 qty: totalQuantityInCart,
                 min: product.minQuantity,
                 substitutes: substitutes
             });
        }
    });

    if (accessoryErrors.length > 0) {
        const content = document.createElement('div');
        content.className = 'space-y-4 max-h-[60vh] overflow-y-auto pr-1';
        
        content.innerHTML = `
            <div class="bg-red-50 border-l-4 border-red-500 p-3 text-red-700 text-sm mb-4">
                <p class="font-bold">⚠️ Minimums de production non atteints</p>
                <p>Les accessoires personnalisés suivants n'atteignent pas le quota minimum.</p>
                <p class="mt-1">Vous devez soit atteindre le minimum, soit <strong>supprimer</strong> ces articles.</p>
            </div>
        `;
        
        accessoryErrors.forEach(err => {
            let subHtml = '';
            if (err.substitutes.length > 0) {
                subHtml = `
                    <div class="mt-3 bg-white p-2 rounded border border-gray-200">
                        <p class="text-xs font-bold text-gray-500 mb-1">💡 Suggestions Gamme Permanente (Sans minimum) :</p>
                        <ul class="text-xs text-indigo-700 space-y-1">
                            ${err.substitutes.map(s => `<li>• ${s.name} (${s.price.toFixed(2)}€)</li>`).join('')}
                        </ul>
                    </div>
                `;
            }

            content.innerHTML += `
                <div class="p-4 border rounded-lg bg-gray-50 shadow-sm relative">
                    <h5 class="font-bold text-gray-800">${err.product.name}</h5>
                    <div class="flex items-center gap-2 mt-1 text-sm">
                        <span class="text-red-600 font-bold">Actuel : ${err.qty}</span>
                        <span class="text-gray-400">/</span>
                        <span class="text-gray-600">Requis : ${err.min}</span>
                    </div>
                    ${subHtml}
                    <div class="mt-3 flex justify-end">
                        <button data-action="remove-accessory-batch" data-product-name="${err.product.name}" class="px-4 py-2 bg-red-600 text-white text-sm font-bold rounded shadow hover:bg-red-700 flex items-center gap-2">
                            <span>🗑️ Supprimer l'article</span>
                        </button>
                    </div>
                </div>
            `;
        });

        content.addEventListener('click', (e) => {
             const btn = e.target.closest('button');
             if (btn && btn.dataset.action === 'remove-accessory-batch') {
                 const pName = btn.dataset.productName;
                 if (confirm(`Voulez-vous supprimer TOUTES les lignes contenant "${pName}" ?`)) {
                     state.currentOrderLineItems = state.currentOrderLineItems.filter(i => {
                         if (i.type === 'pack') return true; 
                         return i.productName !== pName;
                     });
                     renderAll();
                     hideModal(dom.mainModal);
                     showToast(`${pName} supprimé.`);
                     setTimeout(() => handleValidateOrder(), 300);
                 }
             }
        });

        showModal(dom.mainModal, 'Problème de Minimums', content, [
            { label: 'Fermer pour corriger', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-500' }
        ]);
        return;
    }

    proceedToValidation();
};

const proceedToValidation = () => {
    showFinalValidationModal();
};

const showFinalValidationModal = () => {
    const defaultCheck = !state.isLoadedFromBackup;

    const container = document.createElement('div');
    container.innerHTML = `
        <p class="mb-4">Êtes-vous sûr de vouloir finaliser cette commande ?</p>
        <div class="bg-yellow-50 p-3 rounded-md border border-yellow-200">
            <div class="flex items-center">
                <input id="update-stock-on-validate" type="checkbox" class="h-5 w-5 text-indigo-600 border-gray-300 rounded" ${defaultCheck ? 'checked' : ''}>
                <label for="update-stock-on-validate" class="ml-3 block text-sm font-bold text-gray-800">
                    Mettre à jour le stock et l'historique
                </label>
            </div>
            <p class="mt-1 text-xs text-gray-500 ml-8">
                Décochez cette case si vous rééditez une commande déjà validée.
            </p>
        </div>
        <p class="mt-4 text-xs text-gray-500">Note : L'export HTML a été désactivé pour simplifier le processus.</p>
    `;

    showModal(dom.mainModal, 'Confirmer la validation', container, [
        { label: 'Retour', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-400 text-black' },
        {
            label: 'VALIDER & GÉNÉRER PDF',
            className: 'bg-indigo-600 text-white font-bold shadow-lg',
            onClick: () => {
                const shouldUpdateStock = document.getElementById('update-stock-on-validate').checked;
                hideModal(dom.mainModal);
                finalizeValidationProcess(shouldUpdateStock); 
            }
        }
    ]);
};

const finalizeValidationProcess = (shouldUpdateStock) => {
    // 1. Sauvegarde des infos club en local
    saveClientInfo();
    const todayStr = new Date().toLocaleDateString('fr-FR').replace(/\//g, '-');
    
    // On utilise le N° de Précommande pour nommer les fichiers, s'il existe
    const preOrderRef = state.preOrderNumber ? `_${state.preOrderNumber.trim()}` : '';
    const safeClubName = state.clubName.replace(/ /g, '_');

    // 2. MISE À JOUR DU STOCK (Si coché)
    // Cela permet de mettre à jour le "Stock Club" directement dans l'appli permanente
    if (shouldUpdateStock) {
        const stockOrderItems = state.currentOrderLineItems.filter(item => item.isStockOrder);
        if (stockOrderItems.length > 0) {
            stockOrderItems.forEach(item => {
                for (const size in item.quantitiesPerSize) {
                    const qtyToAdd = parseInt(item.quantitiesPerSize[size], 10) || 0;
                    if (qtyToAdd > 0) {
                        if (!state.clubStock[item.productName]) state.clubStock[item.productName] = {};
                        state.clubStock[item.productName][size] = (state.clubStock[item.productName][size] || 0) + qtyToAdd;
                    }
                }
            });
        }
        saveStock(); // Sauvegarde locale
        
        // Historique
        const history = JSON.parse(localStorage.getItem('documentHistory') || '[]');
        history.push({ ...state, id: Date.now() });
        localStorage.setItem('documentHistory', JSON.stringify(history));
    }

    // 3. SAUVEGARDE DE L'ARCHIVE COMMANDE (JSON)
    // C'est ce fichier que le club vous envoie ou garde comme preuve de SA commande ponctuelle
    try {
        const jsonFilename = `ARCHIVE_COMMANDE_${safeClubName}${preOrderRef}_${todayStr}.json`;
        
        // On ne sauvegarde QUE l'état de la commande, pas toute l'appli
        const orderSnapshot = {
            clubName: state.clubName,
            preOrderNumber: state.preOrderNumber,
            date: state.orderDate,
            items: state.currentOrderLineItems,
            totals: calculateAllTotals()
        };

        const dataStr = JSON.stringify(orderSnapshot, null, 2);
        const blob = new Blob([dataStr], { type: "application/json" });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = jsonFilename;
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        URL.revokeObjectURL(url);
    } catch (e) { console.error("Erreur export JSON", e); }

    // 4. GÉNÉRATION DES PDF
    setTimeout(() => {
        const hasNoretItems = state.currentOrderLineItems.some(item => {
            const p = productMap.get(item.productName);
            return !p || p.supplier !== 'BRODELEC';
        });
        const hasBrodelecItems = state.currentOrderLineItems.some(item => {
            const p = productMap.get(item.productName);
            return p && p.supplier === 'BRODELEC';
        });

        if (hasNoretItems) handleExportPdf();
        
        handleExportClubOrderPdf();

        if (state.orderScope === 'licensee') {
            handleExportDetailedPdf();
            setTimeout(() => handleExportDistributionList(), 500);
        }

        if (hasBrodelecItems) {
            setTimeout(() => handleExportBrodelecPdf(), 1000);
        }
        
        // 5. FIN DU PROCESSUS
        // On synchronise le tout sur le Cloud (Le stock est à jour, la commande est passée)
        if (currentClubCode) window.syncToCloud(true);

        localStorage.removeItem('autosavedOrder');
        
        showToast('✅ Commande validée et archivée !', 'success');
        
        // Optionnel : Proposer de vider le panier pour la prochaine fois
        setTimeout(() => {
             if(confirm("Commande générée avec succès !\n\nVoulez-vous vider le panier pour préparer une future commande ?\n(Le stock et les infos du club seront conservés).")) {
                 resetOrderDetails();
             }
        }, 2000);

        renderDashboard();
    }, 1000);
};const handleExportPdf = () => {
    const { jsPDF } = window.jspdf;
    const doc = new jsPDF();
    const pageWidth = doc.internal.pageSize.width;
    const rightMargin = pageWidth - 14;

    const formatDateForDisplay = (dateString) => {
        if (!dateString) return 'N/A';
        const parts = dateString.split('-');
        if (parts.length !== 3) return dateString;
        return `${parts[2]}/${parts[1]}/${parts[0]}`;
    };

    const generatePdfContent = () => {
        let headerEndY = 0;
        doc.setFontSize(18);
        doc.setFont(undefined, 'bold');
        
        if (state.isReassort) {
            doc.setTextColor(200, 0, 0); 
            doc.text("PRÉVISIONNEL DE COMMANDE - RÉASSORT 2 MOIS", 14, 22);
        } else {
            doc.setTextColor(0); 
            doc.text("PRÉVISIONNEL DE COMMANDE", 14, 22);
        }

        doc.setFontSize(11);
        doc.setTextColor(100);
        
        doc.text(`Nom du Club: ${state.clubName}`, 14, 32);
        doc.text(`Département: ${state.departement || 'N/A'}`, 14, 38);
        doc.text(`N° Client: ${state.clientNumber || 'N/A'}`, 14, 44);

        let rightColY = 32;

        doc.text(`N° Précommande: ${state.preOrderNumber || 'N/A'}`, rightMargin, rightColY, { align: 'right' });
        rightColY += 6;

        if (state.isReassort && state.previousOrderReference) {
            doc.setTextColor(200, 0, 0); 
            doc.setFont(undefined, 'bold');
            doc.text(`Réf. Cde Initiale: ${state.previousOrderReference}`, rightMargin, rightColY, { align: 'right' });
            doc.setFont(undefined, 'normal'); 
            doc.setTextColor(100); 
            rightColY += 6;
        }

        doc.text(`Date Commande: ${formatDateForDisplay(state.orderDate)}`, rightMargin, rightColY, { align: 'right' });
        rightColY += 6;
        doc.text(`Départ Usine Prévu: ${formatDateForDisplay(state.factoryDepartureDate)}`, rightMargin, rightColY, { align: 'right' });
        
        headerEndY = 58; 
        doc.setFont(undefined, 'bold'); doc.setTextColor(0);
        doc.text('Informations de Livraison', 14, headerEndY);
        doc.setFont(undefined, 'normal'); doc.setTextColor(100);
        const deliveryText = `Adresse: ${state.deliveryAddress || 'Non renseignée'}\nContact: ${state.deliveryContact || 'Non renseigné'}\nEmail: ${state.deliveryEmail || 'Non renseigné'}`;
        const splitText = doc.splitTextToSize(deliveryText, doc.internal.pageSize.width - 28);
        doc.text(splitText, 14, headerEndY + 6);
        headerEndY += (splitText.length * 6) + 4;
        
        doc.setTextColor(0);
        if (state.orderSpecificity.trim()) {
            const specificityLines = doc.splitTextToSize(state.orderSpecificity, doc.internal.pageSize.width - 28);
            const requiredHeight = 12 + (specificityLines.length * 6);
            if (headerEndY + requiredHeight > doc.internal.pageSize.height - 20) { doc.addPage(); headerEndY = 20; }
            doc.setFont(undefined, 'bold');
            doc.text('Spécificité Commande:', 14, headerEndY + 8);
            doc.setFont(undefined, 'normal');
            doc.text(specificityLines, 14, headerEndY + 14);
            headerEndY += requiredHeight;
        }

        // === CONSOLIDATION STRICTE ===
        const itemsForProduction = state.currentOrderLineItems.filter(item => {
            const product = productMap.get(item.productName);
            const isBrodelec = product && product.supplier === 'BRODELEC';
            return !item.isFromStock && !isBrodelec; 
        });

        const normalizedItems = itemsForProduction.flatMap(item => {
            const normalize = (baseItem, sizeMap) => ({
                productName: (baseItem.productName || '').trim(),
                color: (baseItem.color || '').trim(),
                visual: (baseItem.visual || '').trim(),
                options: Array.isArray(baseItem.options) ? [...baseItem.options].sort() : [],
                specificity: (baseItem.specificity || '').trim(),
                quantitiesPerSize: sizeMap || baseItem.quantitiesPerSize || {},
                pricingGroup: productMap.get(baseItem.productName)?.pricingGroup || null,
                isManualPrice: baseItem.isManualPrice || false,
                initialManualPrice: (typeof baseItem.initialManualPrice === 'number') ? baseItem.initialManualPrice : 0,
            });

            if (item.type === 'pack') {
                return (item.packItems || []).map(packItem => {
                    const sizeMap = { [(packItem.size || 'N/A')]: packItem.quantity || 1 };
                    return normalize(packItem, sizeMap);
                });
            } else {
                return [normalize(item)];
            }
        });

        // Calcul Group Totals (Avec Historique)
        const groupTotals = normalizedItems.reduce((acc, item) => {
             if (item.pricingGroup) {
                 const itemQty = Object.values(item.quantitiesPerSize).reduce((sum, q) => sum + (q || 0), 0);
                 acc[item.pricingGroup] = (acc[item.pricingGroup] || 0) + itemQty;
             }
             return acc;
        }, {});

        if (state.isReassort) {
            state.clubProductRange.forEach(rangeItem => {
                if (rangeItem.lastRestockRefQty && rangeItem.lastRestockRefQty > 0) {
                    const product = productMap.get(rangeItem.name);
                    if (product && product.pricingGroup) {
                        groupTotals[product.pricingGroup] = (groupTotals[product.pricingGroup] || 0) + rangeItem.lastRestockRefQty;
                    }
                }
            });
        }
        if (state.packBaseQuantity > 0) {
            state.clubPacks.forEach(packDefinition => {
                packDefinition.productNames.forEach(productName => {
                    const product = productMap.get(productName);
                    if (product && product.pricingGroup) {
                        groupTotals[product.pricingGroup] = (groupTotals[product.pricingGroup] || 0) + state.packBaseQuantity;
                    }
                });
            });
        }

        const consolidatedItems = {};
        normalizedItems.forEach((item) => {
            const optionsString = JSON.stringify(item.options);
            // Clé STRICTE (avec .trim() pour éviter les bugs d'espaces)
            // ON EXCLUT LA NOTE INTERNE
            const key = `${item.productName.trim()}|${item.color.trim()}|${item.visual.trim()}|${optionsString}|${item.specificity.trim()}`;

            let existing = consolidatedItems[key];
            if (!existing) {
                existing = { ...item, quantitiesPerSize: {} };
                consolidatedItems[key] = existing;
            }

            if (item.quantitiesPerSize && typeof item.quantitiesPerSize === 'object') {
                for (const size in item.quantitiesPerSize) {
                    const qty = item.quantitiesPerSize[size] || 0;
                    if (qty > 0) existing.quantitiesPerSize[size] = (existing.quantitiesPerSize[size] || 0) + qty;
                }
            }
        });

        const finalBodyData = Object.values(consolidatedItems).map(item => {
            item.totalQuantity = Object.values(item.quantitiesPerSize).reduce((sum, q) => sum + (q || 0), 0);
            const pricingQuantity = item.pricingGroup ? (groupTotals[item.pricingGroup] || item.totalQuantity) : item.totalQuantity;
            
            const rangeItem = state.clubProductRange.find(r => r.name === item.productName);

            // 1. Cadeau 0€
            if (item.isManualPrice && item.initialManualPrice === 0) {
                 item.unitPriceTTC = 0;
            } 
            // 2. FORCE RÉASSORT (Si > 2 pièces)
            else if (state.isReassort && rangeItem && rangeItem.lastRestockPrice && item.totalQuantity >= 2) {
                 item.unitPriceTTC = rangeItem.lastRestockPrice;
            }
            // 3. Admin Force
            else if (item.isManualPrice && typeof item.initialManualPrice === 'number') {
                 item.unitPriceTTC = item.initialManualPrice;
            } 
            // 4. Standard
            else {
                 item.unitPriceTTC = getUnitPriceTTC(item.productName, pricingQuantity, item.options || []);
            }

            item.unitPriceHT = item.unitPriceTTC / (1 + TVA_RATE);
            return item;
        }).filter(item => item.totalQuantity > 0);

        const finalTotalQty = finalBodyData.reduce((sum, item) => sum + item.totalQuantity, 0);

        const head = [['Produit', 'Tailles', 'Qté', 'Prix U. HT', 'Prix U. TTC', 'Total HT', 'Total TTC']];
        const body = finalBodyData.map((item) => {
            let productName = item.productName;
            if (item.color) productName += `\n(${item.color})`;
            if (item.visual) productName += `\nVisuel: ${item.visual}`;
            if (item.options && item.options.length > 0) productName += `\nOptions: ${item.options.join(', ')}`;
            if (item.specificity) productName += `\nNOTE : ${item.specificity}`; 

            const unitPriceHT = item.unitPriceHT || 0;
            const unitPriceTTC = item.unitPriceTTC || 0;
            const totalLinePriceHT = unitPriceHT * item.totalQuantity;
            const totalLinePriceTTC = unitPriceTTC * item.totalQuantity;
            const sizesText = getSortedSizesText(item);
            const safeFixed = (num, places) => (typeof num === 'number' ? num.toFixed(places) : '0.00');

            return [
                productName, sizesText, item.totalQuantity,
                `${safeFixed(unitPriceHT, 2)} €`, `${safeFixed(unitPriceTTC, 2)} €`,
                `${safeFixed(totalLinePriceHT, 2)} €`, `${safeFixed(totalLinePriceTTC, 2)} €`
            ];
        });

       doc.autoTable({
            startY: headerEndY + 2,
            head,
            body,
            theme: 'striped',
            headStyles: { fillColor: [41, 128, 185], textColor: 255 },
            styles: { cellPadding: 2, fontSize: 8, valign: 'middle' },
            columnStyles: { 0: { cellWidth: 65 }, 1: { cellWidth: 35 } },
            didParseCell: function (data) {
                if (data.section === 'body' && data.column.index === 0) {
                    const text = data.cell.raw;
                    if (text && typeof text === 'string') {
                        if (text.includes('Visuel:') || text.includes('Options:') || text.includes('NOTE :')) {
                            data.cell.styles.textColor = [220, 20, 60]; 
                        }
                    }
                }
            }
        });
        
        // ... (Reste de la fonction avec les totaux, identique à avant) ...
        let finalY = doc.autoTable.previous.finalY + 10;
        const totalsX = 135;
        if (finalY > (doc.internal.pageSize.height - 70)) { doc.addPage(); finalY = 20; }
        
        doc.setFontSize(10); doc.setTextColor(0); doc.setFont(undefined, 'normal');
        const finalSubtotalHT = finalBodyData.reduce((sum, item) => sum + ((item.unitPriceHT || 0) * item.totalQuantity), 0);
        const finalSubtotalTTC = finalBodyData.reduce((sum, item) => sum + ((item.unitPriceTTC || 0) * item.totalQuantity), 0);
        let finalShippingHT = 0; if (finalSubtotalHT > 2000) finalShippingHT = 0; else if (finalSubtotalHT > 1000) finalShippingHT = 14; else if (finalSubtotalHT > 500) finalShippingHT = 12; else if (finalSubtotalHT > 0) finalShippingHT = 9.50;
        const finalShippingTTC = finalShippingHT * (1 + TVA_RATE);
        const finalGraphicFeeTTC = (state.isCustomCreation && finalBodyData.reduce((sum, item) => { const prod = productMap.get(item.productName); return (prod && prod.type !== 'accessoire') ? sum + item.totalQuantity : sum; }, 0) < 20) ? GRAPHIC_FEE_TTC : 0;
        const finalGrandTotalTTC = finalSubtotalTTC + finalShippingTTC + finalGraphicFeeTTC;
        const finalDownPayment = finalGrandTotalTTC * DOWN_PAYMENT_RATE;
        
        doc.setFont(undefined, 'bold');
        doc.text(`Nombre total d'articles :`, totalsX, finalY);
        doc.text(`${finalTotalQty}`, rightMargin, finalY, { align: 'right' });
        doc.setFont(undefined, 'normal');
        finalY += 6; 

        doc.text(`Sous-total HT:`, totalsX, finalY); doc.text(`${finalSubtotalHT.toFixed(2)} €`, rightMargin, finalY, { align: 'right' });
        finalY += 6; doc.text(`Sous-total TTC:`, totalsX, finalY); doc.text(`${finalSubtotalTTC.toFixed(2)} €`, rightMargin, finalY, { align: 'right' });
        finalY += 6; doc.text(`Frais de port TTC:`, totalsX, finalY); doc.text(`${finalShippingTTC.toFixed(2)} €`, rightMargin, finalY, { align: 'right' });
        if (finalGraphicFeeTTC > 0) { finalY += 6; doc.text(`Forfait Création Graphique TTC:`, totalsX, finalY); doc.text(`${finalGraphicFeeTTC.toFixed(2)} €`, rightMargin, finalY, { align: 'right' }); }
        finalY += 8; doc.setFontSize(12); doc.setFont(undefined, 'bold');
        doc.text(`Total Général TTC:`, totalsX, finalY); doc.text(`${finalGrandTotalTTC.toFixed(2)} €`, rightMargin, finalY, { align: 'right' });
        if (state.documentType === 'commande') { finalY += 8; doc.setTextColor(28, 175, 28); doc.text(`Acompte 30%:`, totalsX, finalY); doc.text(`${finalDownPayment.toFixed(2)} €`, rightMargin, finalY, { align: 'right' }); }
        doc.setTextColor(0); doc.setFont(undefined, 'normal');

        const todayStr = new Date().toISOString().split('T')[0]; 
        doc.save(`PREVISIONNEL_NORET_${state.clubName.replace(/ /g, '_')}_${todayStr}.pdf`);
    };

    generatePdfContent();
};// =================================================================================
// --- PDF EXPORT FUNCTION ( Pour le Club ) --- AVEC P.U. REMISÉ TTC (CORRIGÉ) ---
// =================================================================================

const handleExportClubOrderPdf = () => {
    if (state.currentOrderLineItems.length === 0) return;

    const { jsPDF } = window.jspdf;
    const doc = new jsPDF();
    const pageWidth = doc.internal.pageSize.width;
    const rightMargin = pageWidth - 14;
    const margin = 14;

    const formatDateForDisplay = (dateString) => {
        if (!dateString) return 'N/A';
        const parts = dateString.split('-');
        if (parts.length !== 3) return dateString;
        return `${parts[2]}/${parts[1]}/${parts[0]}`;
    };

    doc.setFontSize(18); doc.setFont(undefined, 'bold');
    if (state.isReassort) { doc.setTextColor(200, 0, 0); doc.text("PRÉVISIONNEL CLUB - RÉASSORT 2 MOIS", margin, 22); }
    else { doc.setTextColor(0); doc.text("PRÉVISIONNEL DE COMMANDE CLUB", margin, 22); }

    doc.setFontSize(12); doc.setFont(undefined, 'bold'); doc.setTextColor(0);
    doc.text("Tarification Club NORET", margin, 29);
    
    doc.setFontSize(11); doc.setFont(undefined, 'normal'); doc.setTextColor(100);
    doc.text(`Nom du Club: ${state.clubName}`, margin, 38);
    doc.text(`Département: ${state.departement || 'N/A'}`, margin, 44);
    doc.text(`N° Client: ${state.clientNumber || 'N/A'}`, margin, 50);
    doc.text(`Date Commande: ${formatDateForDisplay(state.orderDate)}`, rightMargin, 44, { align: 'right' });
    
    let headerEndY = 58; doc.setTextColor(0); 

    const itemsForProduction = state.currentOrderLineItems.filter(item => !item.isFromStock);

    const normalizedItems = itemsForProduction.flatMap(item => {
        const normalize = (baseItem, sizeMap) => ({
            productName: (baseItem.productName || '').trim(),
            color: (baseItem.color || '').trim(),
            visual: (baseItem.visual || '').trim(),
            options: Array.isArray(baseItem.options) ? [...baseItem.options].sort() : [],
            specificity: (baseItem.specificity || '').trim(),
            internalNote: (baseItem.internalNote || '').trim(), // On garde la note
            quantitiesPerSize: sizeMap || baseItem.quantitiesPerSize || {},
            pricingGroup: productMap.get(baseItem.productName)?.pricingGroup || null,
            isManualPrice: baseItem.isManualPrice || false,
            initialManualPrice: (typeof baseItem.initialManualPrice === 'number') ? baseItem.initialManualPrice : 0,
        });

        if (item.type === 'pack') {
            return (item.packItems || []).map(packItem => {
                const sizeMap = { [(packItem.size || 'N/A')]: packItem.quantity || 1 };
                return normalize(packItem, sizeMap);
            });
        } else {
            return [normalize(item)];
        }
    });

    const groupTotals = normalizedItems.reduce((acc, item) => {
        if (item.pricingGroup) {
            const itemQty = Object.values(item.quantitiesPerSize || {}).reduce((sum, q) => sum + (parseInt(q, 10) || 0), 0);
            acc[item.pricingGroup] = (acc[item.pricingGroup] || 0) + itemQty;
        }
        return acc;
    }, {});
    
    if (state.isReassort) {
        state.clubProductRange.forEach(rangeItem => {
            if (rangeItem.lastRestockRefQty && rangeItem.lastRestockRefQty > 0) {
                const product = productMap.get(rangeItem.name);
                if (product && product.pricingGroup) {
                    groupTotals[product.pricingGroup] = (groupTotals[product.pricingGroup] || 0) + rangeItem.lastRestockRefQty;
                }
            }
        });
    }

    if (state.packBaseQuantity > 0) {
        state.clubPacks.forEach(packDefinition => {
            packDefinition.productNames.forEach(productName => {
                const product = productMap.get(productName);
                if (product && product.pricingGroup) {
                    groupTotals[product.pricingGroup] = (groupTotals[product.pricingGroup] || 0) + state.packBaseQuantity;
                }
            });
        });
    }

    const consolidatedItems = {};
    normalizedItems.forEach((item) => {
        const optionsString = JSON.stringify(item.options);
        // CORRECTION MAJEURE : On ENLÈVE la note de la clé pour fusionner les lignes
        const key = `${item.productName}|${item.color}|${item.visual}|${optionsString}|${item.specificity}`;

        let existing = consolidatedItems[key];
        if (!existing) {
            existing = {
                ...item,
                quantitiesPerSize: {},
                allNotes: new Set() // On crée un set pour stocker les notes uniques
            };
            consolidatedItems[key] = existing;
        }

        // On accumule les notes
        if (item.internalNote) existing.allNotes.add(item.internalNote);

        if (item.quantitiesPerSize && typeof item.quantitiesPerSize === 'object') {
            for (const size in item.quantitiesPerSize) {
                const qty = parseInt(item.quantitiesPerSize[size], 10) || 0;
                if (qty > 0) existing.quantitiesPerSize[size] = (existing.quantitiesPerSize[size] || 0) + qty;
            }
        }
    });

    const finalBodyData = Object.values(consolidatedItems).map(item => {
        item.totalQuantity = Object.values(item.quantitiesPerSize).reduce((sum, q) => sum + (q || 0), 0);
        const pricingQuantity = item.pricingGroup ? (groupTotals[item.pricingGroup] || item.totalQuantity) : item.totalQuantity;
        const rangeItem = state.clubProductRange.find(r => r.name === item.productName);

        // MEME LOGIQUE PRIX QUE NORET
        if (item.isManualPrice && item.initialManualPrice === 0) item.unitPriceTTC = 0;
        else if (state.isReassort && rangeItem && rangeItem.lastRestockPrice && item.totalQuantity >= 2) item.unitPriceTTC = rangeItem.lastRestockPrice;
        else if (item.isManualPrice && typeof item.initialManualPrice === 'number') item.unitPriceTTC = item.initialManualPrice;
        else item.unitPriceTTC = getUnitPriceTTC(item.productName, pricingQuantity, item.options || []);

        let discountRate = state.applyDiscount ? (state.clubDiscount || 0) : 0;
        item.finalUnitPriceTTC = item.unitPriceTTC * (1 - (discountRate / 100));
        item.finalUnitPriceHT = item.finalUnitPriceTTC / (1 + TVA_RATE);
        return item;
    }).filter(item => item.totalQuantity > 0);

    const head = [['Article', 'Tailles', 'Qté', 'P.U. TTC', 'Total TTC']];

    const body = finalBodyData.map(item => {
        let productNameDisplay = item.productName;
        if (item.color) productNameDisplay += `\n(${item.color})`;
        if (item.visual) productNameDisplay += `\nVisuel: ${item.visual}`;
        if (item.options && item.options.length > 0) productNameDisplay += `\nOptions: ${item.options.join(', ')}`;
        if (item.specificity) productNameDisplay += `\nNote: ${item.specificity}`;
        
        // --- AFFICHAGE DES NOTES REGROUPÉES ---
        if (item.allNotes && item.allNotes.size > 0) {
            // Convertit le Set en tableau et joint
            const notes = Array.from(item.allNotes).join(' / ');
            productNameDisplay += `\n🔒 NOTE CLUB : ${notes}`;
        }
        // -------------------------------------

        const sizesText = getSortedSizesText(item);
        const safeFixed = (num, places) => (typeof num === 'number' ? num.toFixed(places) : '0.00');
        const finalTotalTTC = item.finalUnitPriceTTC * item.totalQuantity;

        return [
            productNameDisplay,
            sizesText,
            item.totalQuantity,
            `${safeFixed(item.finalUnitPriceTTC, 2)} €`,
            `${safeFixed(finalTotalTTC, 2)} €`
        ];
    });

  doc.autoTable({
            startY: headerEndY + 2,
            head: head,
            body: body,
            theme: 'striped',
            headStyles: { fillColor: [41, 128, 185], textColor: 255 },
            styles: { cellPadding: 2, fontSize: 8, valign: 'middle', overflow: 'linebreak' }, 
            columnStyles: { 0: { cellWidth: 78 }, 1: { cellWidth: 39 }, 2: { cellWidth: 15, halign: 'center' }, 3: { cellWidth: 25, halign: 'right' }, 4: { cellWidth: 25, halign: 'right' } },
            didParseCell: function (data) {
                if (data.section === 'body' && data.column.index === 0) {
                    const text = data.cell.raw;
                    if (text && typeof text === 'string') {
                        if (text.includes('Visuel:') || text.includes('Options:') || text.includes('Note:') || text.includes('NOTE CLUB')) {
                            data.cell.styles.textColor = [220, 20, 60]; 
                        }
                    }
                }
            }
        });

    // ... (Suite inchangée avec les totaux, copiez simplement le bloc existant de la fonction précédente ou celle-ci dessous) ...
    const finalSubtotalTTC = finalBodyData.reduce((sum, item) => sum + (item.finalUnitPriceTTC * item.totalQuantity), 0);
    const finalSubtotalHT = finalSubtotalTTC / (1 + TVA_RATE); 
    const noretItemsOnly = finalBodyData.filter(item => { const prod = productMap.get(item.productName); return !prod || prod.supplier !== 'BRODELEC'; });
    const noretSubtotalTTC = noretItemsOnly.reduce((sum, item) => sum + (item.finalUnitPriceTTC * item.totalQuantity), 0);
    const noretSubtotalHT = noretSubtotalTTC / (1 + TVA_RATE);
    let finalShippingHT = 0; if (noretSubtotalHT > 2000) finalShippingHT = 0; else if (noretSubtotalHT > 1000) finalShippingHT = 14; else if (noretSubtotalHT > 500) finalShippingHT = 12; else if (noretSubtotalHT > 0) finalShippingHT = 9.50; 
    const finalShippingTTC = finalShippingHT * (1 + TVA_RATE);
    const finalTotalNonAccessoryQty = finalBodyData.reduce((sum, item) => { const prod = productMap.get(item.productName); const isBrodelec = prod && prod.supplier === 'BRODELEC'; return (prod && prod.type !== 'accessoire' && !isBrodelec) ? sum + item.totalQuantity : sum; }, 0);
    const finalGraphicFeeTTC = (state.isCustomCreation && finalTotalNonAccessoryQty < 20) ? GRAPHIC_FEE_TTC : 0;
    const finalGrandTotalTTC = finalSubtotalTTC + finalShippingTTC + finalGraphicFeeTTC;
    const finalDownPayment = finalGrandTotalTTC * DOWN_PAYMENT_RATE;
    
    let finalY = doc.autoTable.previous.finalY + 10;
    const totalsLabelX = 115; const totalsValueX = rightMargin; 
    if (finalY > (doc.internal.pageSize.height - 60)) { doc.addPage(); finalY = 20; }
    doc.setFontSize(10); doc.setTextColor(0); doc.setFont(undefined, 'normal');
    doc.text(`Sous-total TTC:`, totalsLabelX, finalY); doc.text(`${finalSubtotalTTC.toFixed(2)} €`, totalsValueX, finalY, { align: 'right' });
    finalY += 6; doc.text(`Frais de port TTC:`, totalsLabelX, finalY); doc.text(`${finalShippingTTC.toFixed(2)} €`, totalsValueX, finalY, { align: 'right' });
    if (finalGraphicFeeTTC > 0) { finalY += 6; doc.text(`Forfait Création Graphique TTC:`, totalsLabelX, finalY); doc.text(`${finalGraphicFeeTTC.toFixed(2)} €`, totalsValueX, finalY, { align: 'right' }); }
    finalY += 8; doc.setFontSize(12); doc.setFont(undefined, 'bold'); doc.text(`Total Général TTC:`, totalsLabelX, finalY); doc.text(`${finalGrandTotalTTC.toFixed(2)} €`, totalsValueX, finalY, { align: 'right' });
    if (state.documentType === 'commande') { finalY += 8; doc.setTextColor(28, 175, 28); doc.text(`Acompte Requis (30%):`, totalsLabelX, finalY); doc.text(`${finalDownPayment.toFixed(2)} €`, totalsValueX, finalY, { align: 'right' }); }
    doc.setTextColor(0); doc.setFont(undefined, 'normal'); 

    const todayStr = new Date().toISOString().split('T')[0];
    doc.save(`PREVISIONNEL_CLUB_${state.clubName.replace(/ /g, '_')}_${todayStr}.pdf`);
    showToast("Prévisionnel Club (PDF) généré !");
};
const handleExportDetailedPdf = () => {
    if (state.orderScope !== 'licensee') {
        showToast("L'export détaillé par licencié n'est disponible qu'en mode de saisie 'Par licencié'.", 'error');
        return;
    }
    if (state.currentOrderLineItems.length === 0) {
        showToast("La commande est vide, rien à exporter.", "error");
        return;
    }

    const { jsPDF } = window.jspdf;
    const doc = new jsPDF({ orientation: 'landscape' });
    const pageHeight = doc.internal.pageSize.height;
    const pageWidth = doc.internal.pageSize.width;
    const margin = 14;

    let globalClubEffort = 0; 
    const globalProductSummary = {};

    doc.setFontSize(16);
    doc.setFont(undefined, 'bold');
    
    let title = `Détail Financier par Licencié - ${state.clubName}`;
    if (state.isReassort) {
        title += " (RÉASSORT 2 MOIS)";
        doc.setTextColor(200, 0, 0);
    }
    doc.text(title, margin, 15);
    doc.setTextColor(0);

    doc.setFontSize(10);
    doc.setFont(undefined, 'normal');
    doc.text(`Date d'export: ${new Date().toLocaleDateString('fr-FR')}`, margin, 22);

    const groupedItems = state.currentOrderLineItems.reduce((acc, item) => {
        const key = item.licencieName || 'Commande Globale'; 
        if (!acc[key]) acc[key] = [];
        acc[key].push(item);
        return acc;
    }, {});

    const sortedLicensees = Object.keys(groupedItems).sort((a, b) => {
        if (a === 'Commande Globale' || a === 'Stock Club') return 1;
        if (b === 'Commande Globale' || b === 'Stock Club') return -1;
        return a.localeCompare(b);
    });

    let startY = 30;

    sortedLicensees.forEach(licensee => {
        if ((licensee === 'Commande Globale' || licensee === 'Stock Club') && !groupedItems[licensee]?.length) return;

        const rawLicenseeItems = groupedItems[licensee];
        if (!rawLicenseeItems || rawLicenseeItems.length === 0) return;

        // CONSOLIDATION
        const consolidatedItemsMap = {};
        
        rawLicenseeItems.forEach(item => {
            const optionsStr = (item.options || []).sort().join(',');
            // On regroupe par produit ET par note interne pour éviter les mélanges
            const key = `${item.productName}|${item.visual||''}|${optionsStr}|${item.specificity||''}|${item.color||''}|${item.internalNote||''}`;

            if (!consolidatedItemsMap[key]) {
                consolidatedItemsMap[key] = { 
                    ...item, 
                    quantitiesPerSize: { ...item.quantitiesPerSize },
                    totalQuantity: item.totalQuantity,
                    totalPriceTTC: item.totalPriceTTC, 
                    unitPriceTTC: item.unitPriceTTC 
                };
            } else {
                const existing = consolidatedItemsMap[key];
                existing.totalQuantity += item.totalQuantity;
                existing.totalPriceTTC += item.totalPriceTTC;
                for (const [size, qty] of Object.entries(item.quantitiesPerSize || {})) {
                    existing.quantitiesPerSize[size] = (existing.quantitiesPerSize[size] || 0) + qty;
                }
            }
        });
        
        const licenseeItems = Object.values(consolidatedItemsMap);

        let licenseeSubtotalFinal = 0; 
        let licenseeTotalInitialNoret = 0; 
        let licenseeTotalDiscountAmount = 0; 

        const head = [['Produit / Pack', 'Visuel', 'Spéc.', 'Options', 'Taille(s)', 'Qté', 'P.U. (Prix Club)', 'Total (Prix Club)']];

        const body = licenseeItems.map(item => {
            let finalItemTotalClub = 0;
            let displayProductName, displayVisual, displaySpecificity, displayOptions, displaySizes, displayQty, displayUnitPriceClub;
            
            const isStockItem = item.isStockOrder || item.isFromStock;
            const rangeItem = state.clubProductRange.find(rItem => rItem.name === item.productName);
            const hasFixedPrice = !item.isManualPrice && !isStockItem && rangeItem && typeof rangeItem.licenseePrice === 'number' && rangeItem.licenseePrice >= 0;

            displayVisual = item.visual || '';
            displaySpecificity = item.specificity || '';
            displayOptions = (item.options || []).join(', ');
            displayQty = item.totalQuantity || 0;
            
            displayProductName = item.productName || 'Article inconnu';
            if (item.isStockOrder) displayProductName = `[POUR STOCK] ${displayProductName}`;
            if (item.isFromStock) displayProductName = `[LIVRÉ STOCK] ${displayProductName}`;
            if (item.color) displayProductName += `\n(${item.color})`; 

            // CAS 1 : PACKS
            if (item.type === 'pack') {
                displayProductName = `PACK: ${item.productName}\n` + (item.packItems || []).map(pi => {
                    let itemLine = `- ${pi.productName} (${pi.size || 'N/A'})`;
                    if (pi.visual) itemLine += `\n  (Visuel: ${pi.visual})`;
                    if (pi.options && pi.options.length > 0) itemLine += `\n  (Options: ${pi.options.join(', ')})`;
                    return itemLine;
                }).join('\n');
                
                // CORRECTION : J'ai supprimé la ligne qui ajoutait la note ICI. 
                // Elle ne sera ajoutée qu'une seule fois à la fin du bloc.

                displaySizes = '-'; 
                displayVisual = '-'; 
                displayOptions = '-'; 
                
                const pricePaidTTC = item.totalPriceTTC || 0; 
                const realValueTTC = item.realPriceTTC || 0; 

                displayUnitPriceClub = `Pack: ${(pricePaidTTC / displayQty).toFixed(2)}€`;
                finalItemTotalClub = pricePaidTTC;
                
                if (realValueTTC > pricePaidTTC) {
                    licenseeTotalInitialNoret += realValueTTC;
                    licenseeTotalDiscountAmount += (realValueTTC - pricePaidTTC);
                } else {
                    licenseeTotalInitialNoret += pricePaidTTC;
                }
                
                licenseeSubtotalFinal += finalItemTotalClub;
            }
            // CAS 2 : PRIX FIXÉ PAR LE CLUB
            else if (hasFixedPrice) {
                displaySizes = getSortedSizesText(item);
                const fixedPrice = rangeItem.licenseePrice; 
                const noretPrice = item.unitPriceTTC || 0;
                const discountAmount = noretPrice - fixedPrice;
                displayUnitPriceClub = `${fixedPrice.toFixed(2)} € (Fixe)`;
                if (discountAmount > 0.01) {
                    licenseeTotalDiscountAmount += (discountAmount * item.totalQuantity);
                }
                finalItemTotalClub = fixedPrice * item.totalQuantity;
                licenseeTotalInitialNoret += (item.totalPriceTTC || 0);
                licenseeSubtotalFinal += finalItemTotalClub; 
            } 
            // CAS 3 : PRIX CALCULÉ
            else {
                displaySizes = getSortedSizesText(item); 
                const totalTTC = item.totalPriceTTC || 0;
                licenseeTotalInitialNoret += totalTTC; 
                const unitPrice = item.totalQuantity > 0 ? (totalTTC / item.totalQuantity) : 0;
                if (item.isManualPrice && totalTTC === 0) {
                     finalItemTotalClub = 0;
                     displayUnitPriceClub = `0.00 € (Offert)`;
                } else {
                    const effectiveDiscountRate = item.appliedDiscountRate || 0;
                    const discountAmountForItem = totalTTC * (effectiveDiscountRate / 100);
                    finalItemTotalClub = totalTTC - discountAmountForItem;
                    licenseeTotalDiscountAmount += discountAmountForItem; 
                    let displayPrice = unitPrice;
                    if (effectiveDiscountRate > 0) displayPrice = unitPrice * (1 - (effectiveDiscountRate / 100));
                    displayUnitPriceClub = `${displayPrice.toFixed(2)} €`;
                    if (effectiveDiscountRate > 0) displayUnitPriceClub += ` (-${effectiveDiscountRate}%)`;
                }
                if (isStockItem && totalTTC === 0) {
                    displayUnitPriceClub = '0.00 € (Stock)';
                    finalItemTotalClub = 0; 
                }
                licenseeSubtotalFinal += finalItemTotalClub; 
            }
            
            // --- AJOUT UNIQUE DE LA NOTE ---
            if (item.internalNote) {
                displayProductName += `\n🔒 Note: ${item.internalNote}`;
            }
            // -------------------------------

            const summaryKey = item.type === 'pack' 
                ? `PACK_${item.productName}` 
                : `${item.productName}|${item.visual||''}|${(item.options||[]).sort().join(',')}|${item.specificity||''}|${item.color||''}`;

            if (!globalProductSummary[summaryKey]) {
                globalProductSummary[summaryKey] = {
                    name: item.productName,
                    type: item.type,
                    visual: item.visual,
                    options: item.options,
                    specificity: item.specificity,
                    color: item.color,
                    totalQty: 0,
                    totalAmount: 0
                };
            }
            globalProductSummary[summaryKey].totalQty += item.totalQuantity;
            globalProductSummary[summaryKey].totalAmount += finalItemTotalClub;

            return [ displayProductName, displayVisual, displaySpecificity, displayOptions, displaySizes, displayQty, displayUnitPriceClub, `${finalItemTotalClub.toFixed(2)} €` ];
        }); 

        const tableHeightEstimate = (body.length + 1) * 10 + 30; 
        if (startY + tableHeightEstimate > pageHeight - 15) { doc.addPage(); startY = 20; }

        doc.setFontSize(12); doc.setFont(undefined, 'bold'); doc.setTextColor(0); 
        doc.text(licensee, margin, startY);
        doc.setFont(undefined, 'normal');

        doc.autoTable({
            startY: startY + 2,
            head: head,
            body: body,
            theme: 'grid',
            headStyles: { fillColor: [79, 70, 229], fontSize: 7, textColor: 255 },
            styles: { fontSize: 7, cellPadding: 1, overflow: 'linebreak', valign: 'middle' },
            columnStyles: { 0: { cellWidth: 70 }, 1: { cellWidth: 15 }, 2: { cellWidth: 20 }, 3: { cellWidth: 25 }, 4: { cellWidth: 15 }, 5: { cellWidth: 10, halign: 'center' }, 6: { cellWidth: 25, halign: 'right' }, 7: { cellWidth: 25, halign: 'right' } },
        });

        startY = doc.autoTable.previous.finalY + 5;

        if (licensee !== 'Commande Globale' && licensee !== 'Stock Club') {
            globalClubEffort += licenseeTotalDiscountAmount;
            const deposit = state.licenseeDeposits[licensee] || 0;
            const participation = state.applyClubParticipation ? (state.clubParticipationAmount || 0) : 0;
            if (state.applyClubParticipation) globalClubEffort += participation;
            const remaining = licenseeSubtotalFinal - participation - deposit;

            if (startY > pageHeight - 30) { doc.addPage(); startY = 20; }
            doc.setFontSize(9); doc.setFont(undefined, 'normal');
            
            let summaryText = `Total Final (Prix Licencié): ${licenseeSubtotalFinal.toFixed(2)}€`;
            if (licenseeTotalDiscountAmount > 0.005) { 
                summaryText = `(Total Tarif Public: ${licenseeTotalInitialNoret.toFixed(2)}€ | Avantage Club: -${licenseeTotalDiscountAmount.toFixed(2)}€) | ${summaryText}`;
            }
            if (participation > 0) summaryText += ` | Part. Club: -${participation.toFixed(2)}€`;
            summaryText += ` | Acompte Versé: ${deposit.toFixed(2)}€`;
            
            doc.setFont(undefined, 'bold');
            doc.text(summaryText, margin, startY + 5);
            doc.setTextColor(255, 0, 0); doc.text(`Restant Dû: ${remaining.toFixed(2)}€`, margin, startY + 11);
            doc.setTextColor(0); doc.setFont(undefined, 'normal');
            startY += 20; 
        } else { startY += 10; }
    }); 

    const globalBody = Object.values(globalProductSummary).sort((a, b) => a.name.localeCompare(b.name)).map(row => {
            let details = row.name;
            if(row.type === 'pack') details = `[PACK] ${row.name}`;
            if(row.color) details += `\n(${row.color})`;
            if(row.visual) details += `\nVisuel: ${row.visual}`;
            if(row.options && row.options.length) details += `\nOpt: ${row.options.join(', ')}`;
            if(row.specificity) details += `\nNote: ${row.specificity}`;
            const avgPrice = row.totalQty > 0 ? (row.totalAmount / row.totalQty) : 0;
            return [ details, row.totalQty, `${avgPrice.toFixed(2)} €`, `${row.totalAmount.toFixed(2)} €` ];
    });

    const sumTotalClub = Object.values(globalProductSummary).reduce((sum, r) => sum + r.totalAmount, 0);
    globalBody.push([ { content: "TOTAL GLOBAL CUMULÉ (TARIF CLUB)", styles: { fontStyle: 'bold', halign: 'right' } }, { content: "", styles: { fillColor: [240, 240, 240] } }, { content: "", styles: { fillColor: [240, 240, 240] } }, { content: `${sumTotalClub.toFixed(2)} €`, styles: { fontStyle: 'bold', fillColor: [200, 255, 200], textColor: [0, 100, 0] } } ]);

    doc.addPage();
    doc.setFontSize(16); doc.setTextColor(0, 50, 100); doc.text("RECAPITULATIF GLOBAL (Tarif CLUB)", margin, 20);
    doc.setFontSize(10); doc.setTextColor(100); doc.text("Ce tableau additionne les montants exacts facturés aux licenciés (Prix fixés par le club ou remisés).", margin, 27);
    doc.text("Il ne s'agit pas du tarif fournisseur, mais bien du chiffre d'affaires attendu auprès des membres.", margin, 32);

    doc.autoTable({
        startY: 40,
        head: [['Article / Détails', 'Qté Totale', 'P.U. Moyen (Licencié)', 'Total (Tarif Licencié)']],
        body: globalBody,
        theme: 'grid',
        headStyles: { fillColor: [0, 50, 100], textColor: 255, fontSize: 10 },
        styles: { fontSize: 9, cellPadding: 3, valign: 'middle' },
        columnStyles: { 0: { cellWidth: 'auto' }, 1: { cellWidth: 25, halign: 'center', fontStyle: 'bold' }, 2: { cellWidth: 35, halign: 'right' }, 3: { cellWidth: 35, halign: 'right', fontStyle: 'bold' } },
        alternateRowStyles: { fillColor: [245, 247, 250] }
    });

    const todayStr = new Date().toISOString().split('T')[0];
    doc.save(`DETAIL_FINANCIER_LICENCIES_${state.clubName.replace(/ /g, '_')}_${todayStr}.pdf`);
    showToast("Export détaillé par licencié généré avec succès !");
};
const showBlankOrderOptionsModal = () => {
    if (!state.clubName.trim()) {
        showToast("Veuillez d'abord sélectionner un club.", 'error');
        return;
    }
    if (!state.clubProductRange || state.clubProductRange.length === 0) {
        showToast(`Aucune gamme n'est définie pour le club ${state.clubName}.`, 'error');
        return;
    }

    // --- VARIABLES LOCALES ---
    let importedRestockData = {}; 
    let hasImportedFile = false;
    
    // On récupère l'état global défini sur la page principale
    const isReassortActive = state.isReassort;

    // --- INTERFACE ---
    const container = document.createElement('div');
    container.className = 'space-y-4';

    const renderContent = () => {
        const sortedRangeForFilter = [...state.clubProductRange].sort((a, b) => {
            const indexA = allAvailableProducts.findIndex(p => p.name === a.name);
            const indexB = allAvailableProducts.findIndex(p => p.name === b.name);
            return indexA - indexB;
        });

        const productListHtml = sortedRangeForFilter.map(item => {
            let defaultPrice = '';
            let defaultQty = '';
            let sourceInfo = ''; 
            let rowClass = 'bg-white'; 

            // Par défaut, tout est coché
            const isChecked = item.isIncludedInRestock !== false; 

            // Logique d'affichage des prix (Seulement utile en mode Réassort)
            if (isReassortActive) {
                if (importedRestockData[item.name]) {
                    const data = importedRestockData[item.name];
                    defaultPrice = (data.totalAmount / data.qty).toFixed(2);
                    defaultQty = data.qty;
                    sourceInfo = ' (Importé)';
                    rowClass = 'bg-green-50';
                }
                else if (item.lastRestockPrice !== undefined) {
                     defaultPrice = parseFloat(item.lastRestockPrice).toFixed(2);
                     defaultQty = item.lastRestockRefQty || '';
                } 
                if (!defaultPrice && item.licenseePrice) {
                    defaultPrice = parseFloat(item.licenseePrice).toFixed(2);
                }
            }

            // HTML de la ligne produit
            return `
            <div class="flex items-center justify-between py-2 border-b border-gray-100 ${rowClass} px-2 -mx-2 rounded">
                <div class="flex flex-col flex-grow overflow-hidden mr-2">
                    <div class="flex items-center">
                        <input type="checkbox" id="filter-prod-${sanitizeForId(item.name)}" value="${item.name}" class="product-filter-checkbox h-4 w-4 text-indigo-600 rounded border-gray-300" ${isChecked ? 'checked' : ''}>
                        <label for="filter-prod-${sanitizeForId(item.name)}" class="ml-2 block text-xs text-gray-700 truncate font-medium" title="${item.name}">
                            ${item.name}
                        </label>
                    </div>
                    ${sourceInfo ? `<span class="text-[9px] text-green-700 ml-6 italic font-bold">${sourceInfo}</span>` : ''}
                </div>
                
                <div class="manual-data-wrapper ${isReassortActive ? 'flex' : 'hidden'} flex-shrink-0 gap-1 items-center">
                    <div class="flex flex-col items-center w-16" title="Quantité de référence">
                        <input type="number" step="1" placeholder="Qté" value="${defaultQty}" data-product-name="${item.name}" class="manual-qty-input w-full text-xs border-gray-300 rounded-md py-1 px-1 text-center bg-white shadow-sm focus:border-indigo-500" ${isReassortActive ? '' : 'disabled'}>
                    </div>
                    <div class="flex items-center w-20 relative" title="Prix de référence">
                        <input type="number" step="0.01" placeholder="Prix" value="${defaultPrice}" data-product-name="${item.name}" class="manual-price-input w-full text-xs border-gray-300 rounded-md py-1 px-1 text-right bg-white shadow-sm font-bold text-indigo-900 pr-5 focus:border-indigo-500" ${isReassortActive ? '' : 'disabled'}>
                        <span class="text-[10px] text-gray-400 absolute right-1.5 pointer-events-none">€</span>
                    </div>
                </div>
            </div>
        `}).join('');

        const savedPrevRef = state.previousOrderReference || '';

        // --- CONSTRUCTION DU CONTENU SELON LE MODE ---
        let configBlock = '';
        
        if (isReassortActive) {
            // --- MODE RÉASSORT (Complexe) ---
            configBlock = `
                <div class="p-3 bg-blue-50 rounded-md border border-blue-200">
                    <div class="flex items-center mb-2">
                        <span class="text-lg mr-2">🔄</span>
                        <h4 class="font-bold text-blue-900 text-sm">Configuration Réassort 2 mois</h4>
                    </div>
                    <div class="pl-7 space-y-3">
                        <div>
                            <label for="blank-prev-order" class="block text-xs font-medium text-gray-600">1. N° Commande Précédente <span class="text-red-500">*</span></label>
                            <input type="text" id="blank-prev-order" value="${savedPrevRef}" placeholder="Ex: CDE-2023-045" class="mt-1 block w-full rounded-md border-blue-300 shadow-sm text-sm bg-white">
                        </div>
                        <div class="pt-2">
                            <label class="block text-xs font-medium text-gray-600 mb-1">2. Fichier JSON Dernière Commande <span class="text-red-500">*</span></label>
                            <div class="flex gap-2">
                                <label for="import-prev-json-input" class="cursor-pointer px-3 py-2 bg-blue-600 text-white text-xs font-bold rounded shadow hover:bg-blue-700 flex items-center gap-2">
                                    <span>📂 Choisir le fichier .json</span>
                                </label>
                                <input type="file" id="import-prev-json-input" class="hidden" accept=".json">
                                <span id="import-status-text" class="text-xs text-gray-500 flex items-center">
                                    ${hasImportedFile ? '✅ Fichier chargé' : 'Aucun fichier chargé'}
                                </span>
                            </div>
                        </div>
                    </div>
                </div>
            `;
        } else {
            // --- MODE STANDARD (Simple) ---
            configBlock = `
                <div class="p-3 bg-gray-50 rounded-md border border-gray-200">
                    <div class="flex items-center mb-2">
                        <span class="text-lg mr-2">📄</span>
                        <h4 class="font-bold text-gray-800 text-sm">Configuration du Bon de Commande Vierge</h4>
                    </div>
                    <div class="pl-7">
                        <label for="blank-order-qty-ref" class="block text-xs font-bold text-gray-700">Prix affichés sur le bon (Base Quantité)</label>
                        <select id="blank-order-qty-ref" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm text-sm">
                            <option value="0">-- Ne pas afficher de prix --</option>
                            <option value="1">Tarif Unitaire (1-4 pièces)</option>
                            <option value="5" selected>Tarif 5-14 pièces (Défaut)</option>
                            <option value="15">Tarif 15-24 pièces</option>
                            <option value="25">Tarif 25-49 pièces</option>
                            <option value="50">Tarif 50-79 pièces</option>
                            <option value="80">Tarif 80+ pièces</option>
                        </select>
                        <p class="text-[10px] text-gray-500 mt-1">Le PDF affichera les prix correspondants à cette tranche (sauf si un prix fixe club est défini).</p>
                    </div>
                </div>
            `;
        }

        container.innerHTML = `
            <div class="space-y-4 max-h-[70vh] overflow-y-auto pr-2">
                ${configBlock}

                <div class="p-3 bg-white rounded-md border border-gray-300">
                    <div class="flex justify-between items-center mb-2 border-b pb-1">
                        <h4 class="font-bold text-sm text-gray-800">Sélection des articles <span class="${isReassortActive ? '' : 'hidden'} text-xs text-indigo-600 ml-2">(Qté Ref / Prix Ref)</span></h4>
                        <div class="space-x-2">
                            <button type="button" id="btn-check-all" class="text-[10px] text-blue-600 underline">Tout cocher</button>
                            <button type="button" id="btn-uncheck-all" class="text-[10px] text-blue-600 underline">Tout décocher</button>
                        </div>
                    </div>
                    <div class="grid grid-cols-1 gap-0">
                        ${productListHtml}
                    </div>
                </div>
            </div>
        `;

        // --- EVENTS INTERNES ---
        container.querySelector('#btn-check-all').onclick = () => {
            container.querySelectorAll('.product-filter-checkbox').forEach(cb => cb.checked = true);
        };
        container.querySelector('#btn-uncheck-all').onclick = () => {
            container.querySelectorAll('.product-filter-checkbox').forEach(cb => cb.checked = false);
        };

        // GESTION IMPORT JSON (Uniquement en mode réassort)
        const fileInput = container.querySelector('#import-prev-json-input');
        if (fileInput) {
            fileInput.addEventListener('change', (e) => {
                const file = e.target.files[0];
                if (!file) return;

                const reader = new FileReader();
                reader.onload = (evt) => {
                    try {
                        let loaded = JSON.parse(evt.target.result);
                        const items = loaded.currentOrderLineItems || (loaded.currentState ? loaded.currentState.currentOrderLineItems : []);
                        
                        if (!items || items.length === 0) {
                            showToast("Le fichier ne contient pas d'articles.", "error");
                            return;
                        }

                        importedRestockData = {};
                        items.forEach(line => {
                            if (!importedRestockData[line.productName]) {
                                importedRestockData[line.productName] = { qty: 0, totalAmount: 0 };
                            }
                            importedRestockData[line.productName].qty += line.totalQuantity;
                            if (line.totalPriceTTC) {
                                importedRestockData[line.productName].totalAmount += line.totalPriceTTC;
                            }
                        });

                        hasImportedFile = true;
                        showToast("Fichier analysé avec succès !");
                        renderContent(); // Re-rendu pour afficher les prix importés

                    } catch (err) {
                        console.error(err);
                        showToast("Fichier JSON invalide.", "error");
                    }
                };
                reader.readAsText(file);
            });
        }
    };

    renderContent(); 

    // --- FONCTION DE SAUVEGARDE (Interne) ---
    const saveRestockData = () => {
        // En mode standard, on renvoie juste false
        if (!isReassortActive) return { isRestock: false, manualPrices: {} };

        const prevOrderInput = document.getElementById('blank-prev-order');
        if (prevOrderInput) state.previousOrderReference = prevOrderInput.value.trim();

        const manualPrices = {};
        const allCheckboxes = container.querySelectorAll('.product-filter-checkbox');
        
        allCheckboxes.forEach(checkbox => {
            const prodName = checkbox.value;
            const isChecked = checkbox.checked;
            const productInRange = state.clubProductRange.find(p => p.name === prodName);
            
            if (productInRange) {
                productInRange.isIncludedInRestock = isChecked;
                if (isChecked) {
                    const priceInput = container.querySelector(`.manual-price-input[data-product-name="${prodName}"]`);
                    const qtyInput = container.querySelector(`.manual-qty-input[data-product-name="${prodName}"]`);
                    if (priceInput && qtyInput) {
                        const valPrice = parseFloat(priceInput.value);
                        const valQty = parseInt(qtyInput.value) || 0;
                        if (!isNaN(valPrice) && valPrice > 0) {
                            manualPrices[prodName] = valPrice;
                            productInRange.lastRestockPrice = valPrice;
                            productInRange.lastRestockRefQty = valQty > 0 ? valQty : 99999;
                        }
                    }
                }
            }
        });
        
        // Sauvegarde dans le localStorage
        const rangeKey = `range_${state.clubName.replace(/[\s/\\?%*:|"<>]/g, '_')}`;
        localStorage.setItem(rangeKey, JSON.stringify(state.clubProductRange));
        
        return { isRestock: true, manualPrices };
    };

    // --- ACTIONS DU BAS DE MODALE ---
    const modalActions = [
        { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-400' },
        { 
            label: '📄 Générer le PDF', 
            className: 'bg-teal-600 text-white font-bold',
            onClick: () => {
                // Vérifications spécifiques au mode Réassort
                if (isReassortActive) {
                    const prevOrderVal = document.getElementById('blank-prev-order').value.trim();
                    if (!prevOrderVal) {
                        showToast("Le N° de la commande précédente est obligatoire.", "error");
                        return;
                    }
                }

                const { isRestock, manualPrices } = saveRestockData();
                const qtyRefElement = document.getElementById('blank-order-qty-ref');
                // En mode standard, on lit la valeur du select. En mode réassort, par défaut 5 (peu importe, car prix manuel)
                const qtyRef = qtyRefElement ? parseInt(qtyRefElement.value, 10) : 5; 
                
                const checkboxes = container.querySelectorAll('.product-filter-checkbox:checked');
                const selectedProductNames = Array.from(checkboxes).map(cb => cb.value);

                if (selectedProductNames.length === 0) {
                    alert("Veuillez sélectionner au moins un article.");
                    return;
                }

                hideModal(dom.mainModal);
                handleGenerateBlankOrderPdf(qtyRef, isRestock, state.previousOrderReference, selectedProductNames, manualPrices);
            } 
        }
    ];

    // Bouton Spécial (Uniquement si mode Réassort actif)
    if (isReassortActive) {
        modalActions.push({ 
            label: '💾 Mémoriser & Saisir Réassort', 
            className: 'bg-indigo-600 text-white font-bold border-l border-indigo-400',
            onClick: () => {
                const prevOrderVal = document.getElementById('blank-prev-order').value.trim();
                
                if (!prevOrderVal) {
                    showToast("Le N° de la commande précédente est obligatoire.", "error");
                    return;
                }
                if (!hasImportedFile) {
                    alert("Pour valider un réassort, vous DEVEZ importer le fichier JSON de la commande précédente.");
                    return;
                }

                saveRestockData(); 
                hideModal(dom.mainModal);
                
                if (confirm("Configuration enregistrée.\nVoulez-vous vider le panier actuel pour commencer la saisie ?")) {
                    state.currentOrderLineItems = [];
                    state.activeLicensee = '';
                    state.licencieName = '';
                    state.validatedLicensees = [];
                    state.isLoadedFromBackup = false; 
                    renderAll();
                    showToast("Panier vidé. Prêt pour la saisie.");
                } else {
                     showToast("Configuration mémorisée.");
                }
            } 
        });
    }

    showModal(dom.mainModal, "Générer Bon Vierge & Options", container, modalActions, 'max-w-4xl');
};    
const handleGenerateBlankOrderPdf = (referenceQuantity = 5, isRestock = false, prevOrderRef = '', filterList = null, manualPrices = {}) => {
    if (!state.clubName.trim() || !state.clubProductRange || state.clubProductRange.length === 0) return;

    const { jsPDF } = window.jspdf;
    const doc = new jsPDF();
    const pageHeight = doc.internal.pageSize.height;
    const pageWidth = doc.internal.pageSize.width;
    const margin = 14;
    const contentWidth = pageWidth - 2 * margin;
    let currentY = 15; 

    // --- HELPER ---
    const getRangeLabel = (qty) => {
        if (qty === 1) return "1-4 pièces";
        if (qty === 5) return "5-14 pièces";
        if (qty === 15) return "15-24 pièces";
        if (qty === 25) return "25-49 pièces";
        if (qty === 50) return "50-79 pièces";
        if (qty === 80) return "80+ pièces";
        return `${qty}+ pièces`;
    };
    const rangeLabel = getRangeLabel(referenceQuantity);

    // --- EN-TÊTE ---
    doc.setFontSize(18);
    doc.setFont(undefined, 'bold');
    
    const title = isRestock ? `PRÉVISIONNEL DE COMMANDE - RÉASSORT 2 MOIS` : `PRÉVISIONNEL DE COMMANDE - ${state.clubName}`;
    doc.text(title, margin, currentY);
    currentY += 8; 

    const showPrices = referenceQuantity !== 0;

    // --- BLOC D'INFORMATION (CALCUL HAUTEUR DYNAMIQUE) ---
    doc.setFontSize(9); 
    doc.setFont(undefined, 'normal');
    
    let infoBoxHeight = 30; 
    if (isRestock) infoBoxHeight = 44; 
    if (!showPrices && isRestock) infoBoxHeight = 30; 
    
    // CORRECTION : On agrandit la boîte si une remise est active pour faire de la place
    const hasDiscount = state.applyDiscount && state.clubDiscount > 0;
    if (showPrices && hasDiscount) {
        infoBoxHeight += 6; 
    }

    doc.setFillColor(248, 249, 250);
    doc.setDrawColor(200);
    doc.rect(margin, currentY, contentWidth, infoBoxHeight, 'F');
    doc.rect(margin, currentY, contentWidth, infoBoxHeight, 'S');

    let textY = currentY + 5; 
    const leftColX = margin + 4;
    
    // --- PARTIE 1 : INFOS PRIX ---
    if (showPrices) {
        // Ligne 1 : Titres
        doc.setFont(undefined, 'bold');
        doc.setTextColor(0);
        doc.text(" INFORMATIONS TARIF :", leftColX, textY);
        doc.setFont(undefined, 'normal');
        doc.text("Les prix indiqués sont TTC (Toutes Taxes Comprises).", leftColX + 50, textY);
        textY += 6; 

        // Ligne 2 : Base Tarifaire
        if (!isRestock) {
            const baseText = `Base tarifaire appliquée : Tranche ${rangeLabel}`;
            doc.text(baseText, leftColX, textY);
            const w = doc.getTextWidth(baseText);
            doc.setFont(undefined, 'italic'); doc.setFontSize(8);
            doc.text(" (sauf si un prix fixe a été défini par le club)", leftColX + w + 1, textY);
            doc.setFont(undefined, 'normal'); doc.setFontSize(9);
        } else {
            const baseText = `Base tarifaire appliquée : Tranche ${rangeLabel}`;
            doc.text(baseText, leftColX, textY);
            const widthOfBaseText = doc.getTextWidth(baseText);
            doc.setFont(undefined, 'italic');
            doc.setFontSize(8); 
            doc.text(" (tarif indicatif pour les articles non commandés sur dernière commande)", leftColX + widthOfBaseText + 2, textY);
            doc.setFontSize(9); 
            doc.setFont(undefined, 'normal');
        }
        
        // Ligne 3 (Optionnelle) : Remise Club (CORRIGÉ : SUR UNE NOUVELLE LIGNE)
        if (hasDiscount) {
            textY += 5; // On saute une ligne
            doc.setFont(undefined, 'bold');
            doc.setTextColor(22, 163, 74); // Vert
            let discountMsg = `Remise Club active : -${state.clubDiscount}%`;
            if (state.discountType === 'item') discountMsg += " (sur articles éligibles)";
            else discountMsg += " (sur toute la commande)";
            
            doc.text(discountMsg, leftColX, textY);
            doc.setTextColor(0); // Reset couleur
            doc.setFont(undefined, 'normal');
        }

        if (isRestock) {
            textY += 6; 
            doc.setDrawColor(220);
            doc.line(leftColX, textY - 3, pageWidth - margin - 4, textY - 3);
        }
        
        // On prépare le Y pour la suite (Conditions)
        textY += 6; 
    } 

    // --- PARTIE 2 : CONDITIONS ---
    // Si on n'affiche pas les prix et que c'est du réassort, on réinitialise le Y
    if (!showPrices && isRestock) textY = currentY + 6;

    doc.setTextColor(200, 0, 0); 
    doc.setFont(undefined, 'bold');

    if (isRestock) {
        let orderRefText = prevOrderRef ? `(Réf. Cde N°${prevOrderRef})` : '';
        doc.text(`CONDITIONS RÉASSORT 2 MOIS ${orderRefText} :`, leftColX, textY);
        textY += 5; 
        
        doc.setFont(undefined, 'normal');
        doc.text("• Minimum de la commande globale : 10 pièces.", leftColX, textY);
        textY += 4.5;
        doc.text("• Minimum par article : 2 pièces pour le maintien du tarif.", leftColX, textY);
        textY += 4.5;
        doc.setFont(undefined, 'italic');
        doc.text("(Si un article est commandé à l'unité, le tarif standard s'appliquera).", leftColX, textY);
    } else {
        doc.text(`CONDITIONS DE COMMANDE :`, leftColX, textY);
        textY += 5;
        doc.setFont(undefined, 'normal');
        doc.text("• Le minimum de la commande globale du club doit être de 10 pièces.", leftColX, textY);
        textY += 4.5;

        if (!showPrices) {
            doc.text("• Le tarif appliqué sera fonction du quantitatif commandé", leftColX, textY);
            textY += 4;
            doc.text("  par article d'une même gamme.", leftColX, textY);
        } else {
            doc.setFont(undefined, 'italic'); doc.setTextColor(80); doc.setFontSize(8);
            doc.text("(Ce bon est une pré-commande, la validation finale dépendra du volume global).", leftColX, textY);
            doc.setFontSize(9);
        }
    }
    
    doc.setTextColor(0); 
    doc.setFont(undefined, 'normal');

    // On sort de la boite en utilisant sa hauteur calculée
    currentY += infoBoxHeight + 6; 

    // --- ZONE DE SAISIE ---
    doc.setFontSize(11);
    doc.text("Nom / Prénom du Licencié :", margin, currentY);
    doc.setDrawColor(0);
    doc.setLineWidth(0.5);
    doc.line(margin + 55, currentY, contentWidth + margin, currentY); 
    
    currentY += 10; 

    // --- TABLEAU ---
    const head = [['Article & Détails Tarifaires', 'Tailles & Quantités']];
    const body = [];
    
    let rangeToProcess = state.clubProductRange;
    if (filterList && Array.isArray(filterList)) {
        rangeToProcess = rangeToProcess.filter(item => filterList.includes(item.name));
    }

    const sortedRange = rangeToProcess
        .map(rangeItem => ({ rangeData: rangeItem, productData: productMap.get(rangeItem.name) }))
        .filter(item => item.productData && item.productData.category !== 'option')
        .sort((a, b) => {
            const indexA = allAvailableProducts.findIndex(p => p.name === a.productData.name);
            const indexB = allAvailableProducts.findIndex(p => p.name === b.productData.name);
            return indexA - indexB; 
        });
        
    sortedRange.forEach(({ rangeData, productData }) => {
        let displayName = productData.name;
        
        if (showPrices) {
            let priceDisplay = "";
            let calculatedPrice = 0;
            let isManual = false;
            let isFixed = false;

            // 1. Priorité Prix Manuel (Réassort)
            if (manualPrices && manualPrices[productData.name] !== undefined) {
                calculatedPrice = manualPrices[productData.name];
                isManual = true;
                isFixed = true;
            }
            // 2. Priorité Prix Fixe Gamme
            else if (rangeData.licenseePrice !== null && rangeData.licenseePrice !== undefined && rangeData.licenseePrice >= 0) {
                calculatedPrice = rangeData.licenseePrice;
                isFixed = true;
            } 
            // 3. Calcul Prix Standard
            else {
                if (productData.pricingTiers) calculatedPrice = getPriceForQuantity(productData.pricingTiers, referenceQuantity);
                else if (productData.price) calculatedPrice = productData.price;

                if (calculatedPrice > 0) {
                    if (rangeData.defaultOptions) {
                        rangeData.defaultOptions.forEach(optName => {
                            const optProd = productMap.get(optName);
                            if (optProd) {
                                if (optProd.fixedPriceTTC) calculatedPrice += optProd.fixedPriceTTC;
                                else if (optProd.pricingTiers) calculatedPrice += getPriceForQuantity(optProd.pricingTiers, referenceQuantity);
                            }
                        });
                    }
                }
            }

            if (calculatedPrice > 0) {
                let label = "";
                let finalPrice = calculatedPrice;
                let originalPriceDisplay = "";

                // APPLICATION REMISE SUR LE BON
                let applyDiscountHere = false;
                if (!isFixed && state.applyDiscount && state.clubDiscount > 0) {
                    if (state.discountType === 'global') {
                        applyDiscountHere = true;
                    } else if (state.discountType === 'item' && rangeData.allowDiscount) {
                        applyDiscountHere = true;
                    }
                }

                if (applyDiscountHere) {
                    finalPrice = calculatedPrice * (1 - (state.clubDiscount / 100));
                    originalPriceDisplay = ` (au lieu de ${calculatedPrice.toFixed(2)}€)`;
                    label = `(Remise -${state.clubDiscount}%)`;
                } else {
                    if (isManual) {
                        label = "(prix dernière commande)";
                    } else if (rangeData.licenseePrice !== null) {
                        label = isRestock ? "(prix dernière commande)" : "(Prix fixé par le club)";
                    } else if (productData.subtype === 'ACCESSOIRES PERMANENTS') {
                        label = "(Tarif fixe)";
                    } else {
                        if (isRestock) {
                            label = "(prix dernière commande)";
                        } else {
                            label = `(Tarif ${getRangeLabel(referenceQuantity)})`;
                        }
                    }
                }
                
                priceDisplay = `${finalPrice.toFixed(2)} € ${label}${originalPriceDisplay}`;
                displayName += `\nPrix TTC : ${priceDisplay}`;
            } else {
                displayName += `\n Tarif : Selon quantité commandée`;
            }
        }

        if (rangeData.defaultOptions && rangeData.defaultOptions.length > 0) {
            const cleanOptions = rangeData.defaultOptions.map(opt => opt.replace('Ajustement Longueur ', ''));
            displayName += `\nOptions : ${cleanOptions.join(', ')}`;
        }
        if (rangeData.markingType) displayName += `\nFinition : ${rangeData.markingType}`;
        if (productData.colors && productData.colors.length > 0) displayName += `\nColoris : ${productData.colors.join(', ')}`;

        body.push([displayName, '']);
    });

    doc.autoTable({
        startY: currentY,
        head: head,
        body: body,
        theme: 'grid',
        headStyles: { fillColor: isRestock ? [220, 38, 38] : [22, 163, 74], valign: 'middle', fontSize: 9 },
        columnStyles: { 0: { cellWidth: 100, valign: 'top' }, 1: { cellWidth: contentWidth - 100 } },
        styles: { fontSize: 8, cellPadding: 2, overflow: 'linebreak' }, 
        
        didDrawCell: (data) => {
            if (data.column.index === 1 && data.row.section === 'body') {
                const fullText = data.row.raw[0]; const productName = fullText.split('\n')[0]; const product = productMap.get(productName);
                if (!product) return;
                const sizes = productSizes[product.sizeType || product.type] || []; if (sizes.length === 0) return; 
                
                const cell = data.cell; 
                const boxSize = 3.5; 
                const gridHeight = 9; 
                let startY = cell.y + (cell.height / 2) - (gridHeight / 2) + 2;
                if (cell.height < 15) startY = cell.y + 3;

                const startX = cell.x + 1; 
                const availableWidth = data.column.width - 2; 
                const numSizes = sizes.length || 1; const columnWidth = availableWidth / numSizes;
                
                sizes.forEach((size, index) => {
                    const currentColumnX = startX + (index * columnWidth); doc.setFontSize(5.5); doc.setTextColor(0);
                    const textWidth = doc.getTextWidth(size); const textX = currentColumnX + (columnWidth / 2) - (textWidth / 2);
                    doc.text(size, Math.max(currentColumnX, textX), startY); doc.setDrawColor(0); doc.setFillColor(255, 255, 255);
                    const boxX = currentColumnX + (columnWidth / 2) - (boxSize / 2); const boxY = startY + 1.5; doc.rect(boxX, boxY, boxSize, boxSize, 'S'); 
                });
            }
        },
        didParseCell: function (data) { if (data.column.index === 0 && data.row.section === 'body') { data.cell.styles.minCellHeight = 12; } }
    });

    currentY = doc.autoTable.previous.finalY + 10;

    // ▼▼▼ BLOC SIGNATURE ▼▼▼
    if (currentY + 40 > pageHeight) {
        doc.addPage();
        currentY = 20;
    }

    doc.setDrawColor(150);
    doc.setLineWidth(0.2);
    doc.rect(margin, currentY, contentWidth, 35);

    doc.setFontSize(10);
    doc.setTextColor(0);
    doc.setFont(undefined, 'bold');
    doc.text("RÉCAPITULATIF :", margin + 5, currentY + 8);
    
    doc.setFont(undefined, 'normal');
    doc.setFontSize(11);
    doc.text("Quantité totale : ............................", margin + 5, currentY + 22);

    const centerX = pageWidth / 2;
    doc.setDrawColor(200);
    doc.line(centerX, currentY, centerX, currentY + 35);

    const sigX = centerX + 5;
    doc.setFontSize(10);
    doc.setFont(undefined, 'bold');
    doc.text("VALIDATION :", sigX, currentY + 8);
    
    doc.setFontSize(9);
    doc.setFont(undefined, 'normal');
    doc.text("Date et Signature :", sigX, currentY + 18);
    
    let filenameSuffix = isRestock ? '_REASSORT_2_MOIS' : '';
    const filename = `Previsionnel_Vierge_${state.clubName.replace(/[\s/\\?%*:|"<>]/g, '_')}${filenameSuffix}.pdf`;
    doc.save(filename);
    showToast('Prévisionnel vierge généré !');
};const handleExportExcel = () => {
    if (state.currentOrderLineItems.length === 0 || typeof XLSX === 'undefined') return;

    const totals = calculateAllTotals();
    const totalArticles = state.currentOrderLineItems.reduce((acc, item) => acc + item.totalQuantity, 0);
    const docTitle = `${state.documentType === 'devis' ? 'Devis' : 'Bon de commande'} ${state.isReassort ? '(Réassort 2 mois)' : ''}`;
    
    const dataForSheet = [
        [docTitle], [], 
        ['Nom du Club:', state.clubName], ['Département:', state.departement], ['N° Client:', state.clientNumber], ['Date:', state.orderDate],
        ['Note Commande:', state.orderSpecificity], ['Total Articles:', totalArticles], []
    ];
    
    const excelHeader = ['Produit', 'Visuel', 'Licencié', 'Spécificité', 'Options', 'Tailles & Quantités', 'Qté Totale', 'Prix U. HT', 'Prix U. TTC', 'Total HT', 'Total TTC'];

    if (state.orderScope === 'licensee') {
        const groupedItems = state.currentOrderLineItems.reduce((acc, item) => {
            const key = item.licencieName || 'Commande Globale';
            if (!acc[key]) acc[key] = [];
            acc[key].push(item);
            return acc;
        }, {});
        const sortedLicensees = Object.keys(groupedItems).sort((a, b) => a.localeCompare(b));

        sortedLicensees.forEach(licensee => {
            dataForSheet.push([{ v: `Licencié: ${licensee}`, s: { font: { bold: true }, fill: { fgColor: { rgb: "E0E0E0" } } } }]);
            dataForSheet.push(excelHeader);
            let licenseeSubtotalTTC = 0;
            groupedItems[licensee].forEach(item => {
                let itemTotalTTC = item.totalPriceTTC;
                if (state.applyDiscount && (state.discountType === 'global' || (state.discountType === 'item' && item.applyDiscount))) {
                   itemTotalTTC *= (1 - (state.clubDiscount / 100));
                }
                licenseeSubtotalTTC += itemTotalTTC;
                let productName = item.productName;
                if (item.isFromStock) productName = `[STOCK] ${productName}`;
                if (item.isStockOrder) productName = `[POUR STOCK] ${productName}`;

                dataForSheet.push([
                    productName, item.visual || '', item.licencieName, item.specificity, item.options.join(', '),
                    getSortedSizesText(item).replace(/, /g, ' | '), item.totalQuantity,
                    { t: 'n', v: item.unitPriceHT, z: '#,##0.00 €' }, { t: 'n', v: item.unitPriceTTC, z: '#,##0.00 €' },
                    { t: 'n', v: item.totalPriceHT, z: '#,##0.00 €' }, { t: 'n', v: item.totalPriceTTC, z: '#,##0.00 €' }
                ]);
            });
            
            const deposit = state.licenseeDeposits[licensee] || 0;
            const remaining = licenseeSubtotalTTC - deposit;

            dataForSheet.push([]);
            dataForSheet.push(Array(excelHeader.length - 2).fill(null).concat([
                { v: "Total Articles Licencié:", s: { font: { bold: true } } }, 
                { t: 'n', v: licenseeSubtotalTTC, z: '#,##0.00 €', s: { font: { bold: true } } }
            ]));
            dataForSheet.push(Array(excelHeader.length - 2).fill(null).concat([
                { v: "Acompte Versé:", s: { font: { bold: true, color: { rgb: "008000" } } } }, 
                { t: 'n', v: deposit, z: '#,##0.00 €', s: { font: { bold: true, color: { rgb: "008000" } } } }
            ]));
            dataForSheet.push(Array(excelHeader.length - 2).fill(null).concat([
                { v: "Restant Dû:", s: { font: { bold: true, color: { rgb: "FF0000" } } } }, 
                { t: 'n', v: remaining, z: '#,##0.00 €', s: { font: { bold: true, color: { rgb: "FF0000" } } } }
            ]));
            dataForSheet.push([]);
        });
    } else {
        dataForSheet.push(excelHeader);
        state.currentOrderLineItems.forEach(item => {
            let productName = item.productName;
            if (item.isFromStock) productName = `[STOCK] ${productName}`;
            if (item.isStockOrder) productName = `[POUR STOCK] ${productName}`;
            dataForSheet.push([
                productName, item.visual || '', item.licencieName, item.specificity, item.options.join(', '),
                getSortedSizesText(item).replace(/, /g, ' | '), item.totalQuantity,
                { t: 'n', v: item.unitPriceHT, z: '#,##0.00 €' }, { t: 'n', v: item.unitPriceTTC, z: '#,##0.00 €' },
                { t: 'n', v: item.totalPriceHT, z: '#,##0.00 €' }, { t: 'n', v: item.totalPriceTTC, z: '#,##0.00 €' }
            ]);
        });
    }
    
    const totalsStartColumn = excelHeader.length - 2;
    dataForSheet.push([],
        Array(totalsStartColumn).fill(null).concat(['Sous-total HT:', { t: 'n', v: totals.subtotalHT, z: '#,##0.00 €' }]),
        Array(totalsStartColumn).fill(null).concat(['Sous-total TTC:', { t: 'n', v: totals.subtotalTTC, z: '#,##0.00 €' }]),
        Array(totalsStartColumn).fill(null).concat([`Remise Club (${state.clubDiscount}%) TTC (Info):`, { t: 'n', v: -totals.discountAmountTTC, z: '#,##0.00 €' }]),
        Array(totalsStartColumn).fill(null).concat(['Frais de port TTC:', { t: 'n', v: totals.shippingTTC, z: '#,##0.00 €' }]),
        ...(state.isCustomCreation ? [Array(totalsStartColumn).fill(null).concat(['Forfait Création Graphique TTC:', { t: 'n', v: totals.graphicFeeTTC, z: '#,##0.00 €' }])] : []),
        Array(totalsStartColumn).fill(null).concat([{v: "Total Général TTC:", s:{font:{bold:true}}}, { t: 'n', v: totals.grandTotalTTC, z: '#,##0.00 €', s:{font:{bold:true}}}])
    );
    if(state.documentType === 'commande') {
        dataForSheet.push(Array(totalsStartColumn).fill(null).concat([{v: "Acompte à verser (30%):", s:{font:{bold:true}}}, { t: 'n', v: totals.downPayment, z: '#,##0.00 €', s:{font:{bold:true}}}]));
    }
    
    const wb = XLSX.utils.book_new();
    const ws = XLSX.utils.aoa_to_sheet(dataForSheet);
    ws['!cols'] = excelHeader.map(h => ({wch: h.length > 20 ? 50 : 20}));
    XLSX.utils.book_append_sheet(wb, ws, 'Commande');
    XLSX.writeFile(wb, `detail-commande_${state.clubName.replace(/ /g, '_') || 'commande'}_${state.orderDate}.xlsx`);
};
// ▼▼▼ REMPLACEZ L'ANCIENNE FONCTION PAR CELLE-CI ▼▼▼
// ▼▼▼ REMPLACEZ L'ANCIENNE FONCTION PAR CELLE-CI ▼▼▼
const handleExportDistributionList = () => {
    if (state.orderScope !== 'licensee') {
        showToast("La liste de distribution n'est disponible que pour les commandes par licencié.", 'error');
        return;
    }

    const allPhysicalItems = unrollPacksForDistribution(state.currentOrderLineItems);

    const licenseesWithItems = allPhysicalItems.filter(item => item.licencieName && item.licencieName !== 'Commande Globale');
    if (licenseesWithItems.length === 0) {
        showToast("Aucun article n'a été assigné à un licencié.", 'error');
        return;
    }

    const { jsPDF } = window.jspdf;
    const doc = new jsPDF();

    doc.setFontSize(20);
    doc.setFont(undefined, 'bold');

    // --- TITRE RÉASSORT ---
    let title = `Liste de Distribution - ${state.clubName}`;
    if (state.isReassort) {
        title += " (RÉASSORT)";
        doc.setTextColor(200, 0, 0);
    }
    doc.text(title, 14, 22);
    doc.setTextColor(0);
    // ----------------------

    doc.setFontSize(12);
    doc.setFont(undefined, 'normal');
    doc.text(`Date d'export: ${new Date().toLocaleDateString('fr-FR')}`, 14, 30);

    const groupedItems = licenseesWithItems.reduce((acc, item) => {
        const key = item.licencieName;
        if (!acc[key]) acc[key] = [];
        acc[key].push(item);
        return acc;
    }, {});

    const sortedLicensees = Object.keys(groupedItems).sort((a, b) => a.localeCompare(b));
    let startY = 40;

    sortedLicensees.forEach(licensee => {
        const body = groupedItems[licensee].map(item => {
            let productNameWithOptions = item.productName;
            if (item.visual) {
                productNameWithOptions += `\nVisuel: ${item.visual}`;
            }
            if (item.options && item.options.length > 0) {
                productNameWithOptions += `\nOptions: ${item.options.join(', ')}`;
            }
            if (item.specificity) {
                productNameWithOptions += `\n(${item.specificity})`;
            }
            
            return ['[  ]', productNameWithOptions, item.totalQuantity, getSortedSizesText(item)];
        });
        
        const tableHeight = (body.length + 1) * 10 + 10;
        if (startY + tableHeight > doc.internal.pageSize.height - 20) {
            doc.addPage();
            startY = 20;
        }

        doc.setFontSize(16);
        doc.setFont(undefined, 'bold');
        doc.text(licensee, 14, startY);

        doc.autoTable({
            startY: startY + 5,
            head: [['Remis', 'Article & Détails', 'Qté', 'Taille']],
            body: body,
            theme: 'grid',
            headStyles: { fillColor: [79, 70, 229] },
            styles: { fontSize: 9, cellPadding: 2, valign: 'middle' },
            columnStyles: { 0: { cellWidth: 10 }, 1: { cellWidth: 95 } },
            // ▼▼▼ NOUVELLE LOGIQUE DE COULEUR ▼▼▼
            didParseCell: function (data) {
                if (data.section === 'body' && data.column.index === 1) { // Colonne "Article & Détails"
                    const text = data.cell.raw;
                    if (text && typeof text === 'string') {
                        // Si personnalisé, on met en rouge pour attirer l'attention lors de la distribution
                        if (text.includes('Visuel:') || text.includes('Options:') || text.includes('Note:')) {
                            data.cell.styles.textColor = [220, 20, 60]; // Rouge
                        }
                    }
                }
            }
            // ▲▲▲ FIN NOUVELLE LOGIQUE ▲▲▲
        });

        startY = doc.autoTable.previous.finalY + 15;
    });
    
    const todayStr = new Date().toISOString().split('T')[0];
    doc.save(`DISTRIBUTION_${state.clubName.replace(/ /g, '_')}_${todayStr}.pdf`);
    showToast('PDF de distribution généré avec succès !');
};const handleExportLabels = () => {
    // Vérifications d'usage
    if (state.orderScope !== 'licensee') {
        showToast("Les étiquettes ne sont disponibles qu'en mode 'Par licencié'.", 'error');
        return;
    }
    if (state.currentOrderLineItems.length === 0) {
        showToast("La commande est vide.", 'error');
        return;
    }

    // 1. Préparation des données (Regroupement par licencié)
    // On utilise la fonction 'unroll' pour avoir le détail des packs si besoin
    const allItems = unrollPacksForDistribution(state.currentOrderLineItems);
    
    const groupedItems = allItems.reduce((acc, item) => {
        const name = item.licencieName;
        if (name && name !== 'Commande Globale' && name !== 'Stock Club') {
            if (!acc[name]) acc[name] = [];
            acc[name].push(item);
        }
        return acc;
    }, {});

    const sortedLicensees = Object.keys(groupedItems).sort((a, b) => a.localeCompare(b));

    if (sortedLicensees.length === 0) {
        showToast("Aucun licencié trouvé.", 'error');
        return;
    }

    // 2. Configuration PDF
    const { jsPDF } = window.jspdf;
    const doc = new jsPDF(); // A4 Portrait par défaut
    
    // Paramètres de la grille (8 étiquettes par page : 2 colonnes x 4 lignes)
    const marginX = 10;
    const marginY = 10;
    const cardWidth = 90;  // Largeur étiquette
    const cardHeight = 65; // Hauteur étiquette
    const gapX = 10;       // Espace horizontal entre les 2 col
    const gapY = 5;        // Espace vertical entre les lignes

    let count = 0;

    sortedLicensees.forEach((licensee) => {
        // Gestion Nouvelle Page tous les 8 items
        if (count > 0 && count % 8 === 0) {
            doc.addPage();
        }

        // Calcul position X, Y
        // Index sur la page actuelle (0 à 7)
        const indexOnPage = count % 8;
        const col = indexOnPage % 2; // 0 ou 1
        const row = Math.floor(indexOnPage / 2); // 0, 1, 2, 3

        const x = marginX + (col * (cardWidth + gapX));
        const y = marginY + (row * (cardHeight + gapY));

        // --- DESSIN DE L'ÉTIQUETTE ---
        
        // Cadre pointillé pour découpe facile
        doc.setDrawColor(150);
        doc.setLineDashPattern([3, 3], 0); // Pointillés
        doc.rect(x, y, cardWidth, cardHeight);
        doc.setLineDashPattern([], 0); // Reset ligne continue

        // En-tête : Nom du Licencié
        doc.setFillColor(245, 245, 245); // Fond gris clair titre
        doc.rect(x + 1, y + 1, cardWidth - 2, 12, 'F'); // Bandeau titre
        
        doc.setTextColor(0);
        doc.setFontSize(11);
        doc.setFont(undefined, 'bold');
        // Tronquer le nom s'il est trop long
        let displayName = licensee;
        if (displayName.length > 25) displayName = displayName.substring(0, 22) + '...';
        doc.text(displayName, x + cardWidth / 2, y + 9, { align: 'center' });

      // Corps : Liste des articles
        let currentTextY = y + 18;
        const items = groupedItems[licensee];
        
        // MODIF 1 : Taille 7.5 (très lisible, mais permet d'écrire plus long)
        doc.setFontSize(7.5); 
        doc.setFont(undefined, 'normal');

        // Regrouper les articles
        const consolidatedLabelItems = {};
        items.forEach(item => {
            const key = `${item.productName} [${getSortedSizesText(item)}]`;
            consolidatedLabelItems[key] = (consolidatedLabelItems[key] || 0) + item.totalQuantity;
        });

     // Affichage dynamique
        let lineCount = 0;
        const maxLines = 12; 

        for (const [desc, qty] of Object.entries(consolidatedLabelItems)) {
            if (lineCount >= maxLines) {
                doc.setFontSize(6); 
                doc.setTextColor(100);
                doc.text(`... et suite (voir bon)`, x + 5, currentTextY + 1);
                break;
            }

            // Nettoyage du nom
            let rawName = desc.split('[')[0]; 
            rawName = rawName.replace(/\[.*?\]/g, '').trim();
            
            // Récupération de la taille brute
            const sizeInfo = desc.match(/\[(.*?)\]/); 
            let sizeStr = sizeInfo ? sizeInfo[1] : '';
            
            // --- CORRECTION ICI : J'ai supprimé la ligne qui enlevait le dernier chiffre ---
            // On garde sizeStr tel quel pour avoir "S: 2, M: 4" complet.

            // DÉTECTION : Faut-il passer sur 2 lignes ?
            const isComplexSize = sizeStr.includes('Haut') || sizeStr.includes('/') || sizeStr.includes(',') || (rawName.length + sizeStr.length > 45);

            if (isComplexSize) {
                // --- MODE 2 LIGNES ---
                
                // Ligne 1 : Nom
                let nameLine = `${qty}x ${rawName.substring(0, 45)}${rawName.length > 45 ? '.' : ''}`;
                doc.setFont(undefined, 'bold'); 
                doc.setTextColor(0);
                doc.text(nameLine, x + 5, currentTextY);
                
                currentTextY += 3.5;

                // Ligne 2 : Détail complet
                doc.setFont(undefined, 'normal'); 
                doc.setTextColor(80); // Gris foncé
                doc.setFontSize(7);   
                doc.text(` ${sizeStr}`, x + 10, currentTextY); 

                currentTextY += 4.5; 
                lineCount += 2; 
                
                doc.setFontSize(7.5);
                doc.setTextColor(0);

            } else {
                // --- MODE 1 LIGNE ---
                
                // Si c'est court, on peut nettoyer le ": 1" pour faire joli, mais SEULEMENT si c'est une quantité simple
                let displaySize = sizeStr;
                if (!sizeStr.includes(',')) {
                     displaySize = displaySize.replace(/: \d+$/, ''); // On enlève ": 1" seulement si c'est une taille unique simple
                }

                let cleanName = rawName.length > 35 ? rawName.substring(0, 35) + '.' : rawName;
                const lineText = `${qty}x ${cleanName} (${displaySize})`;
                
                doc.setFont(undefined, 'normal');
                doc.setTextColor(0);
                doc.text(lineText, x + 5, currentTextY);
                
                currentTextY += 4;
                lineCount++;
            }
        }

        // Pied de l'étiquette
        doc.setFontSize(8);
        doc.setTextColor(150); 
        doc.setDrawColor(150); 

        const checkboxY = y + cardHeight - 7;

        // Case 1 : Vérifié
        doc.rect(x + 53, checkboxY, 3, 3); 
        doc.text("Vérifié", x + 58, checkboxY + 2.5);

        // Case 2 : Remis
        doc.rect(x + 72, checkboxY, 3, 3); 
        doc.text("Remis", x + 77, checkboxY + 2.5);

        count++;
    });

    const todayStr = new Date().toISOString().split('T')[0];
    doc.save(`ETIQUETTES_SACS_${state.clubName.replace(/ /g, '_')}_${todayStr}.pdf`);
    showToast("Planche d'étiquettes générée !");
};
const handleExportDistributionExcel = () => {
    // Vérifications de base
    if (state.orderScope !== 'licensee') {
        showToast("L'export distribution n'est disponible qu'en mode 'Par licencié'.", 'error');
        return;
    }
    if (state.currentOrderLineItems.length === 0) {
        showToast("La commande est vide.", 'error');
        return;
    }

    if (typeof XLSX === 'undefined') {
        showToast("Librairie Excel non chargée.", "error");
        return;
    }

    // 1. Préparation des données
    // On déroule les packs pour avoir la liste exacte des articles physiques à remettre
    const allItems = unrollPacksForDistribution(state.currentOrderLineItems);

    // On ne garde que les items liés à des licenciés (pas le stock global)
    const validItems = allItems.filter(item => item.licencieName && item.licencieName !== 'Commande Globale' && item.licencieName !== 'Stock Club');

    // Tri par Nom de Licencié
    validItems.sort((a, b) => a.licencieName.localeCompare(b.licencieName));

    // 2. Construction du tableau pour Excel
    // En-têtes
    const data = [
        ["LISTE DE DISTRIBUTION - " + state.clubName],
        ["Date export : " + new Date().toLocaleDateString()],
        [],
        ["Nom du Licencié", "Qté", "Article", "Taille", "Détails (Options / Visuel)", "Vérifié", "Remis"]
    ];

    let currentLicensee = "";

    validItems.forEach(item => {
        // Petits calculs de texte pour les détails
        let detailsParts = [];
        if (item.visual) detailsParts.push(`Visuel: ${item.visual}`);
        if (item.options && item.options.length > 0) detailsParts.push(`Opt: ${item.options.join(', ')}`);
        if (item.specificity) detailsParts.push(`Note: ${item.specificity}`);
        const detailsStr = detailsParts.join(' | ');

        // Gestion visuelle : Si c'est le même licencié, on ne répète pas son nom (optionnel, mais plus propre)
        // Ici je choisis de répéter le nom pour permettre le tri/filtrage dans Excel, c'est souvent plus pratique.
        const licenseeName = item.licencieName; 

        data.push([
            licenseeName,
            item.totalQuantity,
            item.productName,
            getSortedSizesText(item),
            detailsStr,
            "", // Colonne vide pour "Vérifié" (cocher manuellement sur papier ou "x" sur PC)
            ""  // Colonne vide pour "Remis"
        ]);
    });

    // 3. Génération du fichier
    const wb = XLSX.utils.book_new();
    const ws = XLSX.utils.aoa_to_sheet(data);

    // Ajustement des largeurs de colonnes
    ws['!cols'] = [
        { wch: 25 }, // Licencié
        { wch: 5 },  // Qté
        { wch: 35 }, // Article
        { wch: 15 }, // Taille
        { wch: 40 }, // Détails
        { wch: 10 }, // Vérifié
        { wch: 10 }  // Remis
    ];

    XLSX.utils.book_append_sheet(wb, ws, "Distribution");
    
    const todayStr = new Date().toISOString().split('T')[0];
    XLSX.writeFile(wb, `DISTRIBUTION_EXCEL_${state.clubName.replace(/ /g, '_')}_${todayStr}.xlsx`);
    
    showToast("Fichier Excel de distribution généré !");
};
const mergeLineItems = () => {
    const consolidated = {};

    state.currentOrderLineItems.forEach(item => {
        // --- START: Added Check for Pack Type ---
        if (item.type === 'pack') {
             // If it's a pack, just add it directly without trying to merge sizes
             // Use a unique key like the pack's ID to prevent accidental merging
            consolidated[`pack_${item.id}`] = { // Ensure a unique key
                 ...item,
                 licencieName: 'Commande Globale' // Set licensee name consistently
            };
            return; // Skip the rest of the logic for packs
        }
        // --- END: Added Check for Pack Type ---

        // ▼▼▼ Existing logic for non-pack items (color added to key) ▼▼▼
        const key = `${item.productName}|${item.color || ''}|${item.visual || ''}|${JSON.stringify((item.options || []).sort())}|${item.specificity || ''}|${item.isManualPrice}|${item.isStockOrder}|${item.isFromStock}`; // Added check for item.options

        if (!consolidated[key]) {
            consolidated[key] = {
                ...item,
                licencieName: 'Commande Globale',
                quantitiesPerSize: {}, // Initialize quantitiesPerSize here
                totalQuantity: 0,
            };
        }

        // --- START: Added Check for quantitiesPerSize existence ---
        // Ensure quantitiesPerSize exists on the item before iterating
        if (item.quantitiesPerSize && typeof item.quantitiesPerSize === 'object') {
             for (const size in item.quantitiesPerSize) {
                 const qty = item.quantitiesPerSize[size] || 0;
                  // Ensure quantitiesPerSize exists on the consolidated item
                 if (!consolidated[key].quantitiesPerSize) {
                      consolidated[key].quantitiesPerSize = {};
                 }
                 consolidated[key].quantitiesPerSize[size] = (consolidated[key].quantitiesPerSize[size] || 0) + qty;
             }
        }
         // --- END: Added Check for quantitiesPerSize existence ---
    });

    // Final map to recalculate totalQuantity, handles both packs and merged items
    return Object.values(consolidated).map(item => {
        // --- START: Recalculate totalQuantity safely ---
        if (item.type !== 'pack' && item.quantitiesPerSize && typeof item.quantitiesPerSize === 'object') {
             item.totalQuantity = Object.values(item.quantitiesPerSize).reduce((sum, q) => sum + (q || 0), 0);
        } else if (item.type === 'pack') {
             // Packs should already have totalQuantity = 1 usually, but recalculate just in case
             // For packs, totalQuantity is the number of packs, not items inside
             // If you intended totalQuantity to be items inside, adjust logic here
             item.totalQuantity = item.totalQuantity || 1; // Default to 1 if missing
        } else {
             // Handle potential edge cases where item is not pack but lacks quantitiesPerSize
             item.totalQuantity = item.totalQuantity || 0;
        }
        // --- END: Recalculate totalQuantity safely ---
        return item;
    });
};
const updateButtonStates = () => {
    const addProductBtn = document.getElementById('add-product-btn');
    if (addProductBtn) {
        const productSelected = !!state.currentProduct;
        let quantityEntered = false;
        const productData = productMap.get(state.currentProduct);
        
        if (productData) {
            // Vérification classique des quantités
            if (state.documentType === 'devis' || (productData.type === 'accessoire' && (!productData.sizeType || productData.sizeType === 'unique'))) {
                quantityEntered = (parseInt(state.currentAccessoryQuantity, 10) || 0) > 0;
            } else {
                quantityEntered = Object.values(state.currentQuantities).some(q => (parseInt(q, 10) || 0) > 0);
            }

            // --- CORRECTION ICI ---
            // Avant : on exigeait un prix manuel (parseFloat(state.manualUnitPrice) > 0)
            // Maintenant : on accepte soit un prix manuel, SOIT un prix calculé (standard)
            if (state.isReassort && productData.type !== 'accessoire') {
                const hasManualPrice = parseFloat(state.manualUnitPrice) > 0;
                const hasCalculatedPrice = state.currentCalculatedUnitPrice > 0;
                
                // Le bouton s'active si l'une des deux conditions de prix est remplie
                quantityEntered = quantityEntered && (hasManualPrice || hasCalculatedPrice);
            }
        }
        
        addProductBtn.disabled = !(productSelected && quantityEntered);
    }

    // Gestion du bouton Valider la Commande (Reste inchangé)
    const isReadyForValidation = state.clubName.trim() && (state.documentType === 'devis' || state.departement.trim());
    const validateBtn = document.getElementById('validate-order-btn');
    if (validateBtn) {
        validateBtn.disabled = !(isReadyForValidation && state.currentOrderLineItems.length > 0);
    }

    // Gestion bouton lien portail (si présent)
    const portalBtn = document.getElementById('generate-portal-link-btn');
    if (portalBtn) {
        const selectionMade = state.portalProductSelection && state.portalProductSelection.length > 0;
        const clubNameEntered = !!state.clubName.trim();
        portalBtn.disabled = !(selectionMade && clubNameEntered);
    }
};// --- LOGIQUE RECHERCHE INTELLIGENTE ---
document.body.addEventListener('input', (e) => {
    if (e.target.id === 'smart-product-search') {
        const searchTerm = e.target.value.toLowerCase().trim();
        const resultsContainer = document.getElementById('smart-search-results');
        
        if (searchTerm.length < 2) {
            resultsContainer.classList.add('hidden');
            resultsContainer.innerHTML = '';
            return;
        }

        let sourceProducts = allAvailableProducts;
        // Filtre par gamme club si activé
        if (state.clubProductRange.length > 0 && !state.showAllProducts) {
             sourceProducts = allAvailableProducts.filter(p => 
                state.clubProductRange.some(rangeItem => rangeItem.name === p.name)
             );
        }

        // Filtrage
        const results = sourceProducts.filter(p => 
            p.name.toLowerCase().includes(searchTerm) || 
            p.category.toLowerCase().includes(searchTerm) ||
            p.subtype.toLowerCase().includes(searchTerm)
        );

        if (results.length > 0) {
            resultsContainer.innerHTML = results.map(p => {
                // Mise en évidence du terme recherché
                const regex = new RegExp(`(${searchTerm})`, 'gi');
                const highlightedName = p.name.replace(regex, '<span class="search-highlight">$1</span>');
                const isRange = state.clubProductRange.some(r => r.name === p.name);
                const rangeBadge = isRange ? '<span class="ml-2 text-[10px] bg-green-100 text-green-800 px-1 rounded border border-green-200">Gamme Club</span>' : '';
                
                return `<div class="search-result-item" data-product-name="${p.name}">
                            <div class="text-sm font-medium text-gray-900 flex items-center">${highlightedName} ${rangeBadge}</div>
                            <div class="text-xs text-gray-500">${p.category} > ${p.subtype}</div>
                        </div>`;
            }).join('');
            resultsContainer.classList.remove('hidden');
        } else {
            resultsContainer.innerHTML = `<div class="p-3 text-sm text-gray-500 text-center">Aucun article trouvé pour "${searchTerm}"</div>`;
            resultsContainer.classList.remove('hidden');
        }
    }
});

// Gestion du clic sur un résultat de recherche (MISE A JOUR)
document.body.addEventListener('click', (e) => {
    const resultItem = e.target.closest('.search-result-item');
    if (resultItem) {
        const productName = resultItem.dataset.productName;
        resetProductForm();
        state.currentProduct = productName;
        calculateCurrentFormPrice();
        renderProductForm(); 
        
        // Focus automatique sur la première taille
        setTimeout(() => {
             const firstSize = document.querySelector('.size-input');
             if(firstSize) firstSize.focus();
             // SYNC VISUELLE : On force la liste déroulante sur le bon produit si on réaffiche
             const select = document.getElementById('current-product-select');
             if (select) select.value = productName; 
        }, 100);
    }    
    // Fermer la recherche si clic ailleurs
    if (!e.target.closest('#smart-product-search') && !e.target.closest('#smart-search-results')) {
        const container = document.getElementById('smart-search-results');
        if(container) container.classList.add('hidden');
    }

    // Bouton reset recherche
    if (e.target.closest('#clear-product-search') || e.target.id === 'reset-product-form-btn-small') {
        resetProductForm();
    }
    
    // Bouton Snapshot
    if (e.target.closest('#global-snapshot-btn')) {
        handleGlobalSnapshot();
// REMPLACEZ "const handleGlobalSnapshot = () => {" PAR CECI :
function handleGlobalSnapshot() {
    if (!state.clubName.trim()) {
        showToast("Veuillez définir un nom de club avant de sauvegarder.", "error");
        return;
    }

    // On construit un méga-objet contenant TOUT
    const snapshot = {
        appVersion: "1.0",
        timestamp: new Date().toISOString(),
        currentState: state,
        // On sauvegarde aussi les données "persistantes" liées au club
        clubData: {
            stock: state.clubStock,
            packs: state.clubPacks,
            range: state.clubProductRange,
            visuals: state.clubVisuals,
            deposits: state.licenseeDeposits,
            clientInfo: clientDatabase.find(c => c.clubName === state.clubName)
        }
    };

    try {
        const dataStr = JSON.stringify(snapshot, null, 2);
        const blob = new Blob([dataStr], { type: "application/json" });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        const safeDate = new Date().toISOString().replace(/[:.]/g, '-');
        a.download = `FULL_BACKUP_${state.clubName.replace(/ /g, '_')}_${safeDate}.json`;
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        URL.revokeObjectURL(url);
        showToast("✅ Sauvegarde Complète (Système) effectuée !");
    } catch (error) {
        console.error("Snapshot error", error);
        showToast("Erreur lors de la création de la sauvegarde.", "error");
    }
};    }
});
// =================================================================================
// --- INITIALIZATION ---
// =================================================================================

const initializeAppView = () => {
    // 1. Restauration du Catalogue Custom (Optionnel, si vous l'utilisez encore)
    const savedCatalog = localStorage.getItem('customCatalog');
    if (savedCatalog) {
        try {
            allAvailableProducts = JSON.parse(savedCatalog);
            refreshProductMap();
        } catch (e) { console.error("Erreur chargement catalogue", e); }
    }

    // 2. Initialisation de la date
    dom.orderDate.value = new Date().toISOString().split('T')[0];
    
    // 3. Restauration Clients (Base de données locale des noms de clubs)
    const savedClients = localStorage.getItem('clientDatabase');
    if (savedClients) {
        try { clientDatabase = JSON.parse(savedClients); } catch (e) {}
    }
    updateDatalists();

    // 4. Lancement
    renderAll();
    
    // NOTE : On ne lance plus startAutoSaveInterval() ici.
    // La sauvegarde automatique ne doit se lancer qu'APRES la connexion au club (dans startAppLogin).
};
// Fonction helper pour sortir le setInterval proprement (à ajouter juste avant ou après initializeAppView)
const startAutoSaveInterval = () => {
    setInterval(async () => {
        // Si un club est connecté et qu'il y a des données ou un nom de club
        if (currentClubCode && (state.currentOrderLineItems.length > 0 || state.clubName.trim() !== '')) {
            dom.autosaveStatus.textContent = '☁️ Synchro Cloud...';
            dom.autosaveStatus.className = 'mt-1 text-xs text-blue-600 animate-pulse';
            
            try {
                // Sauvegarde locale par sécurité
                localStorage.setItem('autosavedOrder', JSON.stringify(state));
                
                // Sauvegarde Cloud
                await set(ref(db, 'clubs/' + currentClubCode), state);
                
                setTimeout(() => {
                    const now = new Date();
                    dom.autosaveStatus.textContent = `☁️ Cloud à jour : ${now.toLocaleTimeString('fr-FR')}`;
                    dom.autosaveStatus.className = 'mt-1 text-xs text-gray-400';
                }, 1000);
            } catch (e) {
                dom.autosaveStatus.textContent = '⚠️ Erreur Synchro';
                dom.autosaveStatus.className = 'mt-1 text-xs text-red-600';
            }
        }
    }, 30000); // Toutes les 30 secondes
};// ▼▼▼ COLLEZ CE BLOC DANS VOTRE SCRIPT (Avant l'initialisation) ▼▼▼
const showTreasuryModal = () => {
    if (state.orderScope !== 'licensee') {
        showToast("Le suivi de trésorerie n'est pertinent que pour les commandes par licencié.", 'error');
        return;
    }

    if (state.currentOrderLineItems.length === 0) {
        showToast("Aucune commande à analyser.", "error");
        return;
    }

    // 1. Calcul des données par licencié
    const treasuryData = {};
    const allLicensees = new Set([...state.licenseeList]); 

    // On ajoute aussi ceux qui ont des articles mais ne sont pas dans la liste
    state.currentOrderLineItems.forEach(item => {
        if (item.licencieName && item.licencieName !== 'Commande Globale' && item.licencieName !== 'Stock Club') {
            allLicensees.add(item.licencieName);
        }
    });

    // Calcul des totaux
    allLicensees.forEach(name => {
        const items = state.currentOrderLineItems.filter(i => i.licencieName === name);
        
        let totalOrder = 0;
        items.forEach(item => {
            // Logique de prix : si prix fixe (Gamme) ou prix calculé
            let itemPrice = item.totalPriceTTC;
            
            // On vérifie si c'est un prix fixe "Gamme Club"
            const rangeItem = state.clubProductRange.find(r => r.name === item.productName);
            const hasFixedPrice = !item.isStockOrder && !item.isFromStock && rangeItem && typeof rangeItem.licenseePrice === 'number' && rangeItem.licenseePrice >= 0;
            
            if (hasFixedPrice && item.type !== 'pack') {
                itemPrice = rangeItem.licenseePrice * item.totalQuantity;
            } else if (item.appliedDiscountRate > 0) {
                // Si remise %, on s'assure qu'elle est déduite
                itemPrice = item.totalPriceTTC * (1 - (item.appliedDiscountRate / 100));
            }
            
            totalOrder += itemPrice;
        });

        // Si le licencié n'a rien commandé, on l'ignore (sauf s'il a versé un acompte)
        if (totalOrder === 0 && !state.licenseeDeposits[name]) return;

        const participation = state.applyClubParticipation ? (state.clubParticipationAmount || 0) : 0;
        const deposit = state.licenseeDeposits[name] || 0;
        
        let toPay = totalOrder - participation;
        if (toPay < 0) toPay = 0;
        
        let remaining = toPay - deposit;

        // ▼▼▼ CORRECTIF ANTI -0.00€ ▼▼▼
        // Si le montant est infime (positif ou négatif), on le force à 0 pur.
        if (Math.abs(remaining) < 0.005) {
            remaining = 0;
        }

        treasuryData[name] = {
            totalOrder: totalOrder,
            participation: participation,
            deposit: deposit,
            remaining: remaining
        };
    });

    // 2. Construction du Tableau HTML
    const sortedNames = Object.keys(treasuryData).sort((a, b) => a.localeCompare(b));
    
    let totalGlobalOrder = 0;
    let totalGlobalRemaining = 0;

    let html = `
        <div class="overflow-x-auto max-h-[60vh]">
            <table class="min-w-full text-sm text-left border-collapse">
                <thead class="bg-gray-100 text-gray-600 font-bold sticky top-0 z-10 shadow-sm">
                    <tr>
                        <th class="px-4 py-3 border-b">Licencié</th>
                        <th class="px-4 py-3 border-b text-right">Total Cde</th>
                        <th class="px-4 py-3 border-b text-right text-blue-600">Part. Club</th>
                        <th class="px-4 py-3 border-b text-right text-orange-600">Déjà Versé</th>
                        <th class="px-4 py-3 border-b text-right text-red-600">Reste à Payer</th>
                        <th class="px-4 py-3 border-b text-center">Statut</th>
                    </tr>
                </thead>
                <tbody class="divide-y divide-gray-100">
    `;

    sortedNames.forEach(name => {
        const data = treasuryData[name];
        totalGlobalOrder += data.totalOrder;
        totalGlobalRemaining += data.remaining;

      // Couleur du statut et Bouton de Relance
        let statusBadge = '';
        if (Math.abs(data.remaining) < 0.01) {
            statusBadge = `<span class="inline-flex items-center px-2 py-0.5 rounded text-xs font-medium bg-green-100 text-green-800 cursor-default">✅ Soldé</span>`;
        } else {
            // C'est ici qu'on change : on met un bouton avec une classe spécifique 'btn-relance'
            const colorClass = data.deposit > 0 ? 'bg-orange-100 text-orange-800 border-orange-200' : 'bg-red-100 text-red-800 border-red-200';
            const label = data.deposit > 0 ? 'Partiel ✉️' : 'À régler ✉️';
            
            statusBadge = `
                <button 
                    class="btn-relance inline-flex items-center px-2 py-1 rounded text-xs font-bold border ${colorClass} hover:brightness-95 transition"
                    data-licensee="${name}"
                    title="Cliquez pour envoyer une relance par mail"
                >
                    ${label}
                </button>`;
        }

        html += `
            <tr class="hover:bg-gray-50 transition">
                <td class="px-4 py-2 font-medium text-gray-900">${name}</td>
                <td class="px-4 py-2 text-right font-medium">${data.totalOrder.toFixed(2)}€</td>
                <td class="px-4 py-2 text-right text-blue-600">-${data.participation.toFixed(2)}€</td>
                <td class="px-4 py-2 text-right text-orange-600 font-medium">${data.deposit.toFixed(2)}€</td>
                <td class="px-4 py-2 text-right font-bold ${data.remaining > 0.01 ? 'text-red-600' : 'text-green-600'}">${data.remaining.toFixed(2)}€</td>
                <td class="px-4 py-2 text-center">${statusBadge}</td>
            </tr>
        `;
    });

    html += `
                </tbody>
                <tfoot class="bg-gray-100 font-bold text-gray-800 sticky bottom-0 z-10 shadow-inner">
                    <tr>
                        <td class="px-4 py-3">TOTAL GÉNÉRAL</td>
                        <td class="px-4 py-3 text-right">${totalGlobalOrder.toFixed(2)}€</td>
                        <td class="px-4 py-3 text-right" colspan="2"></td>
                        <td class="px-4 py-3 text-right text-red-700 border-t border-gray-300">${totalGlobalRemaining.toFixed(2)}€</td>
                        <td></td>
                    </tr>
                </tfoot>
            </table>
        </div>
        <div class="mt-4 flex justify-end">
            <button id="export-treasury-excel" class="px-4 py-2 bg-green-600 text-white rounded hover:bg-green-700 text-sm font-medium flex items-center gap-2">
                📥 Exporter Excel
            </button>
        </div>
    `;

    showModal(dom.mainModal, "Suivi Trésorerie & Paiements", document.createElement('div'), [
        { label: 'Fermer', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-500' }
    ], 'max-w-4xl');

    // On injecte le HTML dans la modale
    dom.mainModalBody.innerHTML = html;

    // Gestion du clic Export Excel
    setTimeout(() => {
        const btn = document.getElementById('export-treasury-excel');
        if (btn) {
            btn.onclick = () => {
                if (typeof XLSX === 'undefined') { showToast("Librairie Excel non chargée.", "error"); return; }
                
                const wsData = [
                    ["Suivi Trésorerie - " + state.clubName],
                    ["Date export : " + new Date().toLocaleDateString()],
                    [],
                    ["Licencié", "Total Commande", "Participation Club", "Acompte Versé", "Reste à Payer", "Statut"]
                ];

                sortedNames.forEach(name => {
                    const d = treasuryData[name];
                    let status = "À régler";
                    if(Math.abs(d.remaining) < 0.01) status = "Soldé";
                    else if(d.deposit > 0) status = "Partiel";

                    wsData.push([
                        name, 
                        d.totalOrder, 
                        d.participation, 
                        d.deposit, 
                        d.remaining, 
                        status
                    ]);
                });

                wsData.push([]);
                wsData.push(["TOTAL", totalGlobalOrder, "", "", totalGlobalRemaining, ""]);

                const wb = XLSX.utils.book_new();
                const ws = XLSX.utils.aoa_to_sheet(wsData);
                ws['!cols'] = [{wch:25}, {wch:15}, {wch:15}, {wch:15}, {wch:15}, {wch:15}];

                XLSX.utils.book_append_sheet(wb, ws, "Trésorerie");
                XLSX.writeFile(wb, `TRESORERIE_${state.clubName.replace(/ /g, '_')}.xlsx`);
                showToast("Tableau de trésorerie exporté !");
            };
        }
    }, 100);
setTimeout(() => {
    const modalBody = dom.mainModalBody;
    
    modalBody.addEventListener('click', (e) => {
        // Si on clique sur un bouton de relance
        const btn = e.target.closest('.btn-relance');
        if (btn) {
            const licenseeName = btn.dataset.licensee;
            const data = treasuryData[licenseeName];
            
          // 1. Récupérer le détail des articles
            const items = state.currentOrderLineItems.filter(i => i.licencieName === licenseeName);
            let itemsListText = "";
            
            items.forEach(item => {
                let cleanName = item.productName.replace(/\[.*?\]/g, '').trim();
                
                // ▼▼▼ MODIFICATION ICI ▼▼▼
                // On ajoute un préfixe clair si c'est un pack
                let prefix = "";
                if (item.type === 'pack') {
                    prefix = "[PACK] "; // Ou "📦 PACK " si vous préférez un emoji
                }
                
                itemsListText += `- ${item.totalQuantity}x ${prefix}${cleanName}`;
                // ▲▲▲ ---------------- ▲▲▲

                const sizes = Object.keys(item.quantitiesPerSize || {}).join(', ');
                if(sizes && sizes !== 'U') itemsListText += ` (${sizes})`;
                
                itemsListText += "\n";
            });

            // 2. Demander l'email
            const email = prompt(`Adresse email pour relancer ${licenseeName} ?`);
            
            if (email) {
                // 3. Construire le mail
                const subject = encodeURIComponent(`Relance paiement commande club - ${state.clubName}`);
                
                let body = `Bonjour ${licenseeName},\n\n`;
                body += `Voici le récapitulatif de ta commande pour le club ${state.clubName} :\n\n`;
                body += itemsListText;
                body += `\n--------------------------------\n`;
                body += `Total commande : ${data.totalOrder.toFixed(2)} €\n`;
                if (data.participation > 0) body += `Participation club : -${data.participation.toFixed(2)} €\n`;
                if (data.deposit > 0) body += `Acompte déjà versé : -${data.deposit.toFixed(2)} €\n`;
                body += `\nRESTE À RÉGLER : ${data.remaining.toFixed(2)} €\n`;
                body += `\nMerci de régulariser ce montant rapidement.\n\nCordialement,\nLe responsable commandes.`;

                // 4. Ouvrir le logiciel de messagerie
                window.location.href = `mailto:${email}?subject=${subject}&body=${encodeURIComponent(body)}`;
            }
        }
    });
}, 100);
};
const showStatisticsModal = () => {
    if (state.currentOrderLineItems.length === 0) {
        showToast("Aucune donnée à analyser.", "error");
        return;
    }

    // --- 1. CALCULS ---

    // A. Données Financières & Globales
    const totals = calculateAllTotals();
    const totalRevenue = totals.grandTotalTTC;
    
    let uniqueLicensees = new Set();
    state.currentOrderLineItems.forEach(item => {
        if (item.licencieName && item.licencieName !== 'Commande Globale' && item.licencieName !== 'Stock Club' && item.licencieName !== 'STOCK CLUB') {
            uniqueLicensees.add(item.licencieName);
        }
    });
    const licenseeCount = uniqueLicensees.size || 1;
    const averageBasket = totalRevenue / licenseeCount;

    // B. Agrégation des produits & Catégories
    const productStats = {}; 
    const categoryStats = { 'Haut': 0, 'Bas': 0, 'Accessoire': 0, 'Autre': 0 };
    let qtyFromStock = 0;
    let qtyProduction = 0;

    const allItems = unrollPacksForDistribution(state.currentOrderLineItems);

    allItems.forEach(item => {
        // 1. Stats Produit
        const pName = item.productName;
        if (!productStats[pName]) {
            productStats[pName] = { totalQty: 0, sizes: {} };
        }
        productStats[pName].totalQty += item.totalQuantity;

        if (item.quantitiesPerSize) {
            for (const [size, qty] of Object.entries(item.quantitiesPerSize)) {
                productStats[pName].sizes[size] = (productStats[pName].sizes[size] || 0) + qty;
            }
        } else if (item.totalQuantity > 0) {
            const sizeKey = item.size || "U";
            productStats[pName].sizes[sizeKey] = (productStats[pName].sizes[sizeKey] || 0) + item.totalQuantity;
        }

        // 2. Stats Catégorie & Stock
        const productInfo = productMap.get(pName);
        if (productInfo) {
            // Catégorie simplifiée
            if (productInfo.type === 'accessoire') categoryStats['Accessoire'] += item.totalQuantity;
            else if (productInfo.isCuissardOrCollant || productInfo.subtype.includes('Bas') || productInfo.subtype.includes('Short')) categoryStats['Bas'] += item.totalQuantity;
            else categoryStats['Haut'] += item.totalQuantity;
        } else {
            categoryStats['Autre'] += item.totalQuantity;
        }

        // Stock vs Prod
        if (item.isFromStock) qtyFromStock += item.totalQuantity;
        else qtyProduction += item.totalQuantity;
    });

    // C. Top Acheteurs (Si mode licencié)
    let topSpenders = [];
    if (state.orderScope === 'licensee') {
        const spenderMap = {};
        state.currentOrderLineItems.forEach(item => {
            if (item.licencieName && item.licencieName !== 'Commande Globale' && item.licencieName !== 'Stock Club') {
                // Prix remisé ou normal
                let price = item.totalPriceTTC;
                const rangeItem = state.clubProductRange.find(r => r.name === item.productName);
                if (!item.isStockOrder && !item.isFromStock && rangeItem && rangeItem.licenseePrice) {
                    price = rangeItem.licenseePrice * item.totalQuantity;
                } else if (item.appliedDiscountRate > 0) {
                    price = price * (1 - (item.appliedDiscountRate/100));
                }
                spenderMap[item.licencieName] = (spenderMap[item.licencieName] || 0) + price;
            }
        });
        topSpenders = Object.entries(spenderMap)
            .sort((a, b) => b[1] - a[1])
            .slice(0, 3); // Top 3
    }

    // D. Tri Produits
    const sortedProducts = Object.entries(productStats)
        .map(([name, data]) => ({ name, ...data }))
        .sort((a, b) => b.totalQty - a.totalQty);

    const totalItems = qtyFromStock + qtyProduction;

    // --- 2. UI MODALE ---

    const container = document.createElement('div');
    container.className = 'space-y-6';

    // KPI Cards
    container.innerHTML = `
        <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
            <div class="bg-blue-50 p-3 rounded-lg border border-blue-200 text-center">
                <h3 class="text-blue-800 text-xs font-bold uppercase">CA Total TTC</h3>
                <p class="text-xl font-extrabold text-blue-900">${totalRevenue.toFixed(2)} €</p>
            </div>
            <div class="bg-purple-50 p-3 rounded-lg border border-purple-200 text-center">
                <h3 class="text-purple-800 text-xs font-bold uppercase">Commandes</h3>
                <p class="text-xl font-extrabold text-purple-900">${uniqueLicensees.size}</p>
            </div>
            <div class="bg-green-50 p-3 rounded-lg border border-green-200 text-center">
                <h3 class="text-green-800 text-xs font-bold uppercase">Panier Moyen</h3>
                <p class="text-xl font-extrabold text-green-900">${state.orderScope === 'global' ? 'N/A' : averageBasket.toFixed(2) + ' €'}</p>
            </div>
            <div class="bg-orange-50 p-3 rounded-lg border border-orange-200 text-center">
                <h3 class="text-orange-800 text-xs font-bold uppercase">Total Articles</h3>
                <p class="text-xl font-extrabold text-orange-900">${totalItems}</p>
            </div>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div class="border rounded-lg p-4 bg-white shadow-sm">
                <h4 class="font-bold text-gray-700 mb-3 border-b pb-2">📦 Répartition par Type</h4>
                <div class="space-y-3">
                    <div>
                        <div class="flex justify-between text-xs mb-1"><span>Hauts</span><span>${categoryStats['Haut']}</span></div>
                        <div class="w-full bg-gray-200 rounded-full h-2"><div class="bg-indigo-500 h-2 rounded-full" style="width: ${(categoryStats['Haut']/totalItems*100)||0}%"></div></div>
                    </div>
                    <div>
                        <div class="flex justify-between text-xs mb-1"><span>Bas</span><span>${categoryStats['Bas']}</span></div>
                        <div class="w-full bg-gray-200 rounded-full h-2"><div class="bg-teal-500 h-2 rounded-full" style="width: ${(categoryStats['Bas']/totalItems*100)||0}%"></div></div>
                    </div>
                    <div>
                        <div class="flex justify-between text-xs mb-1"><span>Accessoires</span><span>${categoryStats['Accessoire']}</span></div>
                        <div class="w-full bg-gray-200 rounded-full h-2"><div class="bg-pink-500 h-2 rounded-full" style="width: ${(categoryStats['Accessoire']/totalItems*100)||0}%"></div></div>
                    </div>
                </div>
                <div class="mt-4 pt-3 border-t text-xs text-gray-500 flex justify-between">
                    <span>🏭 À Produire: <strong>${qtyProduction}</strong></span>
                    <span>📦 Du Stock: <strong>${qtyFromStock}</strong></span>
                </div>
            </div>

            <div class="border rounded-lg p-4 bg-white shadow-sm">
                <h4 class="font-bold text-gray-700 mb-3 border-b pb-2">🏆 Top 3 Acheteurs</h4>
                ${topSpenders.length > 0 ? `
                    <ul class="space-y-2">
                        ${topSpenders.map((s, i) => `
                            <li class="flex justify-between items-center p-2 bg-gray-50 rounded">
                                <div class="flex items-center gap-2">
                                    <span class="w-5 h-5 rounded-full bg-yellow-400 text-white text-xs flex items-center justify-center font-bold">${i+1}</span>
                                    <span class="font-medium text-sm text-gray-800">${s[0]}</span>
                                </div>
                                <span class="font-bold text-gray-600 text-sm">${s[1].toFixed(2)} €</span>
                            </li>
                        `).join('')}
                    </ul>
                ` : '<p class="text-sm text-gray-400 italic text-center py-4">Données insuffisantes ou mode global actif.</p>'}
            </div>
        </div>

        <div>
            <h3 class="text-lg font-bold text-gray-800 mb-3 border-b pb-2">Détail par Article</h3>
            <div class="overflow-y-auto max-h-[40vh] border rounded-lg">
                <table class="min-w-full text-sm text-left">
                    <thead class="bg-gray-100 text-gray-700 sticky top-0">
                        <tr>
                            <th class="px-4 py-2 font-bold">Article</th>
                            <th class="px-4 py-2 font-bold text-center">Qté</th>
                            <th class="px-4 py-2 font-bold">Tailles (Top)</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y divide-gray-200">
                        ${sortedProducts.map(p => {
                            const sortedSizes = Object.entries(p.sizes)
                                .sort((a, b) => b[1] - a[1])
                                .slice(0, 4)
                                .map(([s, q]) => `<span class="inline-block bg-gray-100 px-1.5 py-0.5 rounded text-[10px] mr-1"><b>${s}:</b> ${q}</span>`)
                                .join('');
                            return `
                                <tr class="hover:bg-gray-50">
                                    <td class="px-4 py-2 font-medium text-xs">${p.name}</td>
                                    <td class="px-4 py-2 text-center font-bold text-indigo-600">${p.totalQty}</td>
                                    <td class="px-4 py-2 text-gray-600">${sortedSizes}</td>
                                </tr>
                            `;
                        }).join('')}
                    </tbody>
                </table>
            </div>
        </div>
    `;

    // --- 3. EXPORT PDF ---
   // --- 3. EXPORT PDF (Version Graphique & Visuelle) ---
    const handleExportStatsPdf = () => {
        const { jsPDF } = window.jspdf;
        const doc = new jsPDF();
        const pageWidth = doc.internal.pageSize.width;
        const margin = 14;
        let currentY = 20;

        // Fonction utilitaire pour dessiner des boites arrondies (KPI)
        const drawCard = (x, y, w, h, colorBg, colorText, title, value) => {
            doc.setFillColor(...colorBg);
            doc.roundedRect(x, y, w, h, 3, 3, 'F');
            
            doc.setTextColor(...colorText);
            doc.setFontSize(8);
            doc.setFont(undefined, 'bold');
            doc.text(title.toUpperCase(), x + w/2, y + 8, { align: 'center' });
            
            doc.setFontSize(14);
            doc.setFont(undefined, 'bold'); // Extra bold simulé
            doc.text(value, x + w/2, y + 18, { align: 'center' });
        };

       // --- TITRE ---
        doc.setTextColor(0);
        doc.setFontSize(18);
        doc.setFont(undefined, 'bold');
        doc.text(`Rapport Statistique - ${state.clubName}`, margin, currentY);
        currentY += 8;

        // Fonction locale pour formater la date (YYYY-MM-DD -> DD/MM/YYYY)
        const formatOrderDate = (d) => {
            if (!d) return 'N/A';
            const p = d.split('-');
            return p.length === 3 ? `${p[2]}/${p[1]}/${p[0]}` : d;
        };

        // Sous-titre : Date Commande & N° Client
        doc.setFontSize(11);
        doc.setFont(undefined, 'bold'); // En gras pour ressortir
        doc.setTextColor(80); // Gris foncé
        doc.text(`Date de commande : ${formatOrderDate(state.orderDate)}   |   N° Client : ${state.clientNumber || 'N/A'}`, margin, currentY);
        currentY += 6;

        // Date de génération (plus discret)
        doc.setFontSize(9);
        doc.setFont(undefined, 'normal');
        doc.setTextColor(150); // Gris clair
        doc.text(`Document généré le : ${new Date().toLocaleDateString('fr-FR')}`, margin, currentY);
        currentY += 15;

        // --- SECTION 1 : KPI CARDS (Les 4 boites colorées) ---
        const cardWidth = (pageWidth - (margin * 2) - 9) / 4; // 3 espaces de 3mm
        const cardHeight = 25;
        
        // Bleu
        drawCard(margin, currentY, cardWidth, cardHeight, [239, 246, 255], [30, 64, 175], "CA Total TTC", `${totalRevenue.toFixed(2)} €`);
        // Violet
        drawCard(margin + cardWidth + 3, currentY, cardWidth, cardHeight, [250, 245, 255], [107, 33, 168], "Commandes", `${uniqueLicensees.size}`);
        // Vert
        drawCard(margin + (cardWidth + 3)*2, currentY, cardWidth, cardHeight, [240, 253, 244], [22, 101, 52], "Panier Moyen", state.orderScope === 'global' ? 'N/A' : `${averageBasket.toFixed(2)} €`);
        // Orange
        drawCard(margin + (cardWidth + 3)*3, currentY, cardWidth, cardHeight, [255, 247, 237], [154, 52, 18], "Total Articles", `${totalItems}`);

        currentY += cardHeight + 15;

// --- SECTION 2 : GRAPHIQUES & TOP LISTE (2 Colonnes) ---
        const colWidth = (pageWidth - (margin * 2) - 10) / 2;
        
        // DEFINITION DE LA HAUTEUR FIXE POUR LES DEUX BOITES
        const boxHeight = 80; // Augmenté à 80 pour laisser de la place au footer

        // --- COLONNE GAUCHE : Répartition ---
        doc.setDrawColor(220);
        doc.setFillColor(255, 255, 255);
        doc.roundedRect(margin, currentY, colWidth, boxHeight, 2, 2, 'FD'); // Cadre Gauche
        
        doc.setFontSize(11); doc.setTextColor(0); doc.setFont(undefined, 'bold');
        doc.text("Répartition par Type", margin + 5, currentY + 10); // Titre un peu plus bas
        
        let barY = currentY + 22; // Premier graphique un peu plus bas
        
        const drawBar = (label, value, color, percentage) => {
            // Label & Valeur
            doc.setFontSize(9); doc.setTextColor(80); doc.setFont(undefined, 'normal');
            doc.text(label, margin + 5, barY);
            doc.text(String(value), margin + colWidth - 5, barY, { align: 'right' });
            
            barY += 2;
            // Fond gris
            doc.setFillColor(229, 231, 235);
            doc.roundedRect(margin + 5, barY, colWidth - 10, 3, 1, 1, 'F');
            // Barre colorée
            if (percentage > 0) {
                doc.setFillColor(...color);
                doc.roundedRect(margin + 5, barY, (colWidth - 10) * percentage, 3, 1, 1, 'F');
            }
            barY += 12; // Espace un peu plus grand entre les barres (aéré)
        };

        drawBar("Hauts", categoryStats['Haut'], [99, 102, 241], (categoryStats['Haut']/totalItems)); 
        drawBar("Bas", categoryStats['Bas'], [20, 184, 166], (categoryStats['Bas']/totalItems)); 
        drawBar("Accessoires", categoryStats['Accessoire'], [236, 72, 153], (categoryStats['Accessoire']/totalItems)); 

        // --- PIED DE PAGE DANS LA BOITE (Ancré au bas de la boite) ---
        const footerY = currentY + boxHeight - 8; // Position fixe par rapport au bas

        // Ligne de séparation
        doc.setDrawColor(240);
        doc.line(margin + 5, footerY - 5, margin + colWidth - 5, footerY - 5);
        
        // Textes Production / Stock
        doc.setFontSize(8); doc.setTextColor(100);
        doc.text(`Production: ${qtyProduction}`, margin + 5, footerY);
        doc.text(`Stock: ${qtyFromStock}`, margin + colWidth - 5, footerY, { align: 'right' });


        // --- COLONNE DROITE : Top Acheteurs ---
        const col2X = margin + colWidth + 10;
        doc.setDrawColor(220);
        doc.setFillColor(255, 255, 255);
        // On utilise la même boxHeight pour l'alignement visuel
        doc.roundedRect(col2X, currentY, colWidth, boxHeight, 2, 2, 'FD'); 

        doc.setFontSize(11); doc.setTextColor(0); doc.setFont(undefined, 'bold');
        doc.text("Top 3 Acheteurs", col2X + 5, currentY + 10);

        let listY = currentY + 25; // Liste un peu plus basse
        if (topSpenders.length > 0) {
            topSpenders.forEach((spender, i) => {
                // Badge Rond
                doc.setFillColor(250, 204, 21); // Jaune
                doc.circle(col2X + 8, listY - 1, 3, 'F');
                doc.setTextColor(255); doc.setFontSize(7); doc.setFont(undefined, 'bold');
                doc.text(String(i+1), col2X + 8, listY, { align: 'center', baseline: 'middle' });

                // Nom
                doc.setTextColor(50); doc.setFontSize(9); doc.setFont(undefined, 'normal');
                doc.text(spender[0], col2X + 15, listY);

                // Prix
                doc.setFont(undefined, 'bold');
                doc.text(`${spender[1].toFixed(2)} €`, col2X + colWidth - 5, listY, { align: 'right' });

                // Ligne séparation légère
                if (i < 2) {
                    doc.setDrawColor(245);
                    doc.line(col2X + 5, listY + 6, col2X + colWidth - 5, listY + 6);
                }
                listY += 16; // Plus d'espace entre les acheteurs
            });
        } else {
            doc.setFontSize(9); doc.setTextColor(150); doc.setFont(undefined, 'italic');
            doc.text("Données insuffisantes ou mode global.", col2X + colWidth/2, listY + 10, { align: 'center' });
        }

        // IMPORTANT : On descend le curseur global en fonction de la nouvelle hauteur de boite
        currentY += boxHeight + 10;

      // --- SECTION 3 : TABLEAU DÉTAILLÉ ---
        doc.setFontSize(12); doc.setTextColor(0); doc.setFont(undefined, 'bold');
        doc.text("Classement des articles (par quantité commandée)", margin, currentY);
        currentY += 5;

        const body = sortedProducts.map(p => {
            // Mise en forme des tailles comme dans le HTML (Top 5)
            const sortedSizes = Object.entries(p.sizes)
                .sort((a, b) => b[1] - a[1])
                .slice(0, 5)
                .map(([s, q]) => `${s}: ${q}`)
                .join('  |  ');
            
            return [p.name, p.totalQty, sortedSizes];
        });

        doc.autoTable({
            startY: currentY,
            head: [['Article', 'Qté', 'Répartition des Tailles (Top 5)']],
            body: body,
            theme: 'grid',
            headStyles: { fillColor: [243, 244, 246], textColor: [55, 65, 81], fontStyle: 'bold', lineColor: [229, 231, 235] },
            styles: { fontSize: 9, cellPadding: 3, valign: 'middle', textColor: [75, 85, 99] },
            columnStyles: {
                0: { cellWidth: 80, fontStyle: 'bold', textColor: [17, 24, 39] },
                1: { cellWidth: 20, halign: 'center', fontStyle: 'bold', textColor: [79, 70, 229] },
                2: { cellWidth: 'auto', fontSize: 8 }
            },
            alternateRowStyles: { fillColor: [255, 255, 255] }
        });

        doc.save(`Statistiques_${state.clubName.replace(/ /g, '_')}.pdf`);
        showToast("PDF Statistiques (Graphique) généré !");
    };

    showModal(dom.mainModal, "Statistiques Avancées", container, [
        { label: 'Fermer', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-500' },
        { label: '📄 Télécharger PDF', onClick: handleExportStatsPdf, className: 'bg-purple-600 hover:bg-purple-700 text-white' }
    ], 'max-w-4xl');
};
const showCatalogEditor = () => {
    
    // Fonction interne pour afficher l'interface
    const renderEditorInterface = () => {
        const container = document.createElement('div');
        container.className = 'flex flex-col h-[70vh]'; // Grande hauteur

        container.innerHTML = `
            <div class="flex justify-between items-center mb-4 pb-4 border-b">
                <div class="text-sm text-gray-600">
                    Produits totaux : <strong>${allAvailableProducts.length}</strong>
                </div>
                <div class="flex gap-2">
                    <button id="cat-export-btn" class="px-3 py-1 bg-blue-100 text-blue-700 rounded text-xs font-bold">Exporter Catalogue</button>
                    <label for="cat-import-input" class="px-3 py-1 bg-gray-100 text-gray-700 rounded text-xs font-bold cursor-pointer">Importer</label>
                    <input type="file" id="cat-import-input" class="hidden" accept=".json">
                    <button id="cat-reset-btn" class="px-3 py-1 bg-red-100 text-red-700 rounded text-xs font-bold">Réinitialiser (Usine)</button>
                </div>
            </div>

            <div class="flex flex-1 overflow-hidden gap-4">
                <div class="w-1/3 flex flex-col border-r pr-4">
                    <input type="text" id="cat-search" placeholder="Rechercher..." class="mb-2 p-2 border rounded text-sm">
                    <div id="cat-list" class="flex-1 overflow-y-auto space-y-1"></div>
                    <button id="cat-add-btn" class="mt-2 w-full py-2 bg-green-600 text-white font-bold rounded hover:bg-green-700">+ Nouveau Produit</button>
                </div>

                <div class="w-2/3 flex flex-col overflow-y-auto pl-2" id="cat-form-container">
                    <p class="text-gray-400 text-center mt-20">Sélectionnez un produit pour le modifier ou créez-en un nouveau.</p>
                </div>
            </div>
        `;

        // Rendu de la liste
        const listContainer = container.querySelector('#cat-list');
        const searchInput = container.querySelector('#cat-search');
        
        const renderList = (filter = '') => {
            listContainer.innerHTML = '';
            const filtered = allAvailableProducts
                .filter(p => p.name.toLowerCase().includes(filter.toLowerCase()))
                .sort((a, b) => a.name.localeCompare(b.name));

            filtered.forEach(p => {
                const div = document.createElement('div');
                div.className = 'p-2 border rounded cursor-pointer hover:bg-indigo-50 text-xs truncate';
                div.textContent = p.name;
                div.onclick = () => loadProductForm(p);
                listContainer.appendChild(div);
            });
        };

        // Chargement du formulaire d'édition
        const loadProductForm = (product = null) => {
            const formContainer = container.querySelector('#cat-form-container');
            const isNew = !product;
            
            // Valeurs par défaut
            const p = product || { 
                name: '', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'Divers', 
                supplier: 'NORET', price: 0, minQuantity: 1 
            };

            // Si c'est un produit complexe (avec pricingTiers), on simplifie l'affichage pour V1
            const hasTiers = p.pricingTiers && p.pricingTiers.length > 0;
            const displayPrice = hasTiers ? (p.pricingTiers[0].price) : (p.price || 0);

            formContainer.innerHTML = `
                <h3 class="font-bold text-lg mb-4 text-indigo-800">${isNew ? 'Créer un produit' : 'Modifier le produit'}</h3>
                <form id="cat-edit-form" class="space-y-3 text-sm">
                    <div>
                        <label class="block font-bold text-gray-700">Nom du produit (Unique)</label>
                        <input type="text" id="edit-name" value="${p.name}" class="w-full border p-2 rounded" ${!isNew ? 'readonly class="bg-gray-100"' : ''}>
                    </div>
                    <div class="grid grid-cols-2 gap-4">
                        <div>
                            <label class="block font-bold text-gray-700">Catégorie</label>
                            <select id="edit-category" class="w-full border p-2 rounded">
                                <option value="CYCLISME" ${p.category==='CYCLISME'?'selected':''}>CYCLISME</option>
                                <option value="RUNNING" ${p.category==='RUNNING'?'selected':''}>RUNNING</option>
                                <option value="ACCESSOIRES" ${p.category==='ACCESSOIRES'?'selected':''}>ACCESSOIRES</option>
                                <option value="ENFANTS" ${p.category==='ENFANTS'?'selected':''}>ENFANTS</option>
                                <option value="BRODELEC" ${p.category==='BRODELEC'?'selected':''}>BRODELEC</option>
                            </select>
                        </div>
                        <div>
                            <label class="block font-bold text-gray-700">Sous-Type</label>
                            <input type="text" id="edit-subtype" value="${p.subtype || ''}" class="w-full border p-2 rounded">
                        </div>
                    </div>
                    <div class="grid grid-cols-2 gap-4">
                        <div>
                            <label class="block font-bold text-gray-700">Fournisseur</label>
                            <select id="edit-supplier" class="w-full border p-2 rounded">
                                <option value="NORET" ${p.supplier!=='BRODELEC'?'selected':''}>NORET</option>
                                <option value="BRODELEC" ${p.supplier==='BRODELEC'?'selected':''}>BRODELEC</option>
                            </select>
                        </div>
                        <div>
                            <label class="block font-bold text-gray-700">Prix de base TTC (€)</label>
                            <input type="number" step="0.01" id="edit-price" value="${displayPrice}" class="w-full border p-2 rounded font-bold">
                            ${hasTiers ? '<p class="text-xs text-orange-600 mt-1">⚠️ Ce produit possède une grille tarifaire complexe. Modifier ce prix écrasera la grille par un prix fixe unique.</p>' : ''}
                        </div>
                    </div>
                    
                    <div class="pt-4 flex gap-2 border-t mt-4">
                        <button type="button" id="btn-save-prod" class="flex-1 bg-indigo-600 text-white py-2 rounded hover:bg-indigo-700">Enregistrer</button>
                        ${!isNew ? '<button type="button" id="btn-delete-prod" class="px-4 bg-red-600 text-white py-2 rounded hover:bg-red-700">Supprimer</button>' : ''}
                    </div>
                </form>
            `;

            // SAVE handler
            formContainer.querySelector('#btn-save-prod').onclick = () => {
                const newName = document.getElementById('edit-name').value.trim();
                if (!newName) return showToast("Le nom est obligatoire", "error");

                // Création de l'objet
                const newProd = {
                    ...p, // Garde les anciennes propriétés (images, options...)
                    name: newName,
                    category: document.getElementById('edit-category').value,
                    subtype: document.getElementById('edit-subtype').value,
                    supplier: document.getElementById('edit-supplier').value,
                    type: document.getElementById('edit-category').value === 'ACCESSOIRES' ? 'accessoire' : 'haut', // Simplification
                    price: parseFloat(document.getElementById('edit-price').value) || 0,
                    pricingTiers: undefined // On écrase la grille complexe par un prix fixe pour simplifier
                };

                // Mise à jour du tableau global
                if (isNew) {
                    if (allAvailableProducts.find(x => x.name === newName)) return showToast("Ce nom existe déjà", "error");
                    allAvailableProducts.push(newProd);
                } else {
                    const idx = allAvailableProducts.findIndex(x => x.name === p.name);
                    if (idx > -1) allAvailableProducts[idx] = newProd;
                }

                // Sauvegarde
                refreshProductMap();
                localStorage.setItem('customCatalog', JSON.stringify(allAvailableProducts));
                renderList(searchInput.value);
                showToast("Produit enregistré !");
            };

            // DELETE handler
            if (!isNew) {
                formContainer.querySelector('#btn-delete-prod').onclick = () => {
                    if (confirm(`Supprimer définitivement "${p.name}" ?`)) {
                        allAvailableProducts = allAvailableProducts.filter(x => x.name !== p.name);
                        refreshProductMap();
                        localStorage.setItem('customCatalog', JSON.stringify(allAvailableProducts));
                        renderList(searchInput.value);
                        formContainer.innerHTML = '<p class="text-gray-400 text-center mt-20">Produit supprimé.</p>';
                        showToast("Produit supprimé.");
                    }
                };
            }
        };

        // Events
        searchInput.addEventListener('input', (e) => renderList(e.target.value));
        container.querySelector('#cat-add-btn').onclick = () => loadProductForm();
        
        // Reset Usine
        container.querySelector('#cat-reset-btn').onclick = () => {
            if (confirm("ATTENTION : Cela va effacer toutes vos modifications de prix et de produits et remettre le catalogue par défaut du code source. Continuer ?")) {
                localStorage.removeItem('customCatalog');
                location.reload(); // Recharge la page pour remettre le const original
            }
        };

        // Export Catalogue
        container.querySelector('#cat-export-btn').onclick = () => {
            const dataStr = JSON.stringify(allAvailableProducts, null, 2);
            const blob = new Blob([dataStr], { type: "application/json" });
            const a = document.createElement('a');
            a.href = URL.createObjectURL(blob);
            a.download = `CATALOGUE_CLUB_${new Date().toISOString().split('T')[0]}.json`;
            a.click();
        };

        // Import Catalogue
        container.querySelector('#cat-import-input').addEventListener('change', (e) => {
            const file = e.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = (evt) => {
                try {
                    const loaded = JSON.parse(evt.target.result);
                    if (Array.isArray(loaded)) {
                        if (confirm(`Remplacer le catalogue actuel par ces ${loaded.length} produits ?`)) {
                            allAvailableProducts = loaded;
                            refreshProductMap();
                            localStorage.setItem('customCatalog', JSON.stringify(allAvailableProducts));
                            renderList();
                            showToast("Catalogue importé avec succès !");
                        }
                    }
                } catch(err) { showToast("Fichier invalide", "error"); }
            };
            reader.readAsText(file);
        });

        // Init
        renderList();
        return container;
    };

    // Protection par mot de passe
    promptForAdminPassword(() => {
        showModal(dom.mainModal, "Éditeur de Catalogue", renderEditorInterface(), [
            { label: "Fermer", onClick: () => { hideModal(dom.mainModal); renderProductForm(); }, className: "bg-gray-600" }
        ], 'max-w-6xl'); // Très large
    });
};
const showLicenseeRecapModal = (targetLicenseeName = null) => {
    // Si aucun nom passé, on prend celui actif, sinon celui du bouton cliqué
    const licenseeName = targetLicenseeName || state.activeLicensee;
    
    if (!licenseeName) {
        showToast("Aucun licencié sélectionné.", "error");
        return;
    }

    // Récupérer les articles de ce licencié
    const items = state.currentOrderLineItems.filter(i => i.licencieName === licenseeName);

    if (items.length === 0) {
        showToast("Le panier de ce licencié est vide.", "warning");
        return;
    }

    // Calcul du total
    let totalTTC = 0;
    const itemsHtml = items.map(item => {
        let price = item.totalPriceTTC;
        const rangeItem = state.clubProductRange.find(r => r.name === item.productName);
        if (!item.isStockOrder && !item.isFromStock && rangeItem && rangeItem.licenseePrice) {
            price = rangeItem.licenseePrice * item.totalQuantity;
        } else if (item.appliedDiscountRate > 0) {
            price = price * (1 - (item.appliedDiscountRate / 100));
        }
        totalTTC += price;

        let details = getSortedSizesText(item);
        if(item.options.length > 0) details += `<br><span class="text-xs text-gray-500">Opt: ${item.options.join(', ')}</span>`;
        if(item.visual) details += `<br><span class="text-xs text-gray-500">Visuel: ${item.visual}</span>`;

        let nameDisplay = item.productName;
        if (item.type === 'pack') {
            nameDisplay = `<strong>[PACK]</strong> ${item.productName}`;
            details = item.packItems.map(p => `${p.productName} (${p.size})`).join('<br>');
        }

        return `
            <tr class="border-b">
                <td class="py-2 pl-2">
                    <div class="font-medium">${nameDisplay}</div>
                    <div class="text-sm text-gray-600">${details}</div>
                </td>
                <td class="py-2 text-center font-bold">${item.totalQuantity}</td>
                <td class="py-2 pr-2 text-right font-bold">${price.toFixed(2)}€</td>
            </tr>
        `;
    }).join('');

    const container = document.createElement('div');
    container.innerHTML = `
        <div class="bg-gray-50 p-4 rounded-lg border border-gray-200 mb-4">
            <h4 class="font-bold text-lg text-center text-gray-800 mb-4 border-b pb-2">Récapitulatif pour ${licenseeName}</h4>
            <table class="w-full text-left text-sm bg-white rounded shadow-sm overflow-hidden">
                <thead class="bg-gray-100 text-gray-700">
                    <tr>
                        <th class="py-2 pl-2">Article</th>
                        <th class="py-2 text-center">Qté</th>
                        <th class="py-2 pr-2 text-right">Prix</th>
                    </tr>
                </thead>
                <tbody>${itemsHtml}</tbody>
                <tfoot>
                    <tr class="bg-gray-50 font-bold text-indigo-900">
                        <td class="py-2 pl-2 text-right" colspan="2">TOTAL À RÉGLER :</td>
                        <td class="py-2 pr-2 text-right text-lg">${totalTTC.toFixed(2)}€</td>
                    </tr>
                </tfoot>
            </table>
        </div>
        <div class="text-center p-3 bg-yellow-50 border border-yellow-200 rounded text-sm text-yellow-800">
            <p><strong>En cliquant sur "Valider" :</strong></p>
            <p>1. Le client s'engage sur cette commande.</p>
            <p>2. Vous passez automatiquement au licencié suivant.</p>
        </div>
    `;

    // Fonction PDF interne (pour le bouton optionnel)
    const generateTicketPDF = () => {
        const { jsPDF } = window.jspdf;
        const doc = new jsPDF({ format: 'a5' });
        const width = doc.internal.pageSize.width;
        let y = 15;
        doc.setFontSize(14); doc.setFont(undefined, 'bold');
        doc.text("RÉCÉPISSÉ DE COMMANDE", width/2, y, { align: 'center' });
        y += 10;
        doc.setFontSize(12);
        doc.text(`${licenseeName}`, 10, y);
        doc.setFontSize(10); doc.setFont(undefined, 'normal');
        doc.text(state.clubName, width-10, y, { align: 'right' });
        y += 10; doc.setDrawColor(200); doc.line(10, y, width-10, y); y += 5;
        
        items.forEach(item => {
            let name = item.productName;
            if(item.type === 'pack') name = `[PACK] ${name}`;
            if (name.length > 35) name = name.substring(0, 35) + '...';
            doc.setFont(undefined, 'bold');
            doc.text(`${item.totalQuantity}x ${name}`, 10, y);
            
            let price = item.totalPriceTTC;
            const rangeItem = state.clubProductRange.find(r => r.name === item.productName);
            if (!item.isStockOrder && !item.isFromStock && rangeItem && rangeItem.licenseePrice) {
                price = rangeItem.licenseePrice * item.totalQuantity;
            } else if (item.appliedDiscountRate > 0) {
                price = price * (1 - (item.appliedDiscountRate / 100));
            }
            doc.text(`${price.toFixed(2)} €`, width-10, y, { align: 'right' });
            y += 5;
            
            doc.setFont(undefined, 'normal'); doc.setFontSize(9); doc.setTextColor(80);
            let details = "";
            if (item.type === 'pack') details = item.packItems.map(p => `- ${p.productName} (${p.size})`).join('\n');
            else {
                details = getSortedSizesText(item);
                if(item.options.length) details += ` + ${item.options.join(',')}`;
            }
            const splitDetails = doc.splitTextToSize(details, width - 25);
            doc.text(splitDetails, 15, y);
            y += (splitDetails.length * 4) + 2;
            doc.setFontSize(10); doc.setTextColor(0);
        });
        
        y += 5; doc.line(10, y, width-10, y); y += 8;
        doc.setFontSize(12); doc.setFont(undefined, 'bold');
        doc.text("TOTAL :", 10, y); doc.text(`${totalTTC.toFixed(2)} €`, width-10, y, { align: 'right' });
        doc.save(`TICKET_${licenseeName.replace(/ /g, '_')}.pdf`);
        showToast("Ticket téléchargé.");
    };

// --- BOUTONS D'ACTION (DANS showLicenseeRecapModal) ---
    const actions = [
        { 
            label: '✏️ Modifier', 
            className: 'bg-gray-200 text-gray-800 hover:bg-gray-300',
            onClick: () => {
                hideModal(dom.mainModal);
                state.activeLicensee = licenseeName;
                state.licencieName = ''; 
                renderAll();
                setTimeout(() => {
                    document.getElementById('add-article-section').scrollIntoView({ behavior: 'smooth', block: 'start' });
                }, 100);
            }
        },
        // Bouton mail "seul"
        {
            label: '✉️ Mail seul', 
            className: 'bg-orange-100 text-orange-700 hover:bg-orange-200',
            onClick: () => {
                // MODIFICATION ICI : On passe licenseeName explicitement
                triggerMailLogic(licenseeName, false); 
            }
        },
        // Bouton Valider
        { 
            label: '✅ Valider & Suivant', 
            className: 'bg-green-600 text-white hover:bg-green-700 shadow-lg transform hover:scale-105 transition',
            onClick: () => {
                if (confirm(`Voulez-vous envoyer un récapitulatif par mail à ${licenseeName} avant de passer au suivant ?`)) {
                    // MODIFICATION ICI : On passe licenseeName explicitement
                    triggerMailLogic(licenseeName, true);
                } else {
                    finalizeLicenseeValidation();
                }
            }
        }
    ];
    // --- FONCTIONS UTILITAIRES INTERNES ---

    // 1. La logique de validation pure (Coche verte + Reset + Focus)
    const finalizeLicenseeValidation = () => {
        if (!state.validatedLicensees) state.validatedLicensees = [];
        if (!state.validatedLicensees.includes(licenseeName)) {
            state.validatedLicensees.push(licenseeName);
        }

        hideModal(dom.mainModal);
        state.activeLicensee = ''; 
        state.licencieName = '';
        renderAll();
        
        window.scrollTo({ top: 0, behavior: 'smooth' });
        setTimeout(() => {
            if(dom.licencieNameInput) dom.licencieNameInput.focus();
        }, 500);
        
        showToast(`Commande validée pour ${licenseeName}. Suivant !`);
    };

  // 2. La logique de construction du mail (CORRIGÉE : Prend le nom en paramètre)
const triggerMailLogic = (targetName, shouldFinalizeAfter) => {
    // Si aucun nom n'est passé, on essaie de prendre celui actif, sinon on arrête
    const licenseeName = targetName || state.activeLicensee;
    
    if (!licenseeName) {
        alert("Erreur : Impossible d'identifier le licencié pour l'envoi du mail.");
        return;
    }

    // --- A. RÉCUPÉRATION DE L'EMAIL ---
    let storedEmail = '';
    
    // Recherche directe ou insensible à la casse
    if (state.licenseeEmails) {
        if (state.licenseeEmails[licenseeName]) {
            storedEmail = state.licenseeEmails[licenseeName];
        } else {
            // Recherche souple (si majuscules/espaces différents)
            const foundKey = Object.keys(state.licenseeEmails).find(k => k.trim().toLowerCase() === licenseeName.trim().toLowerCase());
            if (foundKey) storedEmail = state.licenseeEmails[foundKey];
        }
    }
    // ------------------------------------------------

    const subject = `Récapitulatif de commande - ${state.clubName}`;
    let body = `Bonjour ${licenseeName},\n\nVoici le détail technique de ta commande validée :\n\n`;
    
    // --- B. RÉCUPÉRATION DES ARTICLES ---
    // C'est ici que ça échouait avant : on filtre maintenant sur le nom passé en paramètre
    const items = state.currentOrderLineItems.filter(i => i.licencieName === licenseeName);

    if (items.length === 0) {
        alert("Attention : Aucun article trouvé pour ce licencié.");
    }

    items.forEach(item => {
        let name = item.productName;
        if(item.type === 'pack') name = `[PACK] ${name}`;
        let details = getSortedSizesText(item);
        if(item.options.length > 0) details += ` (Opt: ${item.options.join(', ')})`;
        if(item.visual) details += ` (Visuel: ${item.visual})`;
        body += `- ${item.totalQuantity}x ${name}\n  Détails : ${details}\n\n`;
    });

    body += `\n========================================\n⚠️  MENTIONS IMPORTANTES  ⚠️\n========================================\n\n`;
    body += `CONDITIONS :\n>> TOUTE COMMANDE VALIDÉE EST FERME et ne peut faire l'objet d'une réclamation ultérieure.\n\n`;
    body += `RESPONSABILITÉ :\n>> Si l'article n'a pas été essayé, le choix des tailles relève de la RESPONSABILITÉ EXCLUSIVE DU LICENCIÉ.\n`;
    body += `\n========================================\n\nMerci !\n${state.clubName}`;

    // --- INTERFACE ---
    const container = document.createElement('div');
    container.className = "space-y-4 text-sm";
    
    container.innerHTML = `
        <p>Envoi du récapitulatif à <strong>${licenseeName}</strong> :</p>
        
        <div class="mb-3">
             <label class="block text-xs font-bold text-gray-500 mb-1">Email destinataire :</label>
             <input type="text" id="email-recipient-input" value="${storedEmail}" class="w-full border border-gray-300 rounded p-2 text-sm" placeholder="Pas d'email trouvé...">
        </div>

        <div class="grid grid-cols-1 gap-3">
            <button id="btn-action-mailto" class="w-full flex items-center justify-center gap-2 px-4 py-3 bg-blue-600 text-white font-bold rounded hover:bg-blue-700">
                <span>📧 Ouvrir le logiciel de messagerie</span>
            </button>
            
            <div class="text-center text-gray-500 text-xs">- OU -</div>

            <button id="btn-action-copy" class="w-full flex items-center justify-center gap-2 px-4 py-3 bg-gray-200 text-gray-800 font-bold rounded hover:bg-gray-300 border border-gray-300">
                <span>📋 Copier le texte (pour coller ailleurs)</span>
            </button>
        </div>
        
        <div id="copy-feedback" class="hidden text-center text-green-600 font-bold mt-2">
            Texte copié !
        </div>
    `;

    showModal(dom.mainModal, "Envoi du Récapitulatif", container, [
        { 
            label: shouldFinalizeAfter ? 'Passer au suivant' : 'Fermer', 
            className: 'bg-gray-500 text-white',
            onClick: () => {
                hideModal(dom.mainModal);
                if (shouldFinalizeAfter) finalizeLicenseeValidation();
            }
        }
    ]);

    // --- GESTIONNAIRES ---
    setTimeout(() => {
        // Gestion du Clic "Ouvrir Email"
        const btnMailto = document.getElementById('btn-action-mailto');
        if(btnMailto) {
            btnMailto.onclick = () => {
                // On prend l'email qui est affiché dans le champ (modifiable par l'utilisateur)
                const emailInput = document.getElementById('email-recipient-input');
                const finalEmail = emailInput ? emailInput.value.trim() : storedEmail;
                
                if (finalEmail) {
                    // Sauvegarde si c'est un nouvel email
                    if (finalEmail !== storedEmail && licenseeName) {
                        state.licenseeEmails[licenseeName] = finalEmail; 
                    }

                    const mailtoLink = `mailto:${finalEmail}?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(body)}`;
                    
                    if (mailtoLink.length > 2000) {
                        alert("⚠️ Attention : Le contenu est trop long pour un lien automatique. Utilisez le bouton 'Copier le texte'.");
                    } else {
                        window.location.href = mailtoLink;
                    }
                } else {
                    alert("Veuillez saisir une adresse email.");
                    if(emailInput) emailInput.focus();
                }
            };
        }

        // Gestion du Clic "Copier"
        const btnCopy = document.getElementById('btn-action-copy');
        const feedback = document.getElementById('copy-feedback');
        
        if(btnCopy) {
            btnCopy.onclick = () => {
                const fullText = `DESTINATAIRE : ${document.getElementById('email-recipient-input').value}\nSUJET : ${subject}\n\n${body}`;
                navigator.clipboard.writeText(fullText).then(() => {
                    feedback.classList.remove('hidden');
                    btnCopy.classList.remove('bg-gray-200', 'text-gray-800');
                    btnCopy.classList.add('bg-green-100', 'text-green-800', 'border-green-300');
                    btnCopy.textContent = "✅ Texte copié !";
                }).catch(err => {
                    console.error('Erreur copie', err);
                    prompt("Impossible de copier auto. Copiez ceci :", fullText);
                });
            };
        }
    }, 100);
};

    showModal(dom.mainModal, "Validation Commande Licencié", container, actions, 'max-w-xl');
};
const handleSaveAsHtml = () => {
    // 1. Message d'information avant génération
    const infoMessage = document.createElement('div');
    infoMessage.className = 'text-sm space-y-3';
    infoMessage.innerHTML = `
        <p>Vous allez générer le <strong>fichier d'application personnalisé</strong> pour votre club.</p>
        <ul class="list-disc list-inside text-gray-600 space-y-1">
            <li><strong>Inclus :</strong> Gamme du club, liste des licenciés (noms/emails), adresses, visuels et packs.</li>
            <li><strong>Exclus :</strong> Le panier actuel et les commandes en cours seront vidés pour permettre une nouvelle saisie propre.</li>
        </ul>
        <p class="font-bold text-indigo-600">Ce fichier sera votre base pour toutes vos futures sessions.</p>
    `;

    showModal(dom.mainModal, "Génération du fichier Club", infoMessage, [
        { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-400' },
        { 
            label: 'Générer le fichier .html', 
            className: 'bg-indigo-600 text-white font-bold',
            onClick: () => {
                hideModal(dom.mainModal);
                executeFinalHtmlSave();
            }
        }
    ]);
};

// Fonction technique de nettoyage et téléchargement
const executeFinalHtmlSave = () => {
    const clubPart = state.clubName ? state.clubName.trim() : 'CLUB';
    const filename = `APPLICATION_COMMANDE_${clubPart.replace(/[\/\\?%*:|"<>]/g, '-')}.html`;

    // 1. Création d'une copie de l'état
    const cleanState = JSON.parse(JSON.stringify(state));
    
    // 2. Nettoyage des données de SESSION (on vide le panier)
    cleanState.currentOrderLineItems = []; 
    cleanState.activeLicensee = '';
    cleanState.licencieName = '';
    cleanState.orderDate = new Date().toISOString().split('T')[0];
    cleanState.preOrderNumber = '';
    cleanState.orderSpecificity = '';
    cleanState.validatedLicensees = [];
    cleanState.isLoadedFromBackup = false;

    // 3. CONSERVATION de l'intelligence (déjà inclus dans le clone, mais on s'en assure)
    // cleanState.clubProductRange -> Conservé
    // cleanState.licenseeList -> Conservé
    // cleanState.licenseeEmails -> Conservé
    // cleanState.savedPolls -> Conservé (C'est ici que vos sondages sont sauvés)
    // cleanState.clubPacks -> Conservé

    // 4. Préparation du contenu HTML avec injection des données
    const jsonState = JSON.stringify(cleanState);
    let saveContainer = document.getElementById('app-saved-state');
    if (!saveContainer) {
        saveContainer = document.createElement('script');
        saveContainer.id = 'app-saved-state';
        saveContainer.type = 'application/json';
        document.body.appendChild(saveContainer);
    }
    saveContainer.textContent = jsonState;

    const htmlContent = document.documentElement.outerHTML;
    saveContainer.remove();

    // 5. Téléchargement
    try {
        const blob = new Blob([htmlContent], { type: "text/html" });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = filename;
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        URL.revokeObjectURL(url);
        showToast("Fichier de base du club (avec sondages) généré !");
    } catch (e) {
        console.error(e);
        showToast("Erreur lors de la création du fichier.", "error");
    }
};
const showPollManagerModal = () => {
    // Sécurité : On vérifie qu'un club est bien défini
    if (!state.clubName.trim()) {
        showToast("Veuillez d'abord définir un nom de club dans l'interface principale.", "error");
        return;
    }

    // État local du sondage en cours d'édition
    let pollState = {
        id: Date.now(), 
        clubName: state.clubName, 
        name: `Campagne ${new Date().toLocaleDateString()}`, 
        selectedProducts: [],
        intentions: [],
        minGoal: 10,
        deadline: '',
        hasFitting: false,
        fittingDates: ''
    };

    // Pré-remplissage avec la gamme du club si existante
    if (state.clubProductRange.length > 0) {
        pollState.selectedProducts = state.clubProductRange.map(i => i.name);
    }

    // --- HELPER : Récupérer les tailles ---
    const getProductSizesArray = (productName) => {
        const product = productMap.get(productName);
        if (!product) return ['U']; 
        return productSizes[product.sizeType || product.type] || ['U'];
    };

    // --- 1. SAUVEGARDE ---
    const persistPolls = (silent = false) => {
        pollState.clubName = state.clubName;
        const existingIndex = state.savedPolls.findIndex(p => p.id === pollState.id);
        if (existingIndex > -1) {
            state.savedPolls[existingIndex] = JSON.parse(JSON.stringify(pollState));
        } else {
            state.savedPolls.push(JSON.parse(JSON.stringify(pollState)));
        }
        localStorage.setItem('savedPolls', JSON.stringify(state.savedPolls));
        
        if(!silent) showToast("Campagne sauvegardée pour " + state.clubName);
        renderPollUI(document.getElementById('poll-ui-container')); 
    };

    // --- 2. EXPORT JSON ---
    const exportPollJson = () => {
        const safeClubName = state.clubName.replace(/[^a-z0-9]/gi, '_');
        const safePollName = pollState.name.replace(/[^a-z0-9]/gi, '_');
        const filename = `SONDAGE_${safeClubName}_${safePollName}.json`;
        const dataStr = JSON.stringify(pollState, null, 2);
        const blob = new Blob([dataStr], { type: "application/json" });
        const a = document.createElement('a');
        a.href = URL.createObjectURL(blob);
        a.download = filename;
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        showToast("Fichier JSON téléchargé !");
    };

    // --- 3. IMPRESSION PDF (Liste d'émargement) ---
    const printIntentionsSheet = () => {
        const { jsPDF } = window.jspdf;
        const doc = new jsPDF({ orientation: 'landscape' });
        const margin = 14;

        doc.setFontSize(18); doc.setFont(undefined, 'bold');
        doc.text(`FEUILLE D'ESSAYAGE / RÉSERVATION - ${state.clubName.toUpperCase()}`, margin, 15);
        
        doc.setFontSize(10); doc.setFont(undefined, 'normal');
        doc.text(`Campagne : ${pollState.name}`, margin, 22);
        
        const productHeaders = pollState.selectedProducts;
        const head = [['NOM & PRÉNOM', ...productHeaders, 'SIGNATURE / VALIDATION']];

        let body = [];
        const respondents = [...new Set(pollState.intentions.map(i => i.name))].sort((a, b) => a.localeCompare(b));

        // On pré-remplit avec les gens qui ont déjà répondu, sinon lignes vides
        if (respondents.length > 0) {
            body = respondents.map(respondentName => {
                const row = Array(head[0].length).fill('');
                row[0] = respondentName; 
                pollState.selectedProducts.forEach((prodName, index) => {
                    const foundIntention = pollState.intentions.find(i => i.name === respondentName && i.product === prodName);
                    if (foundIntention) {
                        row[index + 1] = `${foundIntention.size}${foundIntention.qty > 1 ? ' (x'+foundIntention.qty+')' : ''}`; 
                    }
                });
                return row;
            });
        } 
        // On ajoute toujours des lignes vides pour l'écriture manuelle lors des essayages
        const emptyLinesCount = Math.max(0, 20 - respondents.length);
        for(let k=0; k<emptyLinesCount; k++) {
            body.push(Array(head[0].length).fill(''));
        }

        doc.autoTable({
            startY: 30,
            head: head,
            body: body,
            theme: 'grid',
            styles: { fontSize: 8, lineColor: [200, 200, 200], lineWidth: 0.1, valign: 'middle', halign: 'center', cellPadding: 1 },
            headStyles: { fillColor: [31, 41, 55], textColor: 255, fontSize: 7, minCellHeight: 55, valign: 'bottom' },
            columnStyles: { 
                0: { cellWidth: 45, halign: 'left', fontStyle: 'bold' },
                [head[0].length - 1]: { cellWidth: 30 }
            },
            didDrawCell: function(data) {
                if (data.section === 'head' && data.column.index > 0 && data.column.index < head[0].length - 1) {
                    const doc = data.doc;
                    const rawText = data.cell.raw;
                    doc.setFillColor(31, 41, 55);
                    doc.rect(data.cell.x, data.cell.y, data.cell.width, data.cell.height, 'F');
                    doc.setTextColor(255, 255, 255);
                    doc.setFontSize(7);
                    const maxTextWidth = data.cell.height - 6;
                    const textLines = doc.splitTextToSize(rawText, maxTextWidth);
                    const lineHeight = 2.5; 
                    const xCenter = data.cell.x + (data.cell.width / 2);
                    const x = xCenter - ((textLines.length - 1) * lineHeight) / 2;
                    const y = data.cell.y + data.cell.height - 3; 
                    doc.text(textLines, x, y, { angle: 90, align: 'left' });
                    return false;
                }
            },
            didParseCell: function(data) {
                if (data.section === 'body' && data.column.index > 0 && data.cell.raw !== '') {
                    data.cell.styles.fillColor = [240, 245, 255];
                    data.cell.styles.fontStyle = 'bold';
                    data.cell.styles.textColor = [79, 70, 229];
                }
            }
        });

        doc.setFontSize(8); doc.setTextColor(150);
        doc.text("Document généré pour la gestion des essayages et recueil des besoins.", margin, doc.internal.pageSize.height - 10);
        window.open(doc.output('bloburl'), '_blank');
    };

    const loadPoll = (id) => {
        const found = state.savedPolls.find(p => p.id == id);
        if (found) {
            pollState = JSON.parse(JSON.stringify(found));
            pollState.clubName = state.clubName; 
            renderPollUI(document.getElementById('poll-ui-container'));
            showToast(`Campagne "${pollState.name}" chargée.`);
        }
    };

    const deletePoll = () => {
        if(confirm("Supprimer définitivement cette campagne ?")) {
            state.savedPolls = state.savedPolls.filter(p => p.id !== pollState.id);
            localStorage.setItem('savedPolls', JSON.stringify(state.savedPolls));
            pollState = { id: Date.now(), clubName: state.clubName, name: "Nouvelle Campagne", selectedProducts: state.clubProductRange.map(i=>i.name), intentions: [], minGoal: 10, deadline: '', hasFitting: false, fittingDates: '' };
            renderPollUI(document.getElementById('poll-ui-container'));
        }
    };

    const renderPollUI = (container) => {
        if (!container) return; 
        const totalIntentions = pollState.intentions.reduce((sum, i) => sum + i.qty, 0);
        let displayDate = pollState.deadline ? pollState.deadline.split('-').reverse().join('/') : "[DATE]";
        let fittingText = pollState.hasFitting ? `Des séances d'essayage sont prévues aux dates suivantes : ${pollState.fittingDates || '[DATES À PRÉCISER]'}.\n\n` : "";
        
        const productsListText = pollState.selectedProducts.map(pName => {
            const sizes = getProductSizesArray(pName);
            return `- ${pName} [Tailles: ${sizes.join(', ')}]`;
        }).join('\n');

        // --- NOUVEAU TEXTE EMAIL SANS LIEN ---
        const emailBody = `Bonjour,\n\nLe club (${state.clubName}) envisage de passer une nouvelle commande.\n\n${fittingText}⚠️ IMPORTANT : En cas de commande sans essayage préalable, veuillez noter que les articles ne seront ni repris, ni échangés.\n\nVoici les articles proposés :\n${productsListText}\n\nMerci de nous répondre directement par mail avec vos souhaits (Article + Taille + Quantité) avant le ${displayDate}.\n\nCordialement,\nLe Bureau.`;
        // -------------------------------------

        const relevantPolls = state.savedPolls.filter(p => p.clubName === state.clubName);
        const savedOptions = relevantPolls.map(p => `<option value="${p.id}" ${p.id === pollState.id ? 'selected' : ''}>${p.name.startsWith('[TRAITÉ]') ? '✅' : '📁'} ${p.name}</option>`).join('');

        let firstProductSizes = [];
        if (pollState.selectedProducts.length > 0) {
            firstProductSizes = getProductSizesArray(pollState.selectedProducts[0]);
        }

        container.innerHTML = `
            <div class="flex flex-col h-[82vh]">
                <div class="bg-indigo-100 p-3 rounded-lg border border-indigo-200 mb-3 flex flex-wrap gap-3 items-center justify-between">
                    <div class="flex items-center gap-2 flex-grow">
                        <label class="text-xs font-bold text-indigo-900 uppercase">Campagne :</label>
                        <input type="text" id="poll-name-input" value="${pollState.name}" class="border border-indigo-300 rounded px-2 py-1 text-sm font-bold text-indigo-900 w-64">
                    </div>
                    <div class="flex items-center gap-2">
                         <select id="load-poll-select" class="text-xs border-indigo-300 rounded py-1 pl-2 pr-8 bg-white max-w-[180px]">
                            <option value="">-- Charger (${relevantPolls.length}) --</option>
                            ${savedOptions}
                        </select>
                        <button id="btn-save-poll" class="bg-indigo-600 text-white px-3 py-1 rounded text-xs font-bold shadow hover:bg-indigo-700">💾 Save</button>
                        <button id="btn-print-sheet" class="bg-teal-600 text-white px-3 py-1 rounded text-xs font-bold shadow hover:bg-teal-700" title="Imprimer feuille d'émargement pour essayage">📄 Imprimer PDF</button>
                        <button id="btn-new-poll" class="bg-white border border-indigo-300 text-indigo-700 px-3 py-1 rounded text-xs font-bold hover:bg-indigo-50">➕</button>
                        <button id="btn-delete-poll" class="bg-red-100 text-red-600 border border-red-200 px-3 py-1 rounded text-xs font-bold hover:bg-red-200">🗑️</button>
                    </div>
                </div>

                <div class="flex gap-4 flex-1 overflow-hidden">
                    <div class="w-2/5 flex flex-col gap-3 overflow-y-auto pr-2 border-r">
                        <div class="bg-blue-50 p-3 rounded border border-blue-100 shadow-sm">
                            <h4 class="font-bold text-blue-900 text-xs mb-3 border-b border-blue-200 pb-1 uppercase text-center">Préparation Communication</h4>
                            <div class="grid grid-cols-2 gap-3 mb-3">
                                <div>
                                    <label class="block text-[10px] font-bold text-blue-800 uppercase">Date limite :</label>
                                    <input type="date" id="poll-deadline-input" value="${pollState.deadline}" class="w-full text-xs border-blue-300 rounded p-1.5">
                                </div>
                                <div class="flex flex-col justify-end">
                                    <div class="flex items-center gap-2 mb-1">
                                        <input type="checkbox" id="poll-has-fitting" ${pollState.hasFitting ? 'checked' : ''} class="h-4 w-4 text-blue-600 rounded">
                                        <label for="poll-has-fitting" class="text-[10px] font-bold text-blue-800 cursor-pointer uppercase">Essayage prévu</label>
                                    </div>
                                </div>
                            </div>
                            <div class="${pollState.hasFitting ? '' : 'hidden'} mb-3">
                                <label class="block text-[10px] font-bold text-blue-800 uppercase">Dates / Lieux :</label>
                                <input type="text" id="poll-fitting-dates" value="${pollState.fittingDates}" placeholder="Ex: Samedi au club" class="w-full text-xs border-blue-300 rounded p-1.5 bg-white">
                            </div>
                            <textarea id="poll-email-template" class="w-full text-[10px] p-2 border border-blue-200 rounded h-32 bg-white font-mono" readonly>${emailBody}</textarea>
                            <div class="grid grid-cols-2 gap-2 mt-2">
                                <button id="btn-open-mailto" class="col-span-2 bg-blue-600 text-white text-xs font-bold py-2 rounded shadow hover:bg-blue-700 flex items-center justify-center gap-2"><span>✉️</span> Préparer Mail</button>
                                <button id="btn-copy-mail" class="bg-white border border-blue-300 text-blue-700 text-xs py-1 rounded">Copier Texte</button>
                                <button id="btn-copy-emails" class="bg-white border border-blue-300 text-blue-700 text-xs py-1 rounded">Copier Emails</button>
                            </div>
                        </div>
                        <div class="bg-white p-3 rounded border border-gray-200 flex-1 overflow-hidden flex flex-col shadow-sm">
                            <h4 class="font-bold text-gray-800 text-[10px] uppercase mb-2">Sélection Articles proposés :</h4>
                            <div class="overflow-y-auto text-[10px] flex-1">
                                ${allAvailableProducts.filter(p => state.clubProductRange.some(r => r.name === p.name)).map(p => `
                                    <div class="flex items-center mb-1">
                                        <input type="checkbox" class="poll-prod-check" value="${p.name}" ${pollState.selectedProducts.includes(p.name) ? 'checked' : ''}>
                                        <label class="ml-2 truncate cursor-pointer">${p.name}</label>
                                    </div>
                                `).join('')}
                            </div>
                        </div>
                    </div>

                    <div class="w-3/5 flex flex-col pl-2">
                        <div class="flex justify-between items-end mb-2 px-1">
                            <h4 class="font-bold text-gray-800 text-sm italic">Saisie des retours adhérents</h4>
                            <div class="text-[10px] font-bold text-gray-500 uppercase">Total: ${totalIntentions} pièces</div>
                        </div>
                        <div class="flex gap-1 mb-4 items-end bg-gray-100 p-2 rounded shadow-inner">
                            <div class="flex-1">
                                <label class="text-[9px] font-bold uppercase text-gray-400">Licencié</label>
                                <input type="text" id="poll-input-name" list="licensee-datalist" class="w-full text-xs border rounded p-1">
                            </div>
                            <div class="flex-[1.5]">
                                <label class="text-[9px] font-bold uppercase text-gray-400">Article</label>
                                <select id="poll-input-product" class="w-full text-xs border rounded p-1">
                                    ${pollState.selectedProducts.map(p => `<option value="${p}">${p}</option>`).join('')}
                                </select>
                            </div>
                            <div class="w-20">
                                <label class="text-[9px] font-bold uppercase text-gray-400">Taille</label>
                                <select id="poll-input-size" class="w-full text-xs border rounded p-1 text-center font-bold text-indigo-700">
                                    ${firstProductSizes.map(s => `<option value="${s}">${s}</option>`).join('')}
                                </select>
                            </div>
                             <div class="w-10">
                                <label class="text-[9px] font-bold uppercase text-gray-400">Qté</label>
                                <input type="number" id="poll-input-qty" value="1" class="w-full text-xs border rounded p-1 text-center font-bold">
                            </div>
                            <button id="poll-add-intention-btn" class="bg-indigo-600 text-white px-3 py-1 rounded text-xs font-bold shadow hover:bg-indigo-700">AJOUT</button>
                        </div>
                        <div class="flex-1 overflow-y-auto border rounded bg-white shadow-sm">
                            <table class="w-full text-[11px] text-left border-collapse">
                                <thead class="bg-gray-50 sticky top-0 z-10 border-b">
                                    <tr><th class="p-2">Licencié</th><th class="p-2">Article</th><th class="p-2 text-center">Taille</th><th class="p-2 text-center">Qté</th><th class="p-2"></th></tr>
                                </thead>
                                <tbody>
                                    ${pollState.intentions.length === 0 ? '<tr><td colspan="5" class="p-4 text-center text-gray-400 italic">En attente de saisie manuelle...</td></tr>' : ''}
                                    ${pollState.intentions.map((intent, idx) => `
                                        <tr class="border-b hover:bg-gray-50"><td class="p-2 font-bold">${intent.name}</td><td class="p-2 text-gray-600">${intent.product}</td><td class="p-2 text-center bg-gray-50 font-medium">${intent.size}</td><td class="p-2 text-center font-bold text-indigo-700">${intent.qty}</td><td class="p-2 text-right"><button data-idx="${idx}" class="poll-delete-btn text-red-400 hover:text-red-600">×</button></td></tr>
                                    `).join('')}
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </div>
        `;

        const getEl = (id) => container.querySelector(id);
        getEl('#poll-name-input').oninput = (e) => pollState.name = e.target.value;
        getEl('#btn-save-poll').onclick = () => persistPolls(false);
        // getEl('#btn-export-json').onclick = exportPollJson; // Si vous voulez garder l'export pour backup
        getEl('#btn-print-sheet').onclick = printIntentionsSheet;
        getEl('#btn-delete-poll').onclick = deletePoll;
        getEl('#btn-new-poll').onclick = () => { pollState = { id: Date.now(), clubName: state.clubName, name: "Nouvelle Campagne", selectedProducts: state.clubProductRange.map(i=>i.name), intentions: [], minGoal: 10, deadline: '', hasFitting: false, fittingDates: '' }; renderPollUI(container); };
        getEl('#load-poll-select').onchange = (e) => { if (e.target.value) loadPoll(e.target.value); };
        getEl('#poll-deadline-input').onchange = (e) => { pollState.deadline = e.target.value; renderPollUI(container); };
        getEl('#poll-has-fitting').onchange = (e) => { pollState.hasFitting = e.target.checked; renderPollUI(container); };
        if(getEl('#poll-fitting-dates')) getEl('#poll-fitting-dates').oninput = (e) => pollState.fittingDates = e.target.value;
        
        container.querySelectorAll('.poll-prod-check').forEach(cb => {
            cb.onchange = () => {
                pollState.selectedProducts = Array.from(container.querySelectorAll('.poll-prod-check:checked')).map(c => c.value);
                renderPollUI(container); 
            };
        });

        getEl('#poll-input-product').onchange = (e) => {
            const productName = e.target.value;
            const sizes = getProductSizesArray(productName);
            const sizeSelect = getEl('#poll-input-size');
            sizeSelect.innerHTML = sizes.map(s => `<option value="${s}">${s}</option>`).join('');
        };

        getEl('#btn-open-mailto').onclick = () => {
            const emails = Object.values(state.licenseeEmails).filter(e => e && e.includes('@'));
            if (emails.length === 0) return showToast("Aucun email licencié enregistré.", "warning");
            window.location.href = `mailto:?bcc=${emails.join(';')}&subject=${encodeURIComponent("Sondage Commande - " + state.clubName)}&body=${encodeURIComponent(getEl('#poll-email-template').value)}`;
        };

        getEl('#btn-copy-mail').onclick = () => { getEl('#poll-email-template').select(); document.execCommand('copy'); showToast("Copié !"); };
        getEl('#btn-copy-emails').onclick = () => { navigator.clipboard.writeText(Object.values(state.licenseeEmails).join('; ')); showToast("Emails copiés !"); };

        getEl('#poll-add-intention-btn').onclick = () => {
            const name = getEl('#poll-input-name').value.trim();
            const prod = getEl('#poll-input-product').value;
            const size = getEl('#poll-input-size').value; 
            const qty = parseInt(getEl('#poll-input-qty').value) || 1;
            if(name && prod && qty > 0) {
                pollState.intentions.push({ name, product: prod, size, qty });
                renderPollUI(container);
                setTimeout(() => getEl('#poll-input-name').focus(), 50);
            }
        };

        container.querySelectorAll('.poll-delete-btn').forEach(btn => {
            btn.onclick = (e) => { pollState.intentions.splice(parseInt(e.target.dataset.idx), 1); renderPollUI(container); };
        });
    };

    const container = document.createElement('div');
    container.id = 'poll-ui-container'; 
    renderPollUI(container);

    showModal(dom.mainModal, "Gestion des Intentions de Commande", container, [
        { label: 'Fermer', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-400' },
        { 
            label: '🚀 Transformer en Commande', 
            className: 'bg-green-600 text-white font-bold', 
            onClick: () => {
                if(pollState.intentions.length === 0) return showToast("Rien à convertir.", "error");
                
                if(confirm(`Transférer les ${pollState.intentions.length} intentions dans le panier ?\n\nLe système vérifiera le stock et CALCULERA LES PRIX automatiquement.`)) {
                    let transferredCount = 0;
                    let stockHitCount = 0;
                    let tempStock = JSON.parse(JSON.stringify(state.clubStock));

                    pollState.intentions.forEach(intent => {
                        const cleanProductName = intent.product.trim();
                        const product = productMap.get(cleanProductName);
                        if (!product) return;

                        let qtyNeeded = intent.qty;
                        let qtyFromStock = 0;

                        if (tempStock[cleanProductName] && tempStock[cleanProductName][intent.size]) {
                            const available = tempStock[cleanProductName][intent.size];
                            qtyFromStock = Math.min(qtyNeeded, available);
                            tempStock[cleanProductName][intent.size] -= qtyFromStock;
                        }
                        
                        let discountRateToApply = undefined; 
                        const rangeItem = state.clubProductRange.find(r => r.name === cleanProductName);
                        if (state.applyDiscount && state.discountType === 'item' && rangeItem && rangeItem.allowDiscount) {
                            discountRateToApply = state.clubDiscount;
                        }

                        if (qtyFromStock > 0) {
                            state.currentOrderLineItems.push({
                                id: Date.now() + Math.random(),
                                productName: cleanProductName,
                                licencieName: intent.name,
                                quantitiesPerSize: { [intent.size]: qtyFromStock },
                                totalQuantity: qtyFromStock,
                                options: [],
                                specificity: `Sondage (Stock): ${pollState.name}`,
                                isFromStock: true,
                                isStockOrder: false,
                                type: 'haut',
                                unitPriceTTC: 0, totalPriceTTC: 0, unitPriceHT: 0, totalPriceHT: 0
                            });
                            stockHitCount++;
                        }

                        const qtyProduction = qtyNeeded - qtyFromStock;
                        if (qtyProduction > 0) {
                            state.currentOrderLineItems.push({
                                id: Date.now() + Math.random(),
                                productName: cleanProductName,
                                licencieName: intent.name,
                                quantitiesPerSize: { [intent.size]: qtyProduction },
                                totalQuantity: qtyProduction,
                                options: [],
                                specificity: `Sondage: ${pollState.name}`,
                                isFromStock: false,
                                isStockOrder: false,
                                type: 'haut',
                                specificDiscountRate: discountRateToApply, 
                                applyDiscount: true,
                                unitPriceTTC: 0, totalPriceTTC: 0, unitPriceHT: 0, totalPriceHT: 0
                            });
                        }

                        if (!state.licenseeList.includes(intent.name)) {
                            state.licenseeList.push(intent.name);
                        }
                        transferredCount++;
                    });

                    if (!pollState.name.includes('[TRAITÉ]')) {
                        pollState.name = `[TRAITÉ] ${pollState.name}`;
                        persistPolls(true);
                    }
                    state.orderScope = 'licensee';
                    renderAll();
                    hideModal(dom.mainModal);
                    let msg = `${transferredCount} retours traités.`;
                    if(stockHitCount > 0) msg += `\n📦 ${stockHitCount} lignes prises sur le STOCK.`;
                    showToast(msg, "success");
                }
            }
        }
    ], 'max-w-6xl');
};// =========================================================
// --- LOGIQUE MODE PUBLIC (BOUTIQUE ADHÉRENT) ---
// =========================================================

let publicCart = []; // Panier local de l'adhérent
let activePollId = null;

window.initPublicMode = async (clubCode, pollId) => {
    // 1. Masquer l'appli admin et le login
    document.getElementById('login-overlay').classList.add('hidden');
    document.getElementById('main-app-view').classList.add('hidden');
    document.getElementById('public-shop-view').classList.remove('hidden');

    // 2. Connexion Firebase "Silencieuse"
    currentClubCode = clubCode;
    const clubRef = ref(db, 'clubs/' + clubCode);
    
    try {
        const snapshot = await get(clubRef);
        if (!snapshot.exists()) {
            alert("Ce lien n'est plus valide (Code Club inconnu).");
            return;
        }
        
        const data = snapshot.val();
if (data.licenseeList && Array.isArray(data.licenseeList)) {
        const datalist = document.getElementById('shop-licensee-list');
        if (datalist) {
            // On trie les noms par ordre alphabétique pour faciliter la recherche
            datalist.innerHTML = data.licenseeList.sort().map(name => `<option value="${name}">`).join('');
        }
    }
        
        // Sécurité : Si accès bloqué
        if (data.isAccessBlocked) {
            document.body.innerHTML = '<div class="flex h-screen items-center justify-center bg-gray-100 text-red-600 font-bold">Cette boutique est fermée.</div>';
            return;
        }

        // 3. Charger le sondage actif
        state.clubProductRange = data.clubProductRange || [];
        const polls = data.savedPolls || [];
        
        // Si un ID précis est fourni dans l'URL, on le cherche, sinon on prend le dernier
        const poll = pollId ? polls.find(p => p.id == pollId) : polls[polls.length - 1];

        if (!poll) {
            alert("Aucune campagne de commande n'est active pour le moment.");
            return;
        }

        activePollId = poll.id; // Mémoriser l'ID pour la sauvegarde
        renderPublicShop(data.clubName, poll);

    } catch (e) {
        console.error(e);
        alert("Erreur de chargement boutique.");
    }
};

const renderPublicShop = (clubName, poll) => {
    document.getElementById('shop-club-name').textContent = clubName;
    
    if (poll.deadline) {
        document.getElementById('shop-deadline-display').textContent = `⏳ Date limite : ${poll.deadline.split('-').reverse().join('/')}`;
    }

    const grid = document.getElementById('shop-products-grid');
    grid.innerHTML = '';

    // Filtrer les produits du sondage
    const productsToShow = allAvailableProducts.filter(p => poll.selectedProducts.includes(p.name));

    if (productsToShow.length === 0) {
        grid.innerHTML = '<p class="col-span-full text-center text-gray-500">Aucun article disponible.</p>';
        return;
    }

    productsToShow.forEach(product => {
        // Chercher si un visuel est défini dans la gamme club pour ce produit
        // (Version simplifiée : on prend le nom)
        
        const sizes = productSizes[product.sizeType || product.type] || ['U'];
        const sizeOptions = sizes.map(s => `<option value="${s}">${s}</option>`).join('');

        const card = document.createElement('div');
        card.className = "bg-white p-4 rounded-lg shadow-sm border border-gray-200 flex flex-col justify-between h-full";
        card.innerHTML = `
            <div>
                <h4 class="font-bold text-gray-800 text-sm mb-1">${product.name}</h4>
                <p class="text-xs text-gray-500 mb-3">${product.category} > ${product.subtype}</p>
            </div>
            
            <div class="mt-4 space-y-2 bg-gray-50 p-2 rounded">
                <div class="flex gap-2">
                    <select class="shop-size-select w-2/3 text-sm border-gray-300 rounded p-1">
                        ${sizeOptions}
                    </select>
                    <input type="number" value="1" min="1" class="shop-qty-input w-1/3 text-sm border-gray-300 rounded p-1 text-center">
                </div>
                <button onclick="addToPublicCart('${product.name}')" class="w-full bg-indigo-100 text-indigo-700 hover:bg-indigo-200 font-bold py-2 rounded text-xs transition">
                    AJOUTER AU PANIER
                </button>
            </div>
        `;
        // Pour pouvoir récupérer les inputs facilement lors du clic
        card.querySelector('button').onclick = function() {
            const size = card.querySelector('.shop-size-select').value;
            const qty = parseInt(card.querySelector('.shop-qty-input').value) || 1;
            addToPublicCart(product.name, size, qty);
        };
        
        grid.appendChild(card);
    });
};

window.addToPublicCart = (productName, size, qty) => {
    publicCart.push({ productName, size, qty });
    
    // Animation visuelle simple
    const btn = document.getElementById('shop-cart-count');
    btn.textContent = `${publicCart.length} article(s)`;
    btn.classList.add('scale-125');
    setTimeout(() => btn.classList.remove('scale-125'), 200);
    
    showToast(`Ajouté : ${productName} (${size}) x${qty}`);
};
// --- GESTION DU PANIER PUBLIC ---

window.openPublicCart = () => {
    const modal = document.getElementById('public-cart-modal');
    const list = document.getElementById('public-cart-list');
    const totalQtyEl = document.getElementById('public-cart-total-qty');
    
    if (publicCart.length === 0) {
        list.innerHTML = `
            <div class="text-center py-8 text-gray-400">
                <p class="text-4xl mb-2">🛒</p>
                <p>Votre panier est vide.</p>
            </div>`;
        totalQtyEl.textContent = "0";
    } else {
        list.innerHTML = '';
        let totalQty = 0;
        
        publicCart.forEach((item, index) => {
            totalQty += item.qty;
            
            const itemDiv = document.createElement('div');
            itemDiv.className = "bg-white p-3 rounded-lg border border-gray-200 shadow-sm flex justify-between items-center gap-3";
            itemDiv.innerHTML = `
                <div class="flex-1 min-w-0">
                    <p class="font-bold text-gray-800 text-sm truncate">${item.productName}</p>
                    <p class="text-xs text-gray-500">Taille : <strong class="text-indigo-600">${item.size}</strong></p>
                </div>
                <div class="flex items-center gap-3">
                    <span class="bg-gray-100 text-gray-800 text-xs font-bold px-2 py-1 rounded">x${item.qty}</span>
                    <button onclick="removePublicItem(${index})" class="text-red-500 hover:text-red-700 hover:bg-red-50 p-1.5 rounded-md transition" title="Supprimer">
                        🗑️
                    </button>
                </div>
            `;
            list.appendChild(itemDiv);
        });
        
        totalQtyEl.textContent = totalQty;
    }
    
    modal.classList.remove('hidden');
};

window.closePublicCart = () => {
    document.getElementById('public-cart-modal').classList.add('hidden');
};

window.removePublicItem = (index) => {
    // Supprime l'élément du tableau
    publicCart.splice(index, 1);
    
    // Met à jour le compteur du header
    const btn = document.getElementById('shop-cart-count');
    btn.textContent = `🛒 ${publicCart.length} article(s)`;
    
    // Rafraîchit la vue du panier (ré-ouvre la modale mise à jour)
    openPublicCart();
    
    // Feedback visuel
    showToast("Article supprimé du panier", "info");
};

window.submitPublicOrder = async () => {
    const name = document.getElementById('shop-member-name').value.trim();
    const email = document.getElementById('shop-member-email').value.trim();

    if (!name) { alert("Merci d'indiquer votre Nom et Prénom."); return; }
    if (publicCart.length === 0) { alert("Votre panier est vide."); return; }

    if (!confirm(`Confirmer l'envoi de vos choix (${publicCart.length} articles) ?`)) return;

    try {
        // 1. Recharger les données fraîches pour éviter les conflits
        const clubRef = ref(db, 'clubs/' + currentClubCode);
        const snapshot = await get(clubRef);
        const data = snapshot.val();
        
        let polls = data.savedPolls || [];
        const pollIndex = polls.findIndex(p => p.id === activePollId);
        
        if (pollIndex === -1) { alert("Ce sondage n'existe plus."); return; }

        // 2. Ajouter les intentions
        const poll = polls[pollIndex];
        if (!poll.intentions) poll.intentions = [];

        publicCart.forEach(item => {
            poll.intentions.push({
                name: name,
                product: item.productName,
                size: item.size,
                qty: item.qty,
                date: new Date().toISOString(),
                email: email
            });
        });

        // 3. Sauvegarder dans Firebase
        polls[pollIndex] = poll;
        await update(clubRef, { savedPolls: polls });

        // 4. Feedback et Reset
        alert("✅ Merci ! Vos choix ont été bien enregistrés.");
        publicCart = [];
        document.getElementById('shop-cart-count').textContent = "0 article(s)";
        document.getElementById('shop-member-name').value = "";
        
        // Optionnel : Désactiver le bouton pour éviter double envoi
        // location.reload(); 

    } catch (e) {
        console.error(e);
        alert("Erreur lors de l'envoi. Vérifiez votre connexion.");
    }
};
const init = () => {
    // 1. VERIFICATION URL POUR MODE PUBLIC
    const params = new URLSearchParams(window.location.search);
    const urlClubCode = params.get('club');
    const urlPollId = params.get('poll');

    if (urlClubCode) {
        // Si il y a ?club=CODE dans l'URL, on lance le mode public
        window.initPublicMode(urlClubCode.toUpperCase(), urlPollId);
        return; // ON ARRÊTE LE CHARGEMENT ADMIN ICI
    }
initializeAppView();
      dom.orderSpecificity.addEventListener('focus', () => {
        // On vérifie dans la mémoire du navigateur si l'info n'a pas déjà été montrée
        if (!localStorage.getItem('specificityInfoShown')) {
            const content = document.createElement('p');
            content.className = 'text-sm';
            content.textContent = "Dans ce champ, vous pouvez ajouter des informations complémentaires utiles pour le traitement de votre commande (par exemple : une demande de livraison spécifique, des détails pour la facturation, des contraintes particulières, etc.).";
            
            showModal(dom.mainModal, "Information : Spécificité Commande", content, [
                { 
                    label: "J'ai compris", 
                    onClick: () => {
                        hideModal(dom.mainModal);
                        // On remet le focus sur le champ pour que l'utilisateur puisse écrire
                        dom.orderSpecificity.focus();
                    } 
                }
            ]);
            
            // On enregistre de manière permanente que l'info a été montrée
            localStorage.setItem('specificityInfoShown', 'true'); 
        }
    });
    document.body.addEventListener('input', (e) => {
        const { id, value } = e.target;
        if (id === 'licencieName') { 
            state.licencieName = value; 
            if (state.activeLicensee && value.trim() !== state.activeLicensee) { 
                state.activeLicensee = ''; 
                renderUIState(); 
            }
            scrollToLicensee(value);
        }
if (id === 'clubName') {
                  state.clubName = value;
                  // Réinitialise les champs dépendants si le nom du club est effacé
                  if (value.trim() === '') {
                      dom.departement.value = state.departement = '';
                      dom.clientNumber.value = state.clientNumber = '';
                      state.clubProductRange = []; // Vide la gamme
                      state.clubPacks = []; // Vide les packs
                      state.clubStock = {}; // Vide le stock
                      state.clubVisuals = []; // Vide les visuels
                      renderAll(); // Rafraîchit l'état vidé
                  } else {
                      // Cherche le club dans la base de données locale
                      const found = clientDatabase.find(c => c.clubName === value);
                      if (found) {
                          // Si trouvé, pré-remplit les champs
                          dom.clubName.value = state.clubName = found.clubName;
                          dom.departement.value = state.departement = found.departement || '';
                          dom.clientNumber.value = state.clientNumber = found.clientNumber || '';
        
                          // --- CHARGEMENT ET VALIDATION/CONVERSION DE clubProductRange ---
                          const rangeKey = `range_${state.clubName.replace(/[\s/\\?%*:|"<>]/g, '_')}`;
                          const savedRange = localStorage.getItem(rangeKey);
                          let loadedRange = [];
                          let rangeWarning = null;
                          if (savedRange) {
                              try {
                                  const parsedRange = JSON.parse(savedRange);
                                  if (Array.isArray(parsedRange)) {
                                       if (parsedRange.length > 0 && typeof parsedRange[0] === 'string') {
                                           // Convertir l'ancien format (tableau de strings) vers le nouveau format (tableau d'objets)
                                           console.warn("Chargement ancien format gamme depuis localStorage. Conversion...");
                                           rangeWarning = "Ancien format de gamme chargé, prix licenciés initialisés à nul.";
                                           loadedRange = parsedRange.map(name => ({ name: name, licenseePrice: null }));
                                       } else if (parsedRange.length > 0 && typeof parsedRange[0] === 'object') {
                                           // Valider la structure des objets existants
                                            const originalLength = parsedRange.length; // Stocke la longueur originale pour comparaison
                                           loadedRange = parsedRange.map(item => {
                                                if (item && typeof item === 'object' && item.name) { // Vérifie que l'item est un objet et a un 'name'
                                                     return {
     name: String(item.name),
     licenseePrice: (typeof item.licenseePrice === 'number' && item.licenseePrice >= 0) ? item.licenseePrice : null,
     defaultOptions: Array.isArray(item.defaultOptions) ? item.defaultOptions : [], // AJOUT
     markingType: item.markingType || null // AJOUT
};
                                                } else {
                                                     console.warn("Item de gamme importé invalide (manque 'name' ou n'est pas un objet):", item);
                                                     return null; // Marque cet item comme invalide
                                                }
                                           }).filter(item => item !== null); // Retire les items marqués comme invalides
        
                                           // Vérifie si des items ont été retirés pendant la validation
                                           if(loadedRange.length < originalLength) {
                                                rangeWarning = "Certains articles de la gamme chargée étaient invalides (nom manquant?) et ont été ignorés.";
                                           }
                                       }
                                       // Si le tableau est vide ou déjà au bon format d'objets, loadedRange sera correct
                                       else {
                                          loadedRange = parsedRange; // Suppose un tableau vide correct ou un tableau d'objets
                                       }
                                  } else {
                                       console.warn("Gamme sauvegardée n'est pas un tableau:", parsedRange);
                                       rangeWarning = "La gamme sauvegardée semble corrompue.";
                                       loadedRange = []; // Réinitialise en cas de type invalide
                                  }
                              } catch (e) {
                                  console.error("Erreur parsing gamme depuis localStorage:", e);
                                  rangeWarning = "Erreur lors du chargement de la gamme sauvegardée.";
                                  loadedRange = []; // Réinitialise en cas d'erreur
                              }
                          }
                          state.clubProductRange = loadedRange; // Assigne la gamme validée/convertie
                          if (rangeWarning) {
                              showToast(rangeWarning, 'warning'); // Affiche un avertissement si nécessaire
                          }
                          // --- FIN CHARGEMENT ET VALIDATION ---
        
                          loadPacks(); // Charge les packs de ce club
                          loadStock(); // Charge le stock de ce club
                          // loadVisuals(); // Décommentez si vous avez une fonction pour charger les visuels par club
        
                          state.showAllProducts = false; // S'assure que le filtre de gamme est actif
                          renderAll(); // Rafraîchit l'UI avec toutes les nouvelles données
                      } else {
                           // Si le club n'est pas trouvé, réinitialise les champs liés au club
                           state.departement = '';
                           dom.departement.value = ''; // Met aussi à jour l'UI
                           state.clientNumber = '';
                           dom.clientNumber.value = ''; // Met aussi à jour l'UI
                           state.clubProductRange = [];
                           state.clubPacks = [];
                           state.clubStock = {};
                           state.clubVisuals = [];
                           renderAll(); // Rafraîchit pour montrer l'état "nouveau club"
                      }
                  }
              }
               if (id === 'clientNumber') {
            state.clientNumber = value;
        }
    
        if (id === 'preOrderNumber') state.preOrderNumber = value;
        if (id === 'deliveryAddress') state.deliveryAddress = value;
        if (id === 'deliveryContact') state.deliveryContact = value;
if (id === 'deliveryEmail') state.deliveryEmail = value;
        if (id === 'orderSpecificity') state.orderSpecificity = value;

        updateButtonStates();
    });

    document.body.addEventListener('focusout', (e) => {
        if (['clubName', 'clientNumber', 'departement'].includes(e.target.id)) saveClientInfo();
    });

   document.body.addEventListener('change', (e) => {
        const target = e.target;
        const { id, name, value, checked, classList, dataset } = e.target;
        if (id === 'departement') { state.departement = value; renderAll(); }
        if (id === 'orderDate') { state.orderDate = value; renderAll(); }
        if (id === 'clubDiscount') { state.clubDiscount = parseFloat(value) || 0;
refreshExistingCartDiscounts();
 renderAll(); }
        if (id === 'portalDeadline') state.portalDeadline = value;
        if (id === 'factoryDepartureDate') state.factoryDepartureDate = value;
        if (id === 'apply-participation-check') {
            state.applyClubParticipation = checked;
            if (!checked) {
                state.clubParticipationAmount = 0;
            }
            renderAll();
        }
        if (id === 'clubParticipationAmount') {
            state.clubParticipationAmount = parseFloat(value) || 0;
            renderAll();
        }
        if (id === 'doc-type-reassort') {
            if (checked) {
                showReassortInfoModal();
            } else {
                state.isReassort = false;
                state.lastDeliveryDate = '';
                renderAll();
            }
        }
        if (id === 'custom-creation-check') {
            state.isCustomCreation = checked;
            renderAll();

            if (checked) {
                const content = document.createElement('div');
                content.innerHTML = `
                    <p class="text-sm">Vous avez sélectionné "Création Personnalisée". Les règles suivantes s'appliquent :</p>
                    <ul class="list-disc list-inside mt-3 space-y-2 text-sm">
                        <li>Applicable pour les commandes nécessitant une nouvelle création de maquette.</li>
                        <li>Un minimum de commande de <strong>10 pièces</strong> (hors accessoires) est requis.</li>
                        <li>Le <strong>forfait de création graphique</strong> s'applique automatiquement pour les commandes de <strong>moins de 20 pièces</strong> (hors accessoires).</li>
                    </ul>
                `;
                showModal(dom.mainModal, "Information : Commande Création Personnalisée", content, [
                    { label: "J'ai compris", onClick: () => hideModal(dom.mainModal) }
                ]);
            }
        }
        // ▼▼▼ DÉBUT DU BLOC MODIFIÉ ▼▼▼
   // ▼▼▼ DÉBUT DU BLOC MODIFIÉ (SÉCURITÉ PRÉCOMMANDE) ▼▼▼
     if (name === 'scope') {
            // Si le numéro est déjà défini, on laisse passer (changement de mode en cours de route)
            if (state.preOrderNumber && state.preOrderNumber.trim() !== '') {
                state.orderScope = value;
                renderAll();
                if (value === 'licensee') showModal(dom.mainModal, "Info", document.createElement('div').innerHTML = "<p>Mode Saisie par Licencié activé.</p>", [{ label: "OK", onClick: () => hideModal(dom.mainModal) }]);
                return;
            }

            // Sinon, on bloque et on demande le numéro
            e.target.checked = false; // On décoche visuellement
            
            // On remet les boutons à l'état initial
            document.getElementById('scope-global').checked = false;
            document.getElementById('scope-licensee').checked = false;
            
            // On lance la modale de connexion à la commande
            promptForPreOrderNumber(value); // 'value' est le mode demandé (global ou licensee)
        }
        // ▲▲▲ FIN DU BLOC MODIFIÉ ▲▲▲
        // ▲▲▲ FIN DU BLOC MODIFIÉ ▲▲▲
        if (id === 'store-order-check') state.isStoreOrder = checked;
        if (id === 'apply-discount-check') { state.applyDiscount = checked; if (!checked) state.clubDiscount = 0; renderAll(); }
        if (name === 'discount-type') { state.discountType = value;
refreshExistingCartDiscounts();
 renderAll(); }
    if (id === 'current-product-select') {
            resetProductForm();
            state.currentProduct = value;
            
            if (state.currentProduct) {
                const rangeItem = state.clubProductRange.find(r => r.name === state.currentProduct);
                
                // Options par défaut
                if (rangeItem && rangeItem.defaultOptions) state.currentSelectedOptions = [...rangeItem.defaultOptions];
                
                // Marquage
                if (rangeItem && rangeItem.markingType) state.currentSpecificity = `FINITION : ${rangeItem.markingType}`;
                else state.currentSpecificity = '';

                // ▼▼▼ NOUVEAU : Application automatique du coloris par défaut ▼▼▼
                if (rangeItem && rangeItem.defaultColor) {
                    state.currentColor = rangeItem.defaultColor;
                }
                // ▲▲▲ ----------------------------------------------------- ▲▲▲
            }

            renderProductForm();
            calculateCurrentFormPrice(); 
            
            setTimeout(() => {
                const formDetails = document.getElementById('product-details-form');
                if (formDetails) formDetails.scrollIntoView({ behavior: 'smooth', block: 'start' });
            }, 50);
        }
       if (classList.contains('size-input') || id === 'accessory-qty') {
            // Met à jour la quantité dans l'état de l'application
            if (classList.contains('size-input')) {
                state.currentQuantities[dataset.size] = value;
            } else {
                state.currentAccessoryQuantity = value;
            }

            // Recalcule le prix unitaire basé sur les nouvelles quantités
            calculateCurrentFormPrice();

            // Met à jour l'état (activé/désactivé) du bouton "Ajouter Article"
            updateButtonStates();

            // Met à jour SEULEMENT l'affichage du prix calculé sans redessiner tout le formulaire
            const priceDisplayContainer = document.querySelector('#add-button-step .bg-indigo-50'); // Cible le conteneur du prix
            if (priceDisplayContainer) {
                 if (state.currentCalculatedUnitPrice > 0) {
                     // Met à jour le texte du prix
                     const priceSpan = priceDisplayContainer.querySelector('.text-xl');
                     if(priceSpan){
                        priceSpan.textContent = `${state.currentCalculatedUnitPrice.toFixed(2)}€`;
                     }
                     // S'assure que le conteneur est visible
                     priceDisplayContainer.style.display = 'block'; // Ou 'flex' selon votre mise en page
                 } else {
                     // Cache le conteneur si le prix est 0
                     priceDisplayContainer.style.display = 'none';
                 }
            }
            // -- On ne redessine PAS tout le formulaire (renderProductForm()) --
            // -- On ne fait PAS défiler la page vers le bas (scrollIntoView) --
        }
        if (id === 'manual-price-input') { state.manualUnitPrice = value; updateButtonStates(); }
if (id === 'product-reassort-tier') { calculateCurrentFormPrice(); updateButtonStates(); }
        if (id === 'current-color-select') state.currentColor = value;
        if (id === 'current-visual-select') {
            state.currentVisual = value;
if (value !== '' && state.orderScope === 'licensee') {
                const addBtn = document.getElementById('add-product-btn');
                if (addBtn) {
                    // console.log("Visual selected, focusing 'Add Article' automatically"); // Pour débugger si besoin
                    setTimeout(() => addBtn.focus(), 0); // Léger délai pour assurer le focus
                }
            }
        }
        if (id === 'specificity') state.currentSpecificity = value;
if (id === 'internal-note') state.currentInternalNote = value;
if (id === 'bas-marking-select') {
            state.currentMarking = value;
        }
        if (id === 'add-to-stock-check') { state.isAddingForStock = checked; }
        if (classList.contains('option-checkbox')) {
            const { optionName, optionGroup } = dataset;
            if (optionGroup === 'length') {
                const allLengthOptions = allAvailableProducts.filter(p => p.optionGroup === 'length').map(p => p.name);
                const nonLengthOptions = state.currentSelectedOptions.filter(opt => !allLengthOptions.includes(opt));
                state.currentSelectedOptions = checked ? [...nonLengthOptions, optionName] : nonLengthOptions;
           } else if (optionName === 'AJOUT NOM/PRENOM') {
                // Logique NORET existante
                const marquageContainer = document.getElementById('marquage-input-container');
                const marquageInput = document.getElementById('marquage-text');
                if (checked) {
                    state.currentSelectedOptions.push(optionName);
                    marquageContainer?.classList.remove('hidden');
                    marquageInput?.focus();
                } else {
                    state.currentSelectedOptions = state.currentSelectedOptions.filter(o => o !== optionName);
                    if (marquageInput) marquageInput.value = '';
                    state.currentSpecificity = '';
                    marquageContainer?.classList.add('hidden');
                }
           } else if (optionName === 'Personnalisation logo club') {
                const detailsContainer = document.getElementById('brodelec-details-container');
                if (checked) {
                    state.currentSelectedOptions.push(optionName);
                    detailsContainer?.classList.remove('hidden');
                } else {
                    state.currentSelectedOptions = state.currentSelectedOptions.filter(o => o !== optionName);
                    detailsContainer?.classList.add('hidden');
                }
            } else {
                // Autres options standard
                state.currentSelectedOptions = checked ? [...state.currentSelectedOptions, optionName] : state.currentSelectedOptions.filter(o => o !== optionName);
            }
            calculateCurrentFormPrice();
            renderProductForm();
        } else if (target.id === 'pack-select') {
            state.currentPackSelection = target.value;
            state.currentPackItemConfigs = {};
            renderProductForm();
        } else if (target.classList.contains('pack-item-config-select')) {
            const { productName, configType } = target.dataset;
            if (!state.currentPackItemConfigs[productName]) state.currentPackItemConfigs[productName] = { size: '', visual: '', options: [] };
            state.currentPackItemConfigs[productName][configType] = target.value;
        } else if (target.classList.contains('pack-item-config-option')) {
            const { productName, optionName } = target.dataset;
            if (!state.currentPackItemConfigs[productName]) state.currentPackItemConfigs[productName] = { size: '', visual: '', options: [] };
            
            if (target.checked) {
                state.currentPackItemConfigs[productName].options.push(optionName);
            } else {
                state.currentPackItemConfigs[productName].options = state.currentPackItemConfigs[productName].options.filter(opt => opt !== optionName);
            }
        }
    });

// ▼▼▼ REMPLACEZ TOUT L'ÉCOUTEUR DE CLIC PAR CE BLOC COMPLET ▼▼▼
document.body.addEventListener('click', (e) => {
    const target = e.target;
// Intercepte le clic sur la case "Taille Décalée" pour ouvrir la modale
    if (target.dataset.optionName === 'Majoration Taille Décalée') {
        e.preventDefault(); // Empêche le comportement par défaut de la case à cocher
        showSplitSizingModal();
        return; // Arrête l'exécution pour ne pas déclencher d'autres logiques de clic
    }
    const actionTarget = target.closest('[data-action]');
    
    // Gère le clic sur les cartes de quantité (Hauts, Bas, etc.)
    const quantityCard = target.closest('.quantity-card');
    if (quantityCard) {
        showArticleDetailModal(quantityCard.dataset.subtype);
        return; // Très important pour arrêter l'exécution ici
    }

    // Gère le clic sur le label "Importer Fichier"
    if (target.id === 'import-order-label') {
        e.preventDefault(); 
        const content = document.createElement('div');
        content.innerHTML = `<p class="text-sm">Cette fonction vous permet de <strong>charger un fichier <code>.json</code></strong>.</p>
                             <p class="text-sm mt-2 text-red-600 font-bold">Attention, cela remplacera toutes les informations actuellement saisies.</p>`;
        showModal(dom.mainModal, "Information : Importer un Fichier", content, [
            { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-400' },
            { label: 'Continuer', onClick: () => {
                hideModal(dom.mainModal);
                dom.loadOrderInput.click(); 
            }, className: 'bg-gray-600' }
        ]);
        return; 
    }

    // Gère les boutons principaux d'ajout
    if (target.id === 'add-product-btn' || target.parentElement?.id === 'add-product-btn') {
        handleProductAdd();
    } else if (target.id === 'add-pack-btn') {
        handlePackAdd();
    }
    // Gère les boutons de changement de mode d'ajout
    else if (target.id === 'toggle-add-article') {
        state.addMode = 'article';
        renderProductForm();
    } else if (target.id === 'toggle-add-pack') {
        state.addMode = 'pack';
        state.currentPackSelection = '';
        state.currentPackQuantities = {};
        renderProductForm();
    }
    // Gère les autres boutons
    else if (target.id === 'clear-active-licensee-btn') { state.activeLicensee = ''; renderAll(); }
    else if (target.id === 'reset-product-form-btn') { resetProductForm(); }
    // Gère les actions dans le tableau de commande (Supprimer, Modifier, etc.)
    else if (actionTarget) {
        const { action, itemId, licenseeName } = actionTarget.dataset;
if (action === 'increase-qty' || action === 'decrease-qty') {
            const itemIndex = state.currentOrderLineItems.findIndex(i => i.id == itemId);
            if (itemIndex > -1) {
                const item = state.currentOrderLineItems[itemIndex];
                
                // On ne gère pas les packs ni les tailles multiples pour l'instant
                const isSimpleItem = Object.keys(item.quantitiesPerSize).length <= 1;
                
                if (!isSimpleItem) {
                    showToast("Utilisez 'Modif' pour les articles avec plusieurs tailles.", "warning");
                    return;
                }

                const sizeKey = Object.keys(item.quantitiesPerSize)[0] || 'U';
                let currentQty = item.quantitiesPerSize[sizeKey] || 0;

                if (action === 'increase-qty') {
                    currentQty++;
                } else {
                    currentQty--;
                }

                if (currentQty <= 0) {
                    if (confirm("Supprimer cet article ?")) {
                        state.currentOrderLineItems.splice(itemIndex, 1);
                        showToast("Article supprimé.");
                    }
                } else {
                    item.quantitiesPerSize[sizeKey] = currentQty;
                    item.totalQuantity = currentQty;
                }
                renderAll();
            }
            return; // Important pour ne pas exécuter le reste
        }
        if (action === 'remove-item') {
         const itemIndex = state.currentOrderLineItems.findIndex(item => item.id == itemId);
            if (itemIndex > -1) {
                // 1. On sauvegarde l'élément avant de le supprimer
                const itemToRemove = state.currentOrderLineItems[itemIndex];
                
                // 2. Gestion du Stock (Restauration immédiate)
                if (itemToRemove.isFromStock) {
                    for (const size in itemToRemove.quantitiesPerSize) {
                        const qtyToRestore = itemToRemove.quantitiesPerSize[size];
                        // On s'assure que l'objet stock existe
                        if (!state.clubStock[itemToRemove.productName]) state.clubStock[itemToRemove.productName] = {};
                        state.clubStock[itemToRemove.productName][size] = (state.clubStock[itemToRemove.productName][size] || 0) + qtyToRestore;
                    }
                }

                // 3. Suppression effective
                state.currentOrderLineItems.splice(itemIndex, 1);
                renderAll(); // Mise à jour visuelle
if (currentClubCode) window.syncToCloud(true);
                // 4. Affichage du Toast avec option "ANNULER"
                showToast('Article supprimé.', 'error', {
                    label: '↩ ANNULER',
                    onClick: () => {
                        // --- LOGIQUE D'ANNULATION ---
                        
                        // A. Si c'était du stock, on re-déduit ce qu'on vient de rendre
                        if (itemToRemove.isFromStock) {
                            for (const size in itemToRemove.quantitiesPerSize) {
                                const qtyToConsume = itemToRemove.quantitiesPerSize[size];
                                if (state.clubStock[itemToRemove.productName] && state.clubStock[itemToRemove.productName][size] !== undefined) {
                                    state.clubStock[itemToRemove.productName][size] -= qtyToConsume;
                                    // Nettoyage si 0 (optionnel mais propre)
                                    if (state.clubStock[itemToRemove.productName][size] <= 0) delete state.clubStock[itemToRemove.productName][size];
                                }
                            }
                        }

                        // B. On remet l'article à sa place exacte
                        state.currentOrderLineItems.splice(itemIndex, 0, itemToRemove);
                        
                        // C. On rafraîchit tout
                        renderAll();
                        showToast('Suppression annulée.', 'success');
                    }
                });
            }
        } else if (action === 'toggle-discount') { 
            state.currentOrderLineItems = state.currentOrderLineItems.map(item => item.id == itemId ? { ...item, applyDiscount: !item.applyDiscount } : item); 
            renderAll(); 
        } else if (action === 'edit-item') {
            showEditItemModal(itemId);
} else if (action === 'edit-pack') {
            showEditPackModal(itemId);
        } else if (action === 'add-for-licensee') {
            state.activeLicensee = licenseeName;
            state.licencieName = '';
            renderAll();
            setTimeout(() => { document.getElementById('add-article-section').scrollIntoView({ behavior: 'smooth', block: 'start' }); }, 50);
            scrollToLicensee(licenseeName);
}
        else if (action === 'verify-licensee-order') {
            showLicenseeRecapModal(licenseeName);
        } else if (action === 'manage-deposit') {
            showDepositModal(licenseeName);
        } else if (action === 'return-to-input') {
            dom.licencieNameInput.scrollIntoView({ behavior: 'smooth', block: 'center' });
            dom.licencieNameInput.focus();
        }
    }
      // Gère tous les autres boutons par leur ID
    else if (target.id === 'new-order-btn') { handleNewOrder(); }
    else if (target.id === 'save-order-btn') { handleSaveOrderToFile(); }
    else if (target.id === 'manage-licensees-btn') { showLicenseeManagerModal(); }
    else if (target.id === 'next-licensee-btn') { handleNextLicensee(); }
    else if (target.id === 'validate-order-btn') { handleValidateOrder(); }
    else if (target.closest('#main-modal-close-btn')) { hideModal(dom.mainModal); }
    else if (target.id === 'manage-club-range-btn') { showClubRangeSelectorModal(); }
    else if (target.id === 'manage-visuals-btn') { showVisualManagerModal(); }
    else if (target.id === 'manage-packs-btn') { showPackManagerModal(); }
    else if (target.id === 'toggle-products-btn') { state.showAllProducts = !state.showAllProducts; renderProductForm(); }
    else if (target.id === 'init-stock-btn') { showStockManagerModal(); }
    else if (target.id === 'manage-stock-btn') { showStockManagerModal(); }
else if (target.id === 'generate-blank-order-btn') { showBlankOrderOptionsModal(); }else if (target.closest('#show-treasury-btn')) { showTreasuryModal(); }
else if (target.closest('#show-stats-btn')) { showStatisticsModal(); }
else if (target.id === 'export-labels-btn') { handleExportLabels(); }
else if (target.id === 'export-distribution-excel-btn') { handleExportDistributionExcel(); }
else if (target.id === 'licensee-recap-btn') { showLicenseeRecapModal(); }
else if (target.id === 'editor-mode-btn') { showCatalogEditor(); }
else if (target.id === 'poll-manager-btn') { showPollManagerModal(); }
else if (target.closest('#save-html-app-btn')) { handleSaveAsHtml(); }
else if (target.id === 'regenerate-docs-btn') {
        showRegenerationOptionsModal();
    }    });
   
// Gère la case à cocher pour le forçage de prix
    document.body.addEventListener('change', e => {
        if (e.target.id === 'force-manual-price-check') {
            const container = document.getElementById('manual-price-container');
            if (container) {
                container.classList.toggle('hidden', !e.target.checked);
            }
        }
    });
// --- NOUVEAU BLOC AMÉLIORÉ : Navigation clavier auto (Article -> Taille -> Visuel -> Ajouter) ---
// --- NOUVEAU BLOC AMÉLIORÉ v2 : Navigation clavier auto (Article -> Taille -> Visuel -> Ajouter) ---
    if (dom.productFormContainer) {
        dom.productFormContainer.addEventListener('keyup', (e) => {
            // console.log(`Keyup on: ${e.target.id || e.target.className}, Key: ${e.key}`); // Log général

            // --- 1. Depuis la sélection de l'ARTICLE ---
            if (e.target.id === 'current-product-select' && (e.key === 'Enter' || e.key === 'Tab')) {
                if (state.currentProduct) {
                    e.preventDefault();
                    const firstSizeInput = dom.productFormContainer.querySelector('.size-input');
                    const accessoryQtyInput = dom.productFormContainer.querySelector('#accessory-qty');
                    if (firstSizeInput) {
                        console.log("Focusing first size input");
                        setTimeout(() => firstSizeInput.focus(), 0);
                    } else if (accessoryQtyInput) {
                        console.log("Focusing accessory qty input");
                        setTimeout(() => accessoryQtyInput.focus(), 0);
                    }
                }
            }
            // --- 2. Depuis un champ de TAILLE ---
            else if (e.target.classList.contains('size-input') && (e.key === 'Enter' || e.key === 'Tab')) {
                const qtyValue = e.target.value.trim();
                const qty = parseInt(qtyValue, 10) || 0;

                // On vérifie les conditions pour le saut auto
                const isLicenseeMode = state.orderScope === 'licensee';
                const hasQuantity = qtyValue !== '' && qty > 0;
                // console.log(`Size input keyup: isLicenseeMode=${isLicenseeMode}, hasQuantity=${hasQuantity}`);

                if (isLicenseeMode && hasQuantity) {
                    e.preventDefault();

                    const visualSelect = document.getElementById('current-visual-select');
                    const addBtn = document.getElementById('add-product-btn');
                    const hasVisualsDefined = state.clubVisuals.length > 0;
                    const isVisualSelectVisible = visualSelect && !visualSelect.classList.contains('hidden');

                    // console.log(`Conditions for visual focus: hasVisualsDefined=${hasVisualsDefined}, isVisualSelectVisible=${isVisualSelectVisible}`);

                    if (hasVisualsDefined && isVisualSelectVisible) {
                         console.log("Attempting to focus visual select");
                         setTimeout(() => visualSelect.focus(), 0);
                    } else if (addBtn) {
                         console.log("Attempting to focus add button (no visual or visual hidden)");
                         setTimeout(() => addBtn.focus(), 0);
                    } else {
                         console.log("No element to focus after size input");
                    }
                }
                // Optionnel: Navigation entre tailles si quantité = 0 ou non licencié
                else if (e.key === 'Enter' || e.key === 'Tab') {
                     e.preventDefault();
                     const allSizeInputs = Array.from(dom.productFormContainer.querySelectorAll('.size-input'));
                     const currentIndex = allSizeInputs.indexOf(e.target);
                     const nextInput = allSizeInputs[currentIndex + 1];
                     if (nextInput) {
                         // console.log("Focusing next size input");
                         setTimeout(() => nextInput.focus(), 0);
                     } else { // Dernier champ de taille
                         // console.log("Last size input, trying visual or add button");
                         const visualSelect = document.getElementById('current-visual-select');
                         const addBtn = document.getElementById('add-product-btn');
                         if (state.clubVisuals.length > 0 && visualSelect && !visualSelect.classList.contains('hidden')) {
                             setTimeout(() => visualSelect.focus(), 0);
                         } else if (addBtn) {
                             setTimeout(() => addBtn.focus(), 0);
                         }
                     }
                 }
            }
            // --- 3. Depuis la sélection du VISUEL ---
            else if (e.target.id === 'current-visual-select' && (e.key === 'Enter' || e.key === 'Tab')) {
                 if (state.orderScope === 'licensee') {
                     e.preventDefault();
                     const addBtn = document.getElementById('add-product-btn');
                     if (addBtn) {
                         console.log("Attempting to focus add button from visual select");
                         setTimeout(() => addBtn.focus(), 0);
                     }
                 }
            }
            // --- 4. Depuis la Quantité Accessoire ---
            else if (e.target.id === 'accessory-qty' && (e.key === 'Enter' || e.key === 'Tab')) {
                 if (state.orderScope === 'licensee') {
                    e.preventDefault();
                    const addBtn = document.getElementById('add-product-btn');
                    if(addBtn) {
                        console.log("Attempting to focus add button from accessory qty");
                         setTimeout(() => addBtn.focus(), 0);
                    }
                 }
            }

        }); // Fin keyup listener
    } // Fin if (dom.productFormContainer)
    // --- FIN DU BLOC AMÉLIORÉ v2 ---

dom.loadOrderInput.addEventListener('change', handleLoadOrderFromFile);
    dom.importLicenseesInput.addEventListener('change', handleImportLicensees);
    dom.importStockInput.addEventListener('change', handleImportStock);
    document.getElementById('import-club-range-input').addEventListener('change', handleImportClubRange);
    
    window.addEventListener('beforeunload', (event) => {
        if (state.currentOrderLineItems.length > 0) {
            event.preventDefault();
            event.returnValue = '';
        }
    });

    // ▼▼▼ GESTION DES BOUTONS DE DÉFILEMENT (ÉTAPE 2) ▼▼▼
    const scrollToTopBtn = document.getElementById('scroll-to-top-btn');
    const scrollToBottomBtn = document.getElementById('scroll-to-bottom-btn');
    const summarySection = document.getElementById('summary-and-actions-section');

    scrollToBottomBtn.addEventListener('click', () => {
        summarySection.scrollIntoView({ behavior: 'smooth' });
    });

    scrollToTopBtn.addEventListener('click', () => {
        window.scrollTo({ top: 0, behavior: 'smooth' });
    });

    window.addEventListener('scroll', () => {
        if (window.scrollY > 300) {
            scrollToTopBtn.style.display = 'flex';
        } else {
            scrollToTopBtn.style.display = 'none';
        }
    });

    // ▲▲▲ FIN DE LA GESTION DES BOUTONS ▲▲▲
// ▼▼▼ GESTION DU CLIC SUR LE PANIER FLOTTANT ▼▼▼
    document.getElementById('floating-cart').addEventListener('click', () => {
        document.getElementById('summary-and-actions-section').scrollIntoView({ behavior: 'smooth' });
    });
const scrollToQtyDashboardBtn = document.getElementById('scroll-to-qty-dashboard-btn');
    const qtyDashboardSection = document.getElementById('quantity-dashboard-container');

    if (scrollToQtyDashboardBtn && qtyDashboardSection) { // Check if elements exist
        scrollToQtyDashboardBtn.addEventListener('click', () => {
            qtyDashboardSection.scrollIntoView({ behavior: 'smooth', block: 'start' }); // Use 'start' to align to the top
        });
    }
    // ▲▲▲ FIN DE LA GESTION DU CLIC ▲▲▲
document.getElementById('packBaseQuantity').addEventListener('input', (e) => {
        state.packBaseQuantity = parseInt(e.target.value, 10) || 0;
        // On force un recalcul des totaux pour voir l'impact en direct
        renderAll();
    });
// ▼▼▼ AJOUTER LE BLOC SUIVANT ICI ▼▼▼
    // === DEBUT AJOUT GESTION INPUT REMISE ARTICLE ===
    // Utilise la délégation d'événements sur le corps du tableau
    dom.orderTableBody.addEventListener('change', (e) => {
        // Vérifie si l'élément modifié est bien un input de remise
        if (e.target.classList.contains('item-discount-input')) {
            const itemId = e.target.dataset.itemId;
            const value = e.target.value.trim(); // Valeur saisie
            const itemIndex = state.currentOrderLineItems.findIndex(i => i.id == itemId); // Trouve l'article dans l'état

            if (itemIndex > -1) { // Si l'article existe
                let newRate = undefined; // Utilise 'undefined' pour indiquer "utiliser la globale"
                if (value !== '') { // Si l'utilisateur a saisi quelque chose
                    const parsedRate = parseFloat(value);
                    // Valide la saisie (nombre entre 0 et 100)
                    if (!isNaN(parsedRate) && parsedRate >= 0 && parsedRate <= 100) {
                        newRate = parsedRate; // Stocke la remise spécifique
                    } else {
                         // Si invalide, efface le champ et affiche une erreur
                         e.target.value = ''; // Réinitialise le champ (le placeholder montrera la globale)
                         showToast(`Remise invalide pour ${state.currentOrderLineItems[itemIndex].productName}. Utilisation de la remise globale (${state.clubDiscount}%).`, "error");
                         // newRate reste undefined, donc la globale sera utilisée
                    }
                }
                // else: Si le champ est vidé, newRate reste undefined, la globale s'appliquera

                // Met à jour la propriété 'specificDiscountRate' de l'article dans l'état
                state.currentOrderLineItems[itemIndex].specificDiscountRate = newRate;

                // Recalcule TOUS les totaux et redessine l'interface
                renderAll();
                // Confirme la mise à jour (optionnel)
                // showToast(`Remise mise à jour pour ${state.currentOrderLineItems[itemIndex].productName}.`);
            }
        }
    });
    // === FIN AJOUT GESTION INPUT REMISE ARTICLE ===
    // ▲▲▲ FIN DU BLOC À AJOUTER ▲▲▲
};
  // ==========================================
    // MODULE D'IMPORTATION AVEC TRADUCTEUR (CORRIGÉ)
    // ==========================================
    
    // TABLE DE CORRESPONDANCE (ID JSON -> NOM DANS VOTRE APPLICATION)
    const ID_TO_APP_NAME = {
        // CYCLISME - HAUTS
        "HCH100FRHU": "MAILLOT CLASSIQUE HOMME CONFORT MC VTT/ROUTE",
        "HCM150VESP": "MAILLOT MIXTE PERFORMANCE MC",
        "HCM050VESP": "MAILLOT MIXTE CONFORT SANS MANCHE",
        "HBM150MOMO": "MAILLOT VTT/DESCENTE MIXTE CONFORT MC (Très ample)",
        "HCM180MBST": "MAILLOT MIXTE AERO MC",
        "HCH250YAYA": "MAILLOT MI-SAISON HOMME CONFORT ML",
        "HBM250MOMO": "MAILLOT BMX MIXTE CONFORT ML (Très ample)",
        "HCM280CLCL": "MAILLOT MI-SAISON MIXTE AERO ML",
        "HCM180TPBP": "MAILLOT PLUIE MIXTE AERO MC (non sublimé, marquage DTF)",
        "HCM280TPBP_2": "MAILLOT PLUIE MIXTE AERO ML (non sublimé, marquage DTF)",
        
        // GILETS & VESTES
        "HCM050FIFI": "GILET COUPE-VENT LEGER MIXTE (vent et pluie fine, sans poche dos)",
        "HCM050MTBS": "GILET COUPE-VENT MI-SAISON MIXTE (dos ajouré)",
        "HCM050MTMT": "GILET COUPE-VENT HIVER MIXTE (tout membranné)",
        "HCM250FIFI": "COUPE-VENT LEGER MIXTE CONFORT (vent et pluie fine)",
        "HCM250IMIM": "COUPE-VENT LEGER DEPERLANT MIXTE CONFORT (avec membranne)",
        "HCH200IMYA": "VESTE MI-SAISON MIXTE CONFORT (membranne coupe-vent + mi-saison)",
        "HCH250IDYA": "VESTE HIVER HOMME CONFORT",
        "HCH250IDID": "VESTE HIVER THERMIQUE HOMME CONFORT",
        "HCM280EPKI": "VESTE HIVER MIXTE AERO",

        // BAS
        "BCM150TDSP": "CUISSARD A BRETELLES HOMME CONFORT Peau LANDSCAPE",
        "BCF101TDTD": "CUISSARD FEMME SANS BRETELLES CONFORT Peau LANDSCAPE",
        "BCM180BTBT": "CUISSARD HOMME AERO Peau CERVINO",
        "BCM180BPBP": "CUISSARD A BRETELLES AERO mi-saison - fond CERVINO", // Nom approximatif à vérifier
        "BM4000MKO": "SHORT VTT FOND Peau ENDURANCE 2.5",
        "BCH350TDTD": "CORSAIRE HOMME A BRETELLES CONFORT",
        "BCF300TDTD": "CORSAIRE FEMME SANS BRETELLES CONFORT",
        "BCM251SRSR": "COLLANT HIVER A BRETELLES HOMME CONFORT",
        "BCF201SRSR": "COLLANT HIVER FEMME SANS BRETELLES CONFORT",
        "BCM280BPBP_2": "COLLANT HIVER HOMME AERO Peau CERVINO",
        "BCM210SRSR": "COLLANT MIXTE ECHAUFFEMENT",

        // COMBINAISONS
        "CCM180STBT": "COMBINAISON ROUTE MANCHES COURTES HOMME AERO",
        "CCM180NMTD_PDC": "COMBINAISON CHRONO ROUTE MANCHES COURTES HOMME AERO",
        "CCM480NMTD_PDC": "COMBINAISON CHRONO ROUTE MANCHES LONGUES HOMME AERO",

        // RUNNING
        "HAH150VEVE": "MAILLOT RUNNING HOMME",
        "HAH050VEVE": "DEBARDEUR ATHLETISME HOMME",
        "ABROFOSMSM": "BRASSIERE RUNNING FEMME",
        "HAH250KIKI": "MAILLOT RUNNING HIVER HOMME MANCHES LONGUES",
        "BAM100ADAD": "SHORT RUNNING MIXTE",
        "BAF110ALAL": "SHORTY FEMME RUNNING",
        "BAH110ALAL": "CUISSARD RUNNING HOMME",
        "BAM210ALAL": "COLLANT RUNNING MIXTE",
        
        // ENFANTS
        "HCE150VESP": "MAILLOT ENFANT PERFORMANCE MC",
        "HCE250YAYA": "MAILLOT MI-SAISON ENFANT CONFORT ML",
        "HBE250MOMO": "MAILLOT BMX ENFANT CONFORT ML",
        "HCE050FIFI": "GILET COUPE-VENT LEGER ENFANT",
        "HCE250IDYA": "VESTE HIVER ENFANT CONFORT",
        "BCE150TDSP": "CUISSARD ENFANT CONFORT",
        "BCE251SRSR": "COLLANT HIVER ENFANT SUBLIME CONFORT",
        "BCE210SRSR": "COLLANT ENFANT VELOURS ECHAUFFEMENT",
        "HAE150VEVE": "MAILLOT RUNNING ENFANT MANCHES COURTES",
        "HAE050VEVE": "DEBARDEUR ATHLETISME ENFANT",
        "BAE110ALAL": "CUISSARD RUNNING ENFANT",
        "CTE080K7BT": "TRIFONCTION ENFANT COURTE ET MOYENNE DISTANCE"
    };

    // --- LOGIQUE D'IMPORTATION ---
    setTimeout(() => {
        const fileInput = document.getElementById('json-gamme-input');
        if (fileInput) {
            fileInput.addEventListener('change', function(e) {
                const file = e.target.files[0];
                if (!file) return;

                const reader = new FileReader();
                reader.onload = function(event) {
                    try {
                        const importedData = JSON.parse(event.target.result);
                        console.log("Fichier JSON reçu :", importedData);

                        // 1. IMPORT DU CLUB
                        if (importedData.clubName) {
                            state.clubName = importedData.clubName;
                            if(dom.clubName) dom.clubName.value = importedData.clubName;
                        }
                        if (importedData.clientNumber) {
                            state.clientNumber = importedData.clientNumber;
                            if(dom.clientNumber) dom.clientNumber.value = importedData.clientNumber;
                        }

                        // 2. IMPORT DE LA GAMME
                        if (importedData.savedItems && Array.isArray(importedData.savedItems)) {
                            const newRange = [];
                            let successCount = 0;
                            let failCount = 0;

                            importedData.savedItems.forEach(id => {
                                // A. On essaie de trouver le nom traduit
                                let targetName = ID_TO_APP_NAME[id];

                                // B. Si pas de traduction, on cherche dans le const data (si présent)
                                if (!targetName && typeof data !== 'undefined') {
                                    // Recherche bourrin dans tout le catalogue data
                                    Object.values(data).forEach(cat => cat.forEach(sub => sub.items.forEach(it => {
                                        if (it.id === id) targetName = it.name;
                                    })));
                                }

                                if (targetName) {
                                    // C. On cherche ce nom exact dans VOTRE application
                                    // On nettoie les espaces et on met en majuscules pour comparer souple
                                    const cleanTarget = targetName.toUpperCase().trim();
                                    
                                    const localProduct = allAvailableProducts.find(p => 
                                        p.name.toUpperCase().trim() === cleanTarget || 
                                        p.name.toUpperCase().trim().includes(cleanTarget) // Recherche approximative
                                    );

                                    if (localProduct) {
                                        newRange.push({
                                            name: localProduct.name,
                                            licenseePrice: null,
                                            defaultOptions: [],
                                            markingType: null,
                                            isIncludedInRestock: true
                                        });
                                        successCount++;
                                    } else {
                                        console.warn(`Nom trouvé (${targetName}) mais pas dans allAvailableProducts.`);
                                        failCount++;
                                    }
                                } else {
                                    console.warn(`ID inconnu et non traduit : ${id}`);
                                    failCount++;
                                }
                            });

                            if (newRange.length > 0) {
                                state.clubProductRange = newRange;
                                
                                // Sauvegarde
                                const rangeKey = `range_${state.clubName.replace(/[\s/\\?%*:|"<>]/g, '_')}`;
                                localStorage.setItem(rangeKey, JSON.stringify(state.clubProductRange));

                                state.showAllProducts = false;
                                
                                // Rafraîchissement
                                if (typeof renderProductForm === 'function') renderProductForm();
                                if (typeof renderAll === 'function') renderAll();
                                
                                alert(`✅ IMPORT RÉUSSI !\n\nClub : ${state.clubName}\n${successCount} articles ajoutés à la gamme.\n(${failCount} non reconnus)`);
                            } else {
                                alert("⚠️ Import échoué. Les codes produits du fichier JSON ne correspondent à aucun article de ce bon de commande.");
                            }
                        }
                    } catch (err) {
                        console.error("Erreur import :", err);
                        alert("Fichier invalide.");
                    }
                    e.target.value = '';
                };
                reader.readAsText(file);
            });
        }
    }, 1000);
document.addEventListener('DOMContentLoaded', init);
window.state = state;
    window.renderProductForm = renderProductForm;
window.secureImport = () => {
        // 1. On demande le code
        const code = prompt("🔐 Saisir le code ADMIN pour importer :");
        
        // 2. On vérifie le code
        if (code === '582069') {
            // Si c'est bon, on clique virtuellement sur l'input caché
            const fileInput = document.getElementById('load-order-input');
            if(fileInput) {
                fileInput.click();
            } else {
                alert("Erreur technique : Le champ d'importation (load-order-input) est introuvable.");
            }
        } else if (code !== null) {
            // Si le code est faux (et qu'on n'a pas cliqué sur Annuler)
            alert("⛔ Code incorrect. Accès refusé.");
        }
    };
    window.calculateCurrentFormPrice = calculateCurrentFormPrice;
// --- FONCTION DE SÉCURITÉ POUR L'IMPORT COCKPIT ---
const secureCockpitImport = () => {
    const accessCode = prompt("Veuillez saisir le code d'accès commercial pour importer les données :");
    
    if (accessCode === '582069') {
        // Si le code est bon, on déclenche le clic sur l'input caché
        document.getElementById('hidden-cockpit-input').click();
    } else if (accessCode !== null) {
        alert("⛔ Code incorrect. Cette fonction est réservée à l'usage commercial.");
    }
};

// Rendre la fonction de sécurité publique pour le bouton
window.secureCockpitImport = secureCockpitImport;
const handleCockpitImport = (event) => {
    const file = event.target.files[0];
    if (!file) return;

    const reader = new FileReader();
    reader.onload = async (e) => { // Notez le 'async' ici
        try {
            const data = JSON.parse(e.target.result);
            
            // 1. Mise à jour de l'affichage (Mémoire locale)
            if (document.getElementById('clubName')) document.getElementById('clubName').value = data.nomClub || '';
            if (document.getElementById('departement')) document.getElementById('departement').value = data.departement || '';
            if (document.getElementById('clientNumber')) document.getElementById('clientNumber').value = data.numClient || '';
            if (document.getElementById('preOrderNumber')) document.getElementById('preOrderNumber').value = data.numPrecommande || '';
            if (document.getElementById('factoryDepartureDate')) document.getElementById('factoryDepartureDate').value = data.departUsine || '';
            if (document.getElementById('deliveryContact')) document.getElementById('deliveryContact').value = data.portable || '';
            if (document.getElementById('deliveryEmail')) document.getElementById('deliveryEmail').value = data.email || '';

            let rawAddress = data.adresseLivraison || '';
            let formattedAddress = rawAddress.split(',').map(part => part.trim()).join('\n');
            
            if (document.getElementById('deliveryAddress')) {
                document.getElementById('deliveryAddress').value = formattedAddress;
            }
            state.deliveryAddress = formattedAddress;

            // Mise à jour des variables d'état
            state.clubName = data.nomClub || '';
            state.departement = data.departement || '';
            state.clientNumber = data.numClient || '';
            state.preOrderNumber = data.numPrecommande || '';
            state.factoryDepartureDate = data.departUsine || '';
            state.deliveryContact = data.portable || '';
            state.deliveryEmail = data.email || '';

            saveClientInfo(); 
            renderAll(); 
            
            // 2. SAUVEGARDE IMMÉDIATE SUR LE CLOUD (C'est ici la correction)
            if (currentClubCode) {
                await window.syncToCloud(false); // false = Affiche le message "Sauvegardé"
                showToast("✅ Identité importée et enregistrée sur le Cloud !");
            } else {
                showToast("⚠️ Données affichées mais PAS enregistrées (Pas de code club actif).", "warning");
            }

        } catch (err) {
            console.error(err);
            showToast("Erreur : Fichier JSON corrompu", "error");
        }
    };
    reader.readAsText(file);
    event.target.value = ''; 
};    window.handleCockpitImport = handleCockpitImport;
    // --- FIN DU COLLAGE ---
// --- FONCTION DE SÉCURITÉ POUR L'IMPORT ---
    window.secureImport = () => {
        // 1. On crée le contenu de la fenêtre
        const container = document.createElement('div');
        container.innerHTML = `
            <div class="space-y-3">
                <p class="text-sm text-gray-600">Cette action nécessite une autorisation administrateur.</p>
                <div>
                    <label class="block text-xs font-bold text-gray-700 uppercase">Code de sécurité</label>
                    <input type="password" id="secure-import-code" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-emerald-500 focus:ring-emerald-500 font-mono text-center tracking-widest" placeholder="Code à 6 chiffres">
                </div>
            </div>
        `;

        // 2. On affiche la modale (On utilise dom.mainModal qui est global)
        const modal = document.getElementById('main-modal'); 
        showModal(modal, "🔐 Accès Réservé", container, [
            { 
                label: 'Annuler', 
                onClick: () => modal.classList.remove('is-open'), 
                className: 'bg-gray-400 text-white' 
            },
            { 
                label: 'Valider & Ouvrir', 
                className: 'bg-emerald-600 text-white font-bold shadow-lg',
                onClick: () => {
                    const codeInput = document.getElementById('secure-import-code');
                    if (codeInput.value === '582069') {
                        modal.classList.remove('is-open');
                        // Petite pause pour laisser la modale se fermer
                        setTimeout(() => {
                            document.getElementById('load-order-input').click();
                        }, 100);
                    } else {
                        codeInput.classList.add('border-red-500', 'ring-2', 'ring-red-200');
                        codeInput.value = '';
                        codeInput.placeholder = "Code Erroné !";
                        codeInput.focus();
                    }
                } 
            }
        ], 'max-w-sm');

        // 3. Focus automatique
        setTimeout(() => {
            const input = document.getElementById('secure-import-code');
            if(input) input.focus();
        }, 100);
    };
// --- NOUVELLE FONCTION : GÉNÉRATION BON DE COMMANDE FOURNISSEUR ---
    const generateOrderPDF = () => {
        if (state.currentOrderLineItems.length === 0) return showToast("Panier vide !", "error");

        const { jsPDF } = window.jspdf;
        const doc = new jsPDF();
        const pageWidth = doc.internal.pageSize.width;

        // 1. EN-TÊTE
        doc.setFontSize(20); doc.setFont(undefined, 'bold');
        doc.text("BON DE COMMANDE FOURNISSEUR", pageWidth / 2, 20, { align: 'center' });
        
        doc.setFontSize(12); doc.setFont(undefined, 'normal');
        doc.text(`Club : ${state.clubName}`, 14, 30);
        doc.text(`Date : ${new Date().toLocaleDateString()}`, 14, 36);
        doc.text(`Référence : ${state.clubName.substring(0, 3).toUpperCase()}-${Date.now().toString().slice(-6)}`, 14, 42);

        // 2. AGGRÉGATION INTELLIGENTE (Fusion des lignes)
        let consolidatedItems = [];
        let itemsMap = new Map(); 

        state.currentOrderLineItems.forEach(item => {
            // On ignore le stock et les articles Brodelec pour ce bon spécifique
            const prodInfo = productMap.get(item.productName);
            if (item.isFromStock) return;
            if (prodInfo && prodInfo.supplier === 'BRODELEC') return;

            // Clé unique pour fusionner (Nom + Options + Couleur)
            const key = item.productName + (item.options ? item.options.sort().join('') : '') + (item.color || '');

            if (itemsMap.has(key)) {
                let existingItem = itemsMap.get(key);
                existingItem.totalQuantity += item.totalQuantity;
                // Fusion des tailles
                for (const [size, qty] of Object.entries(item.quantitiesPerSize)) {
                    existingItem.quantitiesPerSize[size] = (existingItem.quantitiesPerSize[size] || 0) + qty;
                }
            } else {
                let newItem = JSON.parse(JSON.stringify(item));
                itemsMap.set(key, newItem);
                consolidatedItems.push(newItem);
            }
        });

        // Tri alphabétique
        consolidatedItems.sort((a, b) => a.productName.localeCompare(b.productName));

        // 3. TABLEAU
        const tableBody = consolidatedItems.map(item => {
            // Formatage des tailles (ex: S:2, M:5)
            const sizesTxt = Object.entries(item.quantitiesPerSize)
                .filter(([_, q]) => q > 0)
                .map(([s, q]) => `${s}:${q}`)
                .join(', ');

            // Recalcul du prix public sur la quantité TOTALE fusionnée
            const publicPrice = getUnitPriceTTC(item.productName, item.totalQuantity, item.options || []);

            let nameDisplay = item.productName;
            if(item.options.length) nameDisplay += `\nOpt: ${item.options.join(', ')}`;
            if(item.color) nameDisplay += `\nCoul: ${item.color}`;

            return [
                nameDisplay,
                sizesTxt,
                item.totalQuantity,
                publicPrice.toFixed(2) + ' €' 
            ];
        });

        if (consolidatedItems.length === 0) {
            doc.text("Aucun article NORET à commander.", 14, 60);
        } else {
            doc.autoTable({
                startY: 50,
                head: [['Désignation Article', 'Répartition Tailles', 'Qté Totale', 'P.U. Public TTC']],
                body: tableBody,
                theme: 'grid',
                headStyles: { fillColor: [41, 128, 185], halign: 'center' },
                columnStyles: {
                    0: { cellWidth: 80, fontSize: 9, fontStyle: 'bold' },
                    1: { cellWidth: 60, fontSize: 8 }, 
                    2: { cellWidth: 20, halign: 'center', fontStyle: 'bold', fontSize: 10 },
                    3: { cellWidth: 20, halign: 'right' }
                }
            });

            // Totaux
            const finalY = doc.lastAutoTable.finalY + 10;
            const totalQty = consolidatedItems.reduce((acc, i) => acc + i.totalQuantity, 0);
            
            doc.setFontSize(11); doc.setFont(undefined, 'bold');
            doc.text(`TOTAL PIÈCES À PRODUIRE : ${totalQty}`, 14, finalY);
        }

        const safeName = state.clubName.replace(/[^a-z0-9]/gi, '_');
        doc.save(`BON_COMMANDE_FOURNISSEUR_${safeName}.pdf`);
        showToast("Bon Fournisseur généré !");
    };
// =========================================================
// --- PANNEAU ADMIN (VUE D'ENSEMBLE) ---
// =========================================================

window.showAdminDashboard = () => {
    const pass = prompt("Accès réservé. Veuillez saisir le mot de passe Administrateur :");
    if (pass !== '582069') { if (pass !== null) alert("Code incorrect."); return; }

    showToast("Chargement des données Cloud...");

    const clubsRef = ref(db, 'clubs');
    get(clubsRef).then((snapshot) => {
        if (!snapshot.exists()) { showToast("Aucune donnée.", "info"); return; }

        const allClubs = snapshot.val();
        const container = document.createElement('div');
        container.className = "space-y-4";

        let tableHtml = `
            <div class="overflow-x-auto max-h-[60vh]">
                <table class="min-w-full text-sm text-left border-collapse">
                    <thead class="bg-slate-800 text-white font-bold sticky top-0">
                        <tr>
                            <th class="px-4 py-3 border-b">Code</th>
                            <th class="px-4 py-3 border-b">Club</th>
                            <th class="px-4 py-3 border-b text-right">Total TTC</th>
                            <th class="px-4 py-3 border-b text-center">Accès</th> <th class="px-4 py-3 border-b text-center">Action</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y divide-gray-200">
        `;

        const sortedCodes = Object.keys(allClubs).sort();

        sortedCodes.forEach(code => {
            const clubData = allClubs[code];
            const clubItems = clubData.currentOrderLineItems || [];
            let clubTotalTTC = clubItems.reduce((sum, item) => sum + (item.totalPriceTTC || 0), 0);
            
            // État du blocage
            const isBlocked = clubData.isAccessBlocked === true;
            const rowClass = isBlocked ? "bg-red-50 text-red-900" : "hover:bg-indigo-50";
            const lockIcon = isBlocked ? "🔒 BLOQUÉ" : "✅ ACTIF";
            const lockBtnLabel = isBlocked ? "Débloquer" : "Bloquer";
            const lockBtnClass = isBlocked ? "bg-green-600 hover:bg-green-700" : "bg-red-500 hover:bg-red-600";

            tableHtml += `
                <tr class="transition ${rowClass}">
                    <td class="px-4 py-2 font-mono font-bold text-xs">${code}</td>
                    <td class="px-4 py-2 font-medium">${clubData.clubName || 'N/A'}</td>
                    <td class="px-4 py-2 text-right font-bold">${clubTotalTTC.toFixed(2)} €</td>
                    <td class="px-4 py-2 text-center text-xs font-bold">${lockIcon}</td>
                    <td class="px-4 py-2 text-center flex gap-2 justify-center">
                        <button onclick="hideModal(dom.mainModal); loadClubFromAdmin('${code}')" class="bg-indigo-600 text-white px-2 py-1 rounded text-xs hover:bg-indigo-700">Ouvrir</button>
                        <button onclick="toggleClubAccess('${code}', ${isBlocked})" class="${lockBtnClass} text-white px-2 py-1 rounded text-xs">${lockBtnLabel}</button>
                    </td>
                </tr>
            `;
        });

        tableHtml += `</tbody></table></div>`;
        container.innerHTML = tableHtml;
        showModal(dom.mainModal, "🕵️ Administration & Sécurité", container, [
            { label: 'Fermer', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-500 text-white' }
        ], 'max-w-6xl');
    });
};
// Fonction pour charger un club directement depuis la liste
window.loadClubFromAdmin = async (code) => {
    if(!confirm(`Voulez-vous charger le dossier du club ${code} ?\nCela remplacera les données actuellement à l'écran.`)) return;
    
    currentClubCode = code;
    const clubRef = ref(db, 'clubs/' + currentClubCode);
    
    try {
        const snapshot = await get(clubRef);
      // --- CAS 1 : LE DOSSIER EXISTE DÉJÀ (Connexion classique) ---
        if (snapshot.exists()) {
            const cloudData = snapshot.val();
            
            // Vérification si banni
            if (cloudData.isAccessBlocked === true) {
                alert("⛔ ACCÈS REFUSÉ\nCe dossier a été clôturé.");
                currentClubCode = null;
                return;
            }

            // --- NOUVELLE LOGIQUE HYBRIDE ---
            
            // 1. On charge les données générales (soit dans 'general', soit à la racine pour compatibilité)
            // Cela récupère : Nom, Licenciés, Gamme, Stock, etc.
            const dataToLoad = cloudData.general ? cloudData.general : cloudData;
            Object.assign(state, dataToLoad);
            
            // 2. MAIS ON VIDE LA SESSION DE COMMANDE
            // Le panier doit être vide au démarrage tant qu'on n'a pas rentré de N° de Précommande
            state.currentOrderLineItems = [];
            state.orderScope = ''; 
            state.preOrderNumber = ''; 
            state.orderSpecificity = '';
            
            // On vide les champs visuels
            if (document.getElementById('preOrderNumber')) document.getElementById('preOrderNumber').value = '';
            
            // On décoche les boutons radios
            if (document.getElementById('scope-global')) document.getElementById('scope-global').checked = false;
            if (document.getElementById('scope-licensee')) document.getElementById('scope-licensee').checked = false;

            showToast(`✅ Connecté : Club ${state.clubName}`);
            
            // On débloque l'interface
            unlockApp();
            renderAll(); 
        }
        
        // --- CAS 2 : LE DOSSIER N'EXISTE PAS (Création) ---
        else {
            // A. DÉTECTION DU "PÈRE"
            let masterCode = "";
            let masterData = null;

            // Nouvelle logique : on cherche la plus longue correspondance existante
            // Si on tape PORNIC-CYCLOS123, on teste si "PORNIC-CYCLOS" existe
            const codes = Object.keys((await get(ref(db, 'clubs'))).val() || {});
            
            // On trie les codes existants du plus long au plus court
            const bestMatch = codes
                .filter(existingCode => code.startsWith(existingCode) && existingCode !== code)
                .sort((a, b) => b.length - a.length)[0];

            if (bestMatch) {
                const masterSnap = await get(ref(db, 'clubs/' + bestMatch));
                if (masterSnap.exists()) {
                    masterData = masterSnap.val();
                    masterCode = bestMatch;
                }
            }

            // B. SCÉNARIO : FICHE IDENTITÉ TROUVÉE
            if (masterData) {
                const confirmClone = confirm(`✨ Fiche identité trouvée : "${masterData.clubName}" (${masterCode}).\n\nVoulez-vous initialiser la commande "${code}" avec ces données (Adresses, Licenciés, Gamme) ?`);
                
                if (confirmClone) {
                    // Sécurité Admin pour créer
                    const creationPass = prompt("🔐 Création de commande : Mot de passe Admin requis :");
                    if (creationPass !== '582069') { 
                        alert("⛔ Refusé."); currentClubCode = null; return; 
                    }

                    // 1. On copie les données STRUCTURELLES du Master (Fiche identité)
                    state.clubName = masterData.clubName;
                    state.departement = masterData.departement;
                    state.clientNumber = masterData.clientNumber;
                    
                    state.deliveryAddress = masterData.deliveryAddress || '';
                    state.deliveryContact = masterData.deliveryContact || '';
                    state.deliveryEmail = masterData.deliveryEmail || '';
                    
                    state.licenseeList = masterData.licenseeList || [];
                    state.licenseeEmails = masterData.licenseeEmails || {};
                    
                    state.clubProductRange = masterData.clubProductRange || [];
                    state.clubVisuals = masterData.clubVisuals || [];
                    state.clubPacks = masterData.clubPacks || [];
                    state.clubStock = masterData.clubStock || {}; // On reprend le stock du club
                    
                    // 2. On initialise les données de la NOUVELLE COMMANDE
                    state.preOrderNumber = code; // Le code devient le N° Précommande
                    state.orderDate = new Date().toISOString().split('T')[0];
                    state.currentOrderLineItems = []; // Panier vide pour commencer
                    state.validatedLicensees = [];

                    // 3. Sauvegarde immédiate
                    await window.syncToCloud(true);
                    showToast(`🚀 Commande ${code} créée d'après ${masterCode} !`);
                } else {
                    currentClubCode = null; return;
                }
            }
            // C. SCÉNARIO : AUCUNE FICHE TROUVÉE (Création Vierge)
            else {
                const create = confirm(`Code "${code}" inconnu et aucune fiche identité ("${code.split('-')[0]}") trouvée.\nCréer un espace vierge ?`);
                if (!create) { currentClubCode = null; return; }

                const creationPass = prompt("🔐 Création vierge : Mot de passe Admin requis :");
                if (creationPass !== '582069') { alert("⛔ Refusé."); currentClubCode = null; return; }

                state.preOrderNumber = code; 
                await window.syncToCloud(true);
                showToast(`🚀 Espace vierge créé : ${code}`);
            }
        }

        // Lancement de l'interface
        unlockApp();
        renderAll();

    } catch (error) {
        console.error(error);
        alert("Erreur technique : " + error.message);
    }
};window.bypassLogin = () => {
    const pass = prompt("Mot de passe Administrateur pour accès hors-ligne :");
    if (pass === '582069') { // Votre mot de passe admin
        unlockApp();
        showToast("⚠️ Mode Démo / Admin (Non connecté au Cloud)", "warning");
    }
};

function unlockApp() {
    document.getElementById('login-overlay').classList.add('hidden'); // Cache l'écran de connexion
    document.getElementById('main-app-view').classList.remove('hidden'); // Affiche l'appli
}
// =========================================================
// --- IMPORTATION DONNÉES D'UN AUTRE CLUB (ARCHIVAGE) ---
// =========================================================

window.importFromPreviousCode = async () => {
    // 1. Sécurité Admin
    const pass = prompt("Accès réservé. Mot de passe :");
    if (pass !== '582069') return;

    if (!currentClubCode) {
        showToast("Vous devez être connecté au NOUVEAU code avant d'importer.", "error");
        return;
    }

    // 2. Demander le code source
    const sourceCodeInput = prompt("Entrez l'ANCIEN code (Source) dont vous voulez récupérer les licenciés et la gamme :");
    if (!sourceCodeInput) return;
    
    // Nettoyage du code source (comme pour le login)
    const sourceCode = sourceCodeInput.trim().toUpperCase().replace(/[.#$[\]]/g, '-');

    if (sourceCode === currentClubCode) {
        alert("Impossible d'importer depuis le même code !");
        return;
    }

    // 3. Aller chercher les infos
    try {
        showToast("Recherche des données...", "info");
        const sourceRef = ref(db, 'clubs/' + sourceCode);
        const snapshot = await get(sourceRef);

        if (!snapshot.exists()) {
            showToast("Aucune donnée trouvée pour ce code source.", "error");
            return;
        }

        const sourceData = snapshot.val();

        // 4. Sélectionner UNIQUEMENT les données structurelles
        if(confirm(`Données trouvées pour "${sourceData.clubName || sourceCode}".\nVoulez-vous importer les licenciés, la gamme et les infos (sans la commande) dans "${state.clubName || currentClubCode}" ?`)) {
            
            // A. Infos Club (On garde le nom actuel s'il est déjà saisi, sinon on prend l'ancien)
            if (!state.clubName) state.clubName = sourceData.clubName || '';
            state.departement = sourceData.departement || state.departement;
            state.clientNumber = sourceData.clientNumber || state.clientNumber;
            
            // B. Coordonnées (On reprend tout)
            state.deliveryAddress = sourceData.deliveryAddress || '';
            state.deliveryContact = sourceData.deliveryContact || '';
            state.deliveryEmail = sourceData.deliveryEmail || '';

            // C. Base de données Licenciés (Le plus important)
            state.licenseeList = sourceData.licenseeList || [];
            state.licenseeEmails = sourceData.licenseeEmails || {};
            
            // D. Gamme Produits & Visuels
            state.clubProductRange = sourceData.clubProductRange || [];
            state.clubVisuals = sourceData.clubVisuals || [];
            state.clubPacks = sourceData.clubPacks || [];
            state.clubStock = sourceData.clubStock || {}; 

            // 5. Sauvegarder dans le NOUVEAU code
            await window.syncToCloud(false); 
            
            // Mise à jour visuelle des champs
            if(dom.clubName) dom.clubName.value = state.clubName;
            if(dom.departement) dom.departement.value = state.departement;
            if(dom.clientNumber) dom.clientNumber.value = state.clientNumber;
            
            renderAll();
            alert(`Succès ! Les données de ${sourceCode} ont été importées.\n\n- ${state.licenseeList.length} licenciés récupérés.\n- Gamme et Stock à jour.`);
        }

    } catch (error) {
        console.error(error);
        showToast("Erreur lors de l'importation : " + error.message, "error");
    }
};
window.toggleClubAccess = async (code, currentStatus) => {
    // currentStatus est 'true' si déjà bloqué, 'false' sinon
    const newStatus = !currentStatus;
    const action = newStatus ? "BLOQUER" : "DÉBLOQUER";
    
    if (!confirm(`Voulez-vous vraiment ${action} l'accès pour le code "${code}" ?`)) return;

    try {
        // On met à jour seulement le champ 'isAccessBlocked' dans Firebase
        await update(ref(db, 'clubs/' + code), { isAccessBlocked: newStatus });
        
        // On rafraîchit le tableau de bord pour voir le changement
        hideModal(dom.mainModal); // Ferme l'ancien
        
        // Petite astuce : on relance le dashboard automatiquement
        // Mais comme il demande le mot de passe, on va le "simuler" ou juste afficher un message
        showToast(`Accès pour ${code} mis à jour : ${newStatus ? 'BLOQUÉ' : 'ACTIF'}`);
        
        // Optionnel : Si vous voulez rouvrir le tableau sans retaper le code,
        // il faudrait modifier showAdminDashboard pour accepter un paramètre "bypass".
        // Pour l'instant, réouvrir manuellement suffit.
        
    } catch (e) {
        alert("Erreur lors de la mise à jour : " + e.message);
    }
};
</script>
<div id="active-licensee-banner" class="hidden fixed bottom-5 left-5 z-50 bg-white border-l-8 border-blue-600 p-4 rounded-lg shadow-2xl max-w-xs sm:max-w-sm transition-all duration-300 ease-in-out border border-gray-200">
    <div class="flex flex-col gap-2">
        <div>
            <p class="text-[10px] font-bold text-gray-400 uppercase tracking-wider mb-1">Saisie en cours pour :</p>
            <p id="banner-licensee-name" class="text-lg font-extrabold text-blue-800 truncate leading-tight"></p>
        </div>
        
        <div class="flex gap-2 mt-1">
            <button id="licensee-recap-btn" class="flex-1 px-3 py-1.5 bg-blue-50 text-blue-700 text-xs font-bold rounded hover:bg-blue-100 border border-blue-200 transition-colors flex items-center justify-center gap-1">
                <span>🛒</span> Voir Panier
            </button>
            <button id="clear-active-licensee-btn" class="px-3 py-1.5 bg-red-50 text-red-600 text-xs font-bold rounded hover:bg-red-100 border border-red-200 transition-colors" title="Arrêter la saisie pour ce licencié">
                &times; Stop
            </button>
        </div>
    </div>
</div>
<div class="fixed bottom-5 right-5 z-50 flex flex-col gap-2">
    <button id="floating-cart" title="Voir le panier et les totaux" class="h-12 w-12 rounded-full bg-blue-600 text-white shadow-lg hover:bg-blue-700 flex items-center justify-center transform transition hover:scale-110">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z" />
         </svg>
    </button>

    <button id="show-stats-btn" title="Statistiques" class="h-12 w-12 rounded-full bg-purple-600 text-white shadow-lg hover:bg-purple-700 flex items-center justify-center transform transition hover:scale-110">
         <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z" />
        </svg>
    </button>

    <button id="show-treasury-btn" title="Trésorerie" class="h-12 w-12 rounded-full bg-emerald-600 text-white shadow-lg hover:bg-emerald-700 flex items-center justify-center transform transition hover:scale-110">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
        </svg>
    </button>

    <button id="scroll-to-qty-dashboard-btn" title="Tableau des quantités" class="h-12 w-12 rounded-full bg-teal-600 text-white shadow-lg hover:bg-teal-700 flex items-center justify-center transform transition hover:scale-110">
         <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M9 17v-2m3 2v-4m3 4v-6m2 10V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2h14a2 2 0 002-2z" />
         </svg>
    </button>

    <button id="scroll-to-bottom-btn" title="Aller aux totaux" class="h-12 w-12 rounded-full bg-indigo-600 text-white shadow-lg hover:bg-indigo-700 flex items-center justify-center transform transition hover:scale-110">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M19 13l-7 7-7-7m14-8l-7 7-7-7" />
         </svg>
    </button>

    <button id="scroll-to-top-btn" title="Remonter en haut" class="h-12 w-12 rounded-full bg-gray-600 text-white shadow-lg hover:bg-gray-700 flex items-center justify-center transform transition hover:scale-110" style="display: none;">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M5 11l7-7 7 7M5 19l7-7 7 7" />
         </svg>
    </button>
</div></div>
</body>
</html>
