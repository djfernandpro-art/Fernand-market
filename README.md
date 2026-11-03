# Fernand-market
Soyez le bienvenue: fashion 
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SuperMarket Stock Manager</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
</head>
<body class="bg-slate-100 text-slate-800 min-h-screen p-4 pb-12">

    <div class="max-w-md mx-auto space-y-5">
        <!-- En-tête -->
        <header class="text-center bg-white p-4 rounded-xl shadow-sm border border-slate-200">
            <h1 class="text-xl font-bold text-blue-600">🛒 SuperMarket Stock</h1>
            <p class="text-xs text-slate-500">Gestion multi-magasins & Séparation des rôles</p>
            
            <!-- Sélecteur de Rôle -->
            <div class="flex bg-slate-100 p-1 rounded-lg mt-3">
                <button type="button" onclick="setRole('vendeur')" id="btnVendeur" class="flex-1 py-1.5 text-xs font-bold rounded-md transition bg-blue-600 text-white shadow-sm">🏪 Mode Vendeur</button>
                <button type="button" onclick="setRole('admin')" id="btnAdmin" class="flex-1 py-1.5 text-xs font-bold rounded-md transition text-slate-600">🔒 Mode Admin</button>
            </div>
        </header>

        <!-- ================= ESPACE VENDEUR ================= -->
        <div id="vendeurSection" class="space-y-4">
            <!-- Sélecteur de Branche -->
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200">
                <label class="block text-xs font-semibold text-slate-500 uppercase mb-2">Magasin Actif (Vente) :</label>
                <select id="branchSelect" onchange="refreshUI()" class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-lg font-medium text-slate-700 outline-none focus:ring-2 focus:ring-blue-500">
                    <option value="Ville A">Magasin - Ville A</option>
                    <option value="Ville B">Magasin - Ville B</option>
                    <option value="Ville C">Magasin - Ville C</option>
                    <option value="Ville D">Magasin - Ville D</option>
                </select>
            </div>

            <!-- Indicateurs Rapides Magasin -->
            <div class="grid grid-cols-2 gap-3">
                <div class="bg-white p-3 rounded-xl shadow-sm border border-slate-200 text-center">
                    <span class="text-xs text-slate-400">Références en stock</span>
                    <p id="totalProducts" class="text-xl font-bold text-slate-700">0</p>
                </div>
                <div class="bg-white p-3 rounded-xl shadow-sm border border-slate-200 text-center">
                    <span class="text-xs text-slate-400">Alertes Rupture</span>
                    <p id="lowStockCount" class="text-xl font-bold text-red-500">0</p>
                </div>
            </div>

            <!-- Encaissement / Vente rapide (Style Caisse Supermarché) -->
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 space-y-3">
                <h2 class="text-xs font-bold text-slate-700 uppercase">⚡ Enregistrer un achat / Vente</h2>
                <div class="space-y-2">
                    <select id="saleProductSelect" class="w-full p-2.5 text-sm bg-slate-50 border border-slate-200 rounded-lg outline-none">
                        <option value="">-- Choisir un produit à vendre --</option>
                    </select>
                    <div class="flex gap-2">
                        <input type="number" id="saleQty" value="1" min="1" placeholder="Quantité" class="w-1/3 p-2 text-sm bg-slate-50 border border-slate-200 rounded-lg outline-none text-center">
                        <button type="button" onclick="processSale()" class="w-2/3 bg-emerald-600 text-white font-medium py-2 rounded-lg text-xs shadow hover:bg-emerald-700 transition">Valider la Vente (Sortie)</button>
                    </div>
                </div>
            </div>

            <!-- Ajout de produit par le vendeur -->
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 space-y-3">
                <h2 class="text-xs font-bold text-slate-700 uppercase">➕ Ajouter un article au magasin</h2>
                <div class="space-y-2">
                    <input type="text" id="prodName" placeholder="Nom du produit" class="w-full p-2 text-sm bg-slate-50 border border-slate-200 rounded-lg outline-none">
                    <div class="grid grid-cols-2 gap-2">
                        <input type="number" id="prodQty" placeholder="Qté initiale" min="0" class="w-full p-2 text-sm bg-slate-50 border border-slate-200 rounded-lg outline-none">
                        <input type="number" id="prodMin" placeholder="Seuil alerte" min="0" class="w-full p-2 text-sm bg-slate-50 border border-slate-200 rounded-lg outline-none">
                    </div>
                    <button type="button" onclick="addProduct()" class="w-full bg-blue-600 text-white font-medium py-2 rounded-lg text-xs shadow hover:bg-blue-700 transition">Ajouter au catalogue</button>
                </div>
            </div>

            <!-- Liste des produits du magasin actif -->
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 space-y-3">
                <h2 class="text-xs font-bold text-slate-700 uppercase">📋 Inventaire du Magasin Actif</h2>
                <div id="productList" class="space-y-2"></div>
            </div>
        </div>

        <!-- ================= ESPACE ADMIN ================= -->
        <div id="adminSection" class="space-y-4 hidden">
            <div class="bg-amber-50 border border-amber-200 p-3 rounded-xl text-xs text-amber-800">
                🔒 <strong>Panneau Administrateur :</strong> Vue globale et centralisée de tous les magasins et de l'historique des mouvements.
            </div>

            <!-- Résumé Global des 4 Magasins -->
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 space-y-3">
                <h2 class="text-xs font-bold text-slate-700 uppercase">📊 Vue d'ensemble des Magasins</h2>
                <div id="adminGlobalSummary" class="space-y-2 text-sm">
                    <!-- Généré dynamiquement -->
                </div>
            </div>

            <!-- Journal Global des Mouvements (Entrées / Sorties) -->
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 space-y-3">
                <h2 class="text-xs font-bold text-slate-700 uppercase">📜 Journal Global des Mouvements</h2>
                <div id="adminMovementsList" class="space-y-2 text-xs">
                    <!-- Historique -->
                </div>
            </div>
        </div>
    </div>

    <!-- Script JavaScript -->
    <script>
        let currentRole = 'vendeur';

        // Initialisation de la structure de données
        let inventory = JSON.parse(localStorage.getItem('sm_inventory_v4')) || {
            "Ville A": [], "Ville B": [], "Ville C": [], "Ville D": []
        };

        let movements = JSON.parse(localStorage.getItem('sm_movements_v4')) || [];

        function setRole(role) {
            currentRole = role;
            const btnV = document.getElementById('btnVendeur');
            const btnA = document.getElementById('btnAdmin');
            const secV = document.getElementById('vendeurSection');
            const secA = document.getElementById('adminSection');

            if (role === 'vendeur') {
                btnV.className = "flex-1 py-1.5 text-xs font-bold rounded-md transition bg-blue-600 text-white shadow-sm";
                btnA.className = "flex-1 py-1.5 text-xs font-bold rounded-md transition text-slate-600";
                secV.classList.remove('hidden');
                secA.classList.add('hidden');
            } else {
                btnA.className = "flex-1 py-1.5 text-xs font-bold rounded-md transition bg-amber-600 text-white shadow-sm";
                btnV.className = "flex-1 py-1.5 text-xs font-bold rounded-md transition text-slate-600";
                secA.classList.remove('hidden');
                secV.classList.add('hidden');
            }
            refreshUI();
        }

        function saveData() {
            try {
                localStorage.setItem('sm_inventory_v4', JSON.stringify(inventory));
                localStorage.setItem('sm_movements_v4', JSON.stringify(movements));
            } catch (e) {
                alert("Erreur de sauvegarde locale.");
            }
            refreshUI();
        }

        function logMovement(branch, type, productName, qty) {
            movements.unshift({
                date: new Date().toLocaleTimeString() + ' ' + new Date().toLocaleDateString(),
                branch,
                type,
                productName,
                qty
            });
            if (movements.length > 50) movements.pop();
        }

        function getCurrentBranch() {
            return document.getElementById('branchSelect').value;
        }

        function addProduct() {
            const branch = getCurrentBranch();
            const nameInput = document.getElementById('prodName');
            const qtyInput = document.getElementById('prodQty');
            const minInput = document.getElementById('prodMin');

            const name = nameInput.value.trim();
            const qty = parseInt(qtyInput.value);
            const min = parseInt(minInput.value);

            if (!name || isNaN(qty) || isNaN(min)) {
                alert("Veuillez remplir tous les champs correctement.");
                return;
            }

            if (!inventory[branch]) inventory[branch] = [];

            inventory[branch].push({ id: Date.now(), name, quantity: qty, minThreshold: min });
            logMovement(branch, 'ENTRÉE (Création)', name, qty);

            nameInput.value = '';
            qtyInput.value = '';
            minInput.value = '';
            saveData();
        }

        function processSale() {
            const branch = getCurrentBranch();
            const select = document.getElementById('saleProductSelect');
            const index = select.value;
            const qtyInput = document.getElementById('saleQty');
            const qty = parseInt(qtyInput.value);

            if (index === "" || isNaN(qty) || qty <= 0) {
                alert("Sélectionnez un produit et une quantité valide pour la vente.");
                return;
            }

            let prod = inventory[branch][index];
            if (prod.quantity < qty) {
                alert("Stock insuffisant dans ce magasin !");
                return;
            }

            prod.quantity -= qty;
            logMovement(branch, 'SORTIE (Vente)', prod.name, qty);
            qtyInput.value = '1';
            saveData();
        }

        function updateStock(index, delta) {
            const branch = getCurrentBranch();
            let prod = inventory[branch][index];
            if (prod.quantity + delta < 0) return;
            
            prod.quantity += delta;
            let type = delta > 0 ? 'ENTRÉE (Ajustement)' : 'SORTIE (Ajustement)';
            logMovement(branch, type, prod.name, Math.abs(delta));
            saveData();
        }

        function deleteProduct(index) {
            const branch = getCurrentBranch();
            let prod = inventory[branch][index];
            logMovement(branch, 'SUPPRESSION', prod.name, prod.quantity);
            inventory[branch].splice(index, 1);
            saveData();
        }

        function refreshUI() {
            if (currentRole === 'vendeur') {
                renderVendeur();
            } else {
                renderAdmin();
            }
        }

        function renderVendeur() {
            const branch = getCurrentBranch();
            const products = inventory[branch] || [];
            
            document.getElementById('totalProducts').innerText = products.length;
            
            let lowStock = 0;
            const listContainer = document.getElementById('productList');
            const saleSelect = document.getElementById('saleProductSelect');
            
            listContainer.innerHTML = '';
            saleSelect.innerHTML = `<option value="">-- Choisir un produit à vendre --</option>`;

            if (products.length === 0) {
                listContainer.innerHTML = `<p class="text-xs text-slate-400 text-center py-4">Aucun produit dans ce magasin.</p>`;
                document.getElementById('lowStockCount').innerText = 0;
                return;
            }

            products.forEach((p, index) => {
                const isLow = p.quantity <= p.minThreshold;
                if (isLow) lowStock++;

                const card = document.createElement('div');
                card.className = `p-3 rounded-lg border flex justify-between items-center ${isLow ? 'bg-red-50 border-red-200' : 'bg-slate-50 border-slate-200'}`;
                card.innerHTML = `
                    <div>
                        <h3 class="font-bold text-xs text-slate-700">${p.name}</h3>
                        <p class="text-[11px] text-slate-500">Stock : <span class="font-bold ${isLow ? 'text-red-600' : 'text-slate-700'}">${p.quantity}</span> (Min: ${p.minThreshold})</p>
                    </div>
                    <div class="flex items-center space-x-1">
                        <button type="button" onclick="updateStock(${index}, -1)" class="px-2 py-1 bg-white border border-slate-200 rounded text-xs font-bold shadow-sm">-</button>
                        <button type="button" onclick="updateStock(${index}, 1)" class="px-2 py-1 bg-white border border-slate-200 rounded text-xs font-bold shadow-sm">+</button>
                        <button type="button" onclick="deleteProduct(${index})" class="p-1 text-red-400 hover:text-red-600 text-xs ml-1">🗑️</button>
                    </div>
                `;
                listContainer.appendChild(card);

                const opt = document.createElement('option');
                opt.value = index;
                opt.textContent = `${p.name} (Stock: ${p.quantity})`;
                saleSelect.appendChild(opt);
            });

            document.getElementById('lowStockCount').innerText = lowStock;
        }

        function renderAdmin() {
            const summaryContainer = document.getElementById('adminGlobalSummary');
            const movementsContainer = document.getElementById('adminMovementsList');
            
            summaryContainer.innerHTML = '';
            movementsContainer.innerHTML = '';

            const branches = ["Ville A", "Ville B", "Ville C", "Ville D"];
            branches.forEach(b => {
                const prods = inventory[b] || [];
                const totalRefs = prods.length;
                const lowAlerts = prods.filter(p => p.quantity <= p.minThreshold).length;

                const div = document.createElement('div');
                div.className = "p-3 bg-slate-50 border border-slate-200 rounded-lg flex justify-between items-center";
                div.innerHTML = `
                    <div>
                        <strong class="text-slate-700">Magasin ${b}</strong>
                        <p class="text-xs text-slate-500">${totalRefs} références enregistrées</p>
                    </div>
                    <div>
                        <span class="px-2 py-1 rounded text-xs font-bold ${lowAlerts > 0 ? 'bg-red-100 text-red-600' : 'bg-emerald-100 text-emerald-600'}">
                            ${lowAlerts} alerte(s)
                        </span>
                    </div>
                `;
                summaryContainer.appendChild(div);
            });

            if (movements.length === 0) {
                movementsContainer.innerHTML = `<p class="text-xs text-slate-400 text-center py-2">Aucun mouvement enregistré pour le moment.</p>`;
                return;
            }

            movements.forEach(m => {
                const mDiv = document.createElement('div');
                mDiv.className = "p-2 bg-slate-50 border border-slate-200 rounded flex justify-between items-center";
                const isEntry = m.type.includes('ENTRÉE');
                mDiv.innerHTML = `
                    <div>
                        <span class="font-bold text-slate-700">[${m.branch}]</span> <span class="text-slate-600">${m.productName}</span> 
                        <span class="font-semibold ${isEntry ? 'text-emerald-600' : 'text-blue-600'}">(${m.type} : ${m.qty})</span>
                    </div>
                    <span class="text-[10px] text-slate-400">${m.date}</span>
                `;
                movementsContainer.appendChild(mDiv);
            });
        }

        // Lancement au démarrage
        refreshUI();
    </script>
</body>
</html>
