<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>L'AGRICOL-APP - William Nando Daccò</title>
    
    <!-- PWA Manifest & Theme -->
    <link rel="manifest" href="data:application/manifest+json;charset=utf-8,%7B%22name%22%3A%22L'Agricol-App%20-%20William%20Nando%20Dacc%C3%B2%22%2C%22short_name%22%3A%22AgricolApp%22%2C%22start_url%22%3A%22.%22%2C%22display%22%3A%22standalone%22%2C%22background_color%22%3A%22%23f4f6f4%22%2C%22theme_color%22%3A%22%231e4220%22%2C%22icons%22%3A%5B%5D%7D">
    <meta name="theme-color" content="#1e4220">

    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- SheetJS (xlsx) CDN -->
    <script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
    <!-- jsPDF & AutoTable CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.31/jspdf.plugin.autotable.min.js"></script>
    <!-- FontAwesome per Icone -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        :root {
            --fendt-green: #2e6330;
            --fendt-dark: #1e4220;
            --fendt-red: #d32f2f;
            --fendt-silver: #e0e0e0;
        }
        body {
            background-color: #f4f6f4;
            color: #1a1a1a;
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
        }
        .bg-fendt { background-color: var(--fendt-green); }
        .bg-fendt-dark { background-color: var(--fendt-dark); }
        .bg-fendt-red { background-color: var(--fendt-red); }
        .text-fendt { color: var(--fendt-green); }
        .border-fendt { border-color: var(--fendt-green); }
        
        .fendt-badge {
            background: linear-gradient(135deg, #3a3a3a 0%, #1a1a1a 100%);
            border: 1px solid #555;
            box-shadow: inset 0 1px 0 rgba(255,255,255,0.2);
        }
        
        .btn-fendt {
            background: linear-gradient(180deg, #38763b 0%, #234c25 100%);
            box-shadow: 0 4px 6px rgba(0,0,0,0.15);
            transition: all 0.2s ease;
        }
        .btn-fendt:hover {
            background: linear-gradient(180deg, #418844 0%, #2e6330 100%);
            transform: translateY(-1px);
        }
        
        .btn-red {
            background: linear-gradient(180deg, #e53935 0%, #b71c1c 100%);
        }

        .btn-blue {
            background: linear-gradient(180deg, #1e88e5 0%, #1565c0 100%);
        }
        
        .custom-checkbox:checked {
            background-color: var(--fendt-red);
            border-color: var(--fendt-red);
        }

        .toast-animation {
            animation: fadeInOut 4.5s forwards;
        }
        @keyframes fadeInOut {
            0% { opacity: 0; transform: translateY(-20px); }
            15% { opacity: 1; transform: translateY(0); }
            85% { opacity: 1; transform: translateY(0); }
            100% { opacity: 0; transform: translateY(-20px); }
        }
    </style>
</head>
<body class="min-h-screen pb-12">

    <!-- Toast Notification Banner -->
    <div id="toastNotification" class="hidden fixed top-4 left-0 right-0 mx-auto z-50 w-11/12 max-w-lg bg-emerald-800 text-white font-bold px-6 py-4 rounded-xl shadow-2xl border-2 border-green-400 text-center toast-animation">
        <p id="toastMessage" class="text-base sm:text-lg uppercase tracking-wide"></p>
    </div>

    <!-- Header Fendt Harmonioso -->
    <header class="bg-fendt-dark text-white shadow-xl border-b-4 border-red-600 sticky top-0 z-40">
        <div class="max-w-4xl mx-auto px-4 py-3 flex flex-col sm:flex-row items-center justify-between gap-2">
            <!-- Left Logo -->
            <div class="fendt-badge px-3 py-1 rounded flex items-center gap-2">
                <span class="text-white font-black tracking-widest text-lg font-mono">FENDT</span>
                <span class="w-2 h-4 bg-red-600 inline-block"></span>
            </div>
            
            <!-- Title & User Name -->
            <div class="text-center">
                <h1 class="text-2xl sm:text-3xl font-extrabold tracking-wide text-amber-400 flex items-center justify-center gap-2">
                    <span>🚜🌾</span>
                    <span>L'Agricol-App</span>
                    <span>🌾🚜</span>
                </h1>
                <p class="text-xs text-gray-300 font-medium tracking-wider">
                    <i class="fa-solid font-semibold fa-id-card text-red-400 mr-1"></i>Registro Ore: <span class="font-bold text-white uppercase">WILLIAM NANDO DACCO'</span>
                </p>
            </div>

            <!-- Right Logo -->
            <div class="fendt-badge px-3 py-1 rounded flex items-center gap-2 hidden sm:flex">
                <span class="w-2 h-4 bg-red-600 inline-block"></span>
                <span class="text-white font-black tracking-widest text-lg font-mono">FENDT</span>
            </div>
        </div>
    </header>

    <!-- Navigation Tabs -->
    <div class="max-w-4xl mx-auto px-4 mt-4">
        <div class="flex rounded-lg bg-gray-200 p-1 shadow-inner">
            <button id="tabCompilaBtn" onclick="switchTab('compila')" class="flex-1 py-2 font-bold text-sm sm:text-base rounded-md transition bg-fendt text-white shadow">
                <i class="fa-solid fa-pen-to-square mr-1"></i> Compila
            </button>
            <button id="tabStoricoBtn" onclick="switchTab('storico')" class="flex-1 py-2 font-bold text-sm sm:text-base rounded-md transition text-gray-700">
                <i class="fa-solid fa-list-check mr-1"></i> Storico & Backup
            </button>
            <button id="tabStatsBtn" onclick="switchTab('stats')" class="flex-1 py-2 font-bold text-sm sm:text-base rounded-md transition text-gray-700">
                <i class="fa-solid fa-chart-pie mr-1"></i> Statistiche
            </button>
        </div>
    </div>

    <!-- Main Container -->
    <main class="max-w-4xl mx-auto px-4 mt-6">

        <!-- SECTION 1: COMPILAZIONE GIORNATA -->
        <div id="sectionCompila" class="space-y-6">
            <div class="bg-white rounded-2xl shadow-md border border-gray-200 p-5 sm:p-6">
                <!-- Data Header Card -->
                <div class="flex flex-col sm:flex-row justify-between items-center pb-4 mb-4 border-b border-gray-200">
                    <div class="flex items-center gap-3 mb-2 sm:mb-0">
                        <div class="w-10 h-10 rounded-full bg-emerald-100 text-fendt flex items-center justify-center text-xl">
                            <i class="fa-regular fa-calendar-days"></i>
                        </div>
                        <div>
                            <span class="text-xs text-gray-500 font-bold uppercase tracking-wider">Data Selezionata</span>
                            <h2 id="currentDayString" class="text-lg font-bold text-gray-800 capitalize">---</h2>
                        </div>
                    </div>
                    <input type="date" id="inputData" class="border border-gray-300 rounded-lg px-3 py-1.5 text-sm font-semibold text-gray-700 focus:ring-2 focus:ring-emerald-600 outline-none">
                </div>

                <!-- Form Registrazione -->
                <form id="formGiornata" onsubmit="salvaGiornata(event)" class="space-y-6">
                    <input type="hidden" id="editIndex" value="-1">

                    <!-- Orari -->
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                        <div class="bg-gray-50 p-4 rounded-xl border border-gray-200">
                            <label class="block text-xs font-bold text-gray-600 uppercase mb-1">
                                <i class="fa-regular fa-clock text-green-700 mr-1"></i> Orario Inizio Giornata
                            </label>
                            <div class="flex gap-2">
                                <input type="time" id="oraInizio" required class="w-full text-xl font-bold bg-white border border-gray-300 rounded-lg p-2.5 text-gray-800 focus:ring-2 focus:ring-emerald-600 outline-none">
                                <button type="button" onclick="setOraAttuale('oraInizio')" class="bg-gray-200 hover:bg-gray-300 px-3 rounded-lg text-xs font-bold text-gray-700 transition">Ora</button>
                            </div>
                        </div>

                        <div class="bg-gray-50 p-4 rounded-xl border border-gray-200">
                            <label class="block text-xs font-bold text-gray-600 uppercase mb-1">
                                <i class="fa-solid fa-flag-checkered text-red-600 mr-1"></i> Orario Fine Giornata
                            </label>
                            <div class="flex gap-2">
                                <input type="time" id="oraFine" required class="w-full text-xl font-bold bg-white border border-gray-300 rounded-lg p-2.5 text-gray-800 focus:ring-2 focus:ring-emerald-600 outline-none">
                                <button type="button" onclick="setOraAttuale('oraFine')" class="bg-gray-200 hover:bg-gray-300 px-3 rounded-lg text-xs font-bold text-gray-700 transition">Ora</button>
                            </div>
                        </div>
                    </div>

                    <!-- Pausa Pranzo Checkbox -->
                    <div class="bg-amber-50 border border-amber-200 rounded-xl p-4 flex items-center justify-between">
                        <div class="flex items-center gap-3">
                            <input type="checkbox" id="pausaNonEseguita" class="w-6 h-6 rounded border-gray-300 custom-checkbox cursor-pointer">
                            <div>
                                <label for="pausaNonEseguita" class="font-bold text-gray-800 text-sm sm:text-base cursor-pointer">
                                    Pausa pranzo NON eseguita
                                </label>
                                <p class="text-xs text-gray-500">Se NON barrato, viene normalmente detratta 1 ora di pausa.</p>
                            </div>
                        </div>
                        <i class="fa-solid fa-utensils text-amber-600 text-xl hidden sm:block"></i>
                    </div>

                    <!-- Calcolo Ore Live Preview -->
                    <div class="bg-emerald-50 border border-emerald-200 rounded-xl p-3 flex justify-between items-center">
                        <span class="text-xs font-bold text-emerald-800 uppercase">Ore Calcolate (arrotondate a mezza ora):</span>
                        <span id="liveOreCalcolate" class="text-lg font-black text-fendt">0 ORE</span>
                    </div>

                    <!-- Pannello Dettagli Lavoro -->
                    <div class="space-y-4 pt-2">
                        <div class="flex justify-between items-center">
                            <h3 class="font-extrabold text-gray-800 text-base uppercase tracking-wider flex items-center gap-2">
                                <i class="fa-solid fa-map-location-dot text-fendt"></i> Luogo e Dettagli Lavoro
                            </h3>
                            <span class="text-xs text-red-600 font-bold bg-red-50 px-2 py-1 rounded border border-red-200">* Compila almeno Cliente o Paese</span>
                        </div>

                        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                            <div>
                                <label class="block text-xs font-bold text-gray-700 mb-1">Nome Cliente</label>
                                <input type="text" id="nomeCliente" placeholder="Es. Rossi Mario / Azienda Agricola" class="w-full border border-gray-300 rounded-lg p-2.5 text-sm focus:ring-2 focus:ring-emerald-600 outline-none">
                            </div>
                            <div>
                                <label class="block text-xs font-bold text-gray-700 mb-1">Paese / Località</label>
                                <input type="text" id="paeseLavoro" placeholder="Es. Crema / Lodi" class="w-full border border-gray-300 rounded-lg p-2.5 text-sm focus:ring-2 focus:ring-emerald-600 outline-none">
                            </div>
                        </div>

                        <!-- Lavorazioni Eseguite con Preset Custom -->
                        <div>
                            <div class="flex justify-between items-center mb-1">
                                <label class="block text-xs font-bold text-gray-700">Lavorazioni Eseguite</label>
                                <button type="button" onclick="apriModalNuovaLavorazione()" class="text-xs font-bold text-fendt hover:underline flex items-center gap-1">
                                    <i class="fa-solid fa-circle-plus"></i> + Nuova Lavorazione
                                </button>
                            </div>
                            <input type="text" id="lavorazioniEseguite" placeholder="Es. Aratura, Trinciatura, Semina..." class="w-full border border-gray-300 rounded-lg p-2.5 text-sm focus:ring-2 focus:ring-emerald-600 outline-none mb-2">
                            
                            <!-- Quick Select Chips Lavorazioni -->
                            <div class="flex flex-wrap gap-1.5" id="presetChips">
                                <!-- Popolate dinamicamente da JS -->
                            </div>
                        </div>

                        <div>
                            <label class="block text-xs font-bold text-gray-700 mb-1">Note Aggiuntive</label>
                            <textarea id="noteLavoro" rows="2" placeholder="Eventuali guasti, campo specificato, condizioni meteo..." class="w-full border border-gray-300 rounded-lg p-2.5 text-sm focus:ring-2 focus:ring-emerald-600 outline-none"></textarea>
                        </div>
                    </div>

                    <!-- Bottoni Azione -->
                    <div class="flex gap-3 pt-2">
                        <button type="submit" id="btnSalvaGiornata" class="flex-1 btn-fendt text-white font-black py-4 rounded-xl text-lg flex items-center justify-center gap-2 shadow-lg">
                            <i class="fa-solid fa-floppy-disk"></i> <span>SALVA GIORNATA</span>
                        </button>
                        <button type="button" id="btnAnnullaEdit" onclick="resetForm()" class="hidden bg-gray-400 hover:bg-gray-500 text-white font-bold px-4 rounded-xl text-sm transition">
                            Annulla
                        </button>
                    </div>
                </form>
            </div>
        </div>

        <!-- SECTION 2: STORICO REGISTRO -->
        <div id="sectionStorico" class="hidden space-y-4">
            <div class="bg-white rounded-2xl shadow-md border border-gray-200 p-5 sm:p-6">
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3 mb-6">
                    <div>
                        <h2 class="text-xl font-black text-gray-800 flex items-center gap-2">
                            <i class="fa-solid fa-box-archive text-fendt"></i> Storico Giornate Registrate
                        </h2>
                        <p class="text-xs text-gray-500">Intestatario: <span class="font-bold text-gray-700">WILLIAM NANDO DACCO'</span></p>
                    </div>

                    <!-- Export & Backup Buttons -->
                    <div class="flex flex-wrap gap-2 w-full sm:w-auto">
                        <button onclick="esportaExcel()" class="btn-fendt text-white text-xs font-bold px-3 py-2 rounded-lg flex items-center gap-1.5">
                            <i class="fa-solid fa-file-excel text-emerald-300"></i> EXCEL
                        </button>
                        <button onclick="esportaPDF()" class="btn-red text-white text-xs font-bold px-3 py-2 rounded-lg flex items-center gap-1.5 shadow">
                            <i class="fa-solid fa-file-pdf text-red-200"></i> PDF
                        </button>
                        <button onclick="esportaBackup()" class="btn-blue text-white text-xs font-bold px-3 py-2 rounded-lg flex items-center gap-1.5 shadow" title="Scarica un file di backup dei dati">
                            <i class="fa-solid fa-cloud-arrow-down"></i> Backup
                        </button>
                        <label class="btn-blue text-white text-xs font-bold px-3 py-2 rounded-lg flex items-center gap-1.5 shadow cursor-pointer" title="Ripristina dati da file di backup">
                            <i class="fa-solid fa-cloud-arrow-up"></i> Ripristina
                            <input type="file" id="fileBackupInput" accept=".json" onchange="importaBackup(event)" class="hidden">
                        </label>
                    </div>
                </div>

                <!-- Ricerca rapida -->
                <div class="mb-4">
                    <div class="relative">
                        <i class="fa-solid fa-magnifying-glass absolute left-3 top-3 text-gray-400 text-sm"></i>
                        <input type="text" id="searchRegistro" oninput="renderStorico()" placeholder="Cerca per cliente, paese o lavorazione..." class="w-full pl-9 pr-4 py-2 border border-gray-300 rounded-lg text-sm focus:ring-2 focus:ring-emerald-600 outline-none">
                    </div>
                </div>

                <!-- Lista Registrazioni -->
                <div id="listaStorico" class="space-y-3">
                    <!-- Popolato dinamicamente -->
                </div>
            </div>
        </div>

        <!-- SECTION 3: STATISTICHE -->
        <div id="sectionStats" class="hidden space-y-6">
            <div class="bg-white rounded-2xl shadow-md border border-gray-200 p-5 sm:p-6">
                <h2 class="text-xl font-black text-gray-800 mb-4 flex items-center gap-2">
                    <i class="fa-solid fa-chart-column text-fendt"></i> Statistiche e Resoconti
                </h2>

                <!-- Summary Cards -->
                <div class="grid grid-cols-2 sm:grid-cols-4 gap-3 mb-6">
                    <div class="bg-emerald-50 p-3 rounded-xl border border-emerald-100 text-center">
                        <span class="text-xs text-gray-500 font-bold block">Giornate Totali</span>
                        <span id="statTotGiornate" class="text-2xl font-black text-fendt">0</span>
                    </div>
                    <div class="bg-emerald-50 p-3 rounded-xl border border-emerald-100 text-center">
                        <span class="text-xs text-gray-500 font-bold block">Ore Totali</span>
                        <span id="statTotOre" class="text-2xl font-black text-fendt">0</span>
                    </div>
                    <div class="bg-emerald-50 p-3 rounded-xl border border-emerald-100 text-center">
                        <span class="text-xs text-gray-500 font-bold block">Clienti Serviti</span>
                        <span id="statTotClienti" class="text-2xl font-black text-fendt">0</span>
                    </div>
                    <div class="bg-emerald-50 p-3 rounded-xl border border-emerald-100 text-center">
                        <span class="text-xs text-gray-500 font-bold block">Lavorazioni Diverse</span>
                        <span id="statTotLavorazioni" class="text-2xl font-black text-fendt">0</span>
                    </div>
                </div>

                <!-- Grafico Ore per Periodo -->
                <div class="mb-8">
                    <div class="flex justify-between items-center mb-3">
                        <h3 class="font-bold text-gray-700 text-sm">Grafico Ore Lavorate</h3>
                        <select id="selectPeriodoGrafico" onchange="aggiornaGrafico()" class="text-xs border border-gray-300 rounded px-2 py-1 font-bold">
                            <option value="giorno">Per Giorno</option>
                            <option value="settimana">Per Settimana</option>
                            <option value="mese" selected>Per Mese</option>
                            <option value="anno">Per Anno</option>
                        </select>
                    </div>
                    <div class="w-full h-64 bg-gray-50 rounded-xl p-2 border border-gray-200">
                        <canvas id="chartOre"></canvas>
                    </div>
                </div>

                <!-- Tabelle Contatori -->
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <!-- Giorni per Cliente -->
                    <div>
                        <h3 class="font-bold text-gray-700 text-sm mb-2 flex items-center gap-2">
                            <i class="fa-solid fa-user-group text-fendt"></i> Giorni per Cliente
                        </h3>
                        <div class="border rounded-xl overflow-hidden text-sm">
                            <table class="w-full text-left border-collapse">
                                <thead class="bg-gray-100 text-gray-600 font-bold text-xs">
                                    <tr>
                                        <th class="p-2.5">Cliente</th>
                                        <th class="p-2.5 text-right">Giorni Attivi</th>
                                    </tr>
                                </thead>
                                <tbody id="tableClientiStats" class="divide-y divide-gray-100">
                                    <!-- Popolato JS -->
                                </tbody>
                            </table>
                        </div>
                    </div>

                    <!-- Giorni per Lavorazione -->
                    <div>
                        <h3 class="font-bold text-gray-700 text-sm mb-2 flex items-center gap-2">
                            <i class="fa-solid fa-tractor text-fendt"></i> Giorni per Lavorazione
                        </h3>
                        <div class="border rounded-xl overflow-hidden text-sm">
                            <table class="w-full text-left border-collapse">
                                <thead class="bg-gray-100 text-gray-600 font-bold text-xs">
                                    <tr>
                                        <th class="p-2.5">Lavorazione</th>
                                        <th class="p-2.5 text-right">Giorni Eseguiti</th>
                                    </tr>
                                </thead>
                                <tbody id="tableLavorazioniStats" class="divide-y divide-gray-100">
                                    <!-- Popolato JS -->
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </main>

    <!-- MODAL NUOVA LAVORAZIONE PRESET -->
    <div id="modalLavorazione" class="hidden fixed inset-0 bg-black bg-opacity-50 z-50 flex items-center justify-center p-4">
        <div class="bg-white rounded-2xl max-w-sm w-full p-6 space-y-4 shadow-2xl">
            <h3 class="font-bold text-lg text-gray-800">Crea Nuova Lavorazione Preset</h3>
            <p class="text-xs text-gray-500">Aggiungi una lavorazione frequente da poter selezionare velocemente nei prossimi inserimenti.</p>
            <input type="text" id="nuovaLavorazioneNome" placeholder="Es. Erpicatura, Trinciatura, Trebbiatura..." class="w-full border border-gray-300 rounded-lg p-2.5 text-sm focus:ring-2 focus:ring-emerald-600 outline-none">
            <div class="flex gap-2 justify-end">
                <button type="button" onclick="chiudiModalNuovaLavorazione()" class="px-4 py-2 bg-gray-200 text-gray-700 font-bold rounded-lg text-xs">Annulla</button>
                <button type="button" onclick="salvaNuovaLavorazionePreset()" class="px-4 py-2 btn-fendt text-white font-bold rounded-lg text-xs">Aggiungi</button>
            </div>
        </div>
    </div>

    <!-- SCRIPT LOGICA APPLICAZIONE & SERVICE WORKER -->
    <script>
        // Registrazione Service Worker per abilitare installazione PWA e tasto Installa su Chrome
        if ('serviceWorker' in navigator) {
            const swCode = `
                self.addEventListener('install', (e) => { self.skipWaiting(); });
                self.addEventListener('activate', (e) => { e.waitUntil(clients.claim()); });
                self.addEventListener('fetch', (e) => { e.respondWith(fetch(e.request).catch(() => caches.match(e.request))); });
            `;
            const blob = new Blob([swCode], { type: 'text/javascript' });
            const swUrl = URL.createObjectURL(blob);
            navigator.serviceWorker.register(swUrl).catch(() => {});
        }

        let giornate = JSON.parse(localStorage.getItem('agricol_giornate')) || [];
        let presetLavorazioni = JSON.parse(localStorage.getItem('agricol_presets')) || [
            "Aratura", "Semina", "Trinciatura", "Erpicatura", "Concimazione", "Raccolta / Trebbiatura", "Manutenzione Mezzi"
        ];
        let myChart = null;

        document.addEventListener('DOMContentLoaded', () => {
            const today = new Date().toISOString().split('T')[0];
            const inputData = document.getElementById('inputData');
            inputData.value = today;
            aggiornaStringaData(today);

            inputData.addEventListener('change', (e) => {
                aggiornaStringaData(e.target.value);
            });

            document.getElementById('oraInizio').addEventListener('input', calcolaOreLive);
            document.getElementById('oraFine').addEventListener('input', calcolaOreLive);
            document.getElementById('pausaNonEseguita').addEventListener('change', calcolaOreLive);

            renderPresetChips();
            renderStorico();
        });

        function switchTab(tab) {
            document.getElementById('sectionCompila').classList.add('hidden');
            document.getElementById('sectionStorico').classList.add('hidden');
            document.getElementById('sectionStats').classList.add('hidden');

            document.getElementById('tabCompilaBtn').className = "flex-1 py-2 font-bold text-sm sm:text-base rounded-md transition text-gray-700";
            document.getElementById('tabStoricoBtn').className = "flex-1 py-2 font-bold text-sm sm:text-base rounded-md transition text-gray-700";
            document.getElementById('tabStatsBtn').className = "flex-1 py-2 font-bold text-sm sm:text-base rounded-md transition text-gray-700";

            if (tab === 'compila') {
                document.getElementById('sectionCompila').classList.remove('hidden');
                document.getElementById('tabCompilaBtn').className = "flex-1 py-2 font-bold text-sm sm:text-base rounded-md transition bg-fendt text-white shadow";
            } else if (tab === 'storico') {
                document.getElementById('sectionStorico').classList.remove('hidden');
                document.getElementById('tabStoricoBtn').className = "flex-1 py-2 font-bold text-sm sm:text-base rounded-md transition bg-fendt text-white shadow";
                renderStorico();
            } else if (tab === 'stats') {
                document.getElementById('sectionStats').classList.remove('hidden');
                document.getElementById('tabStatsBtn').className = "flex-1 py-2 font-bold text-sm sm:text-base rounded-md transition bg-fendt text-white shadow";
                renderStatistiche();
            }
        }

        function aggiornaStringaData(dateStr) {
            if(!dateStr) return;
            const parts = dateStr.split('-');
            const d = new Date(parts[0], parts[1]-1, parts[2]);
            const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
            document.getElementById('currentDayString').innerText = d.toLocaleDateString('it-IT', options);
        }

        function setOraAttuale(inputId) {
            const now = new Date();
            const hh = String(now.getHours()).padStart(2, '0');
            const mm = String(now.getMinutes()).padStart(2, '0');
            document.getElementById(inputId).value = `${hh}:${mm}`;
            calcolaOreLive();
        }

        function calcolaOreEfeffettive(oraInizioStr, oraFineStr, pausaSaltata) {
            if (!oraInizioStr || !oraFineStr) return { oreDecimali: 0, testo: "0 ORE" };

            const [hIn, mIn] = oraInizioStr.split(':').map(Number);
            const [hFi, mFi] = oraFineStr.split(':').map(Number);

            let minInizio = hIn * 60 + mIn;
            let minFine = hFi * 60 + mFi;

            if (minFine <= minInizio) {
                minFine += 24 * 60;
            }

            let minutiTotali = minFine - minInizio;

            if (!pausaSaltata) {
                minutiTotali -= 60;
            }

            if (minutiTotali < 0) minutiTotali = 0;

            const oreIntere = Math.floor(minutiTotali / 60);
            const minRestanti = minutiTotali % 60;

            let oreCalcolate = oreIntere;
            let testoFormattato = "";

            if (minRestanti >= 30) {
                oreCalcolate += 0.5;
                testoFormattato = oreIntere === 0 ? "1/2 ORA" : `${oreIntere} ORE E 1/2`;
            } else {
                testoFormattato = `${oreIntere} ORE`;
            }

            return {
                oreDecimali: oreCalcolate,
                testo: testoFormattato,
                minutiEffettivi: minutiTotali
            };
        }

        function calcolaOreLive() {
            const inTime = document.getElementById('oraInizio').value;
            const outTime = document.getElementById('oraFine').value;
            const pausaNonEseguita = document.getElementById('pausaNonEseguita').checked;

            if (inTime && outTime) {
                const res = calcolaOreEfeffettive(inTime, outTime, pausaNonEseguita);
                document.getElementById('liveOreCalcolate').innerText = res.testo;
            } else {
                document.getElementById('liveOreCalcolate').innerText = "0 ORE";
            }
        }

        function renderPresetChips() {
            const container = document.getElementById('presetChips');
            container.innerHTML = "";
            presetLavorazioni.forEach(p => {
                const chip = document.createElement('button');
                chip.type = "button";
                chip.className = "text-xs bg-emerald-50 hover:bg-emerald-100 text-emerald-800 font-bold px-2.5 py-1 rounded-full border border-emerald-200 transition";
                chip.innerText = "+ " + p;
                chip.onclick = () => {
                    const input = document.getElementById('lavorazioniEseguite');
                    if (input.value.trim() === "") {
                        input.value = p;
                    } else if (!input.value.includes(p)) {
                        input.value += ", " + p;
                    }
                };
                container.appendChild(chip);
            });
        }

        function apriModalNuovaLavorazione() {
            document.getElementById('modalLavorazione').classList.remove('hidden');
        }
        function chiudiModalNuovaLavorazione() {
            document.getElementById('modalLavorazione').classList.add('hidden');
            document.getElementById('nuovaLavorazioneNome').value = "";
        }
        function salvaNuovaLavorazionePreset() {
            const nome = document.getElementById('nuovaLavorazioneNome').value.trim();
            if (nome && !presetLavorazioni.includes(nome)) {
                presetLavorazioni.push(nome);
                localStorage.setItem('agricol_presets', JSON.stringify(presetLavorazioni));
                renderPresetChips();
            }
            chiudiModalNuovaLavorazione();
        }

        function salvaGiornata(e) {
            e.preventDefault();

            const cliente = document.getElementById('nomeCliente').value.trim();
            const paese = document.getElementById('paeseLavoro').value.trim();

            if (!cliente && !paese) {
                alert("ATTENZIONE: È necessario inserire almeno il NOME CLIENTE o il PAESE / LOCALITÀ per salvare la giornata.");
                return;
            }

            const data = document.getElementById('inputData').value;
            const oraInizio = document.getElementById('oraInizio').value;
            const oraFine = document.getElementById('oraFine').value;
            const pausaNonEseguita = document.getElementById('pausaNonEseguita').checked;
            const lavorazioni = document.getElementById('lavorazioniEseguite').value.trim();
            const note = document.getElementById('noteLavoro').value.trim();

            const calcolo = calcolaOreEfeffettive(oraInizio, oraFine, pausaNonEseguita);

            const record = {
                id: Date.now(),
                data: data,
                oraInizio: oraInizio,
                oraFine: oraFine,
                pausaNonEseguita: pausaNonEseguita,
                oreDecimali: calcolo.oreDecimali,
                oreTesto: calcolo.testo,
                cliente: cliente || "-",
                paese: paese || "-",
                lavorazioni: lavorazioni || "-",
                note: note || "-"
            };

            const editIdx = parseInt(document.getElementById('editIndex').value);
            if (editIdx >= 0) {
                giornate[editIdx] = record;
            } else {
                giornate.unshift(record);
            }

            localStorage.setItem('agricol_giornate', JSON.stringify(giornate));

            // Messaggio aggiornato senza la prima parola "lavoro"
            mostraToast("LA GIORNATA È STATA SALVATA CORRETTAMENTE OTTIMO LAVORO 🚜💪. A DOMANI...");

            resetForm();
        }

        function mostraToast(messaggio) {
            const toast = document.getElementById('toastNotification');
            const msgEl = document.getElementById('toastMessage');
            msgEl.innerText = messaggio;
            toast.classList.remove('hidden');

            setTimeout(() => {
                toast.classList.add('hidden');
            }, 4500);
        }

        function resetForm() {
            document.getElementById('formGiornata').reset();
            document.getElementById('editIndex').value = "-1";
            document.getElementById('btnSalvaGiornata').innerHTML = '<i class="fa-solid fa-floppy-disk"></i> <span>SALVA GIORNATA</span>';
            document.getElementById('btnAnnullaEdit').classList.add('hidden');
            document.getElementById('liveOreCalcolate').innerText = "0 ORE";

            const today = new Date().toISOString().split('T')[0];
            document.getElementById('inputData').value = today;
            aggiornaStringaData(today);
        }

        function renderStorico() {
            const container = document.getElementById('listaStorico');
            const query = document.getElementById('searchRegistro').value.toLowerCase();

            container.innerHTML = "";

            const filtrate = giornate.filter(g => 
                g.cliente.toLowerCase().includes(query) ||
                g.paese.toLowerCase().includes(query) ||
                g.lavorazioni.toLowerCase().includes(query)
            );

            if (filtrate.length === 0) {
                container.innerHTML = `
                    <div class="text-center py-8 text-gray-400">
                        <i class="fa-solid fa-folder-open text-4xl mb-2"></i>
                        <p class="text-sm">Nessuna giornata registrata trovata.</p>
                    </div>
                `;
                return;
            }

            filtrate.forEach((g) => {
                const realIndex = giornate.findIndex(item => item.id === g.id);

                const card = document.createElement('div');
                card.className = "border border-gray-200 rounded-xl p-4 bg-gray-50 hover:bg-white transition shadow-sm space-y-2";
                
                const parts = g.data.split('-');
                const dataFormattata = `${parts[2]}/${parts[1]}/${parts[0]}`;

                card.innerHTML = `
                    <div class="flex justify-between items-start">
                        <div>
                            <span class="text-xs font-bold text-gray-500 uppercase tracking-wider">${dataFormattata}</span>
                            <h4 class="font-extrabold text-gray-800 text-base">
                                <i class="fa-solid fa-location-dot text-red-600 mr-1"></i> ${g.paese !== '-' ? g.paese : ''} ${g.cliente !== '-' ? '('+g.cliente+')' : ''}
                            </h4>
                        </div>
                        <span class="bg-fendt text-white font-black text-xs px-2.5 py-1 rounded-full shadow-sm">
                            ${g.oreTesto}
                        </span>
                    </div>

                    <div class="text-xs text-gray-600 grid grid-cols-2 gap-2 pt-1 border-t border-gray-200">
                        <div>
                            <i class="fa-regular fa-clock text-gray-400 mr-1"></i> Inizio: <strong>${g.oraInizio}</strong> | Fine: <strong>${g.oraFine}</strong>
                        </div>
                        <div>
                            <i class="fa-solid fa-utensils text-gray-400 mr-1"></i> Pausa Pranzo: <strong>${g.pausaNonEseguita ? 'NON ESEGUITA (NO)' : 'Eseguita (SI)'}</strong>
                        </div>
                    </div>

                    <div class="text-xs text-gray-700">
                        <strong><i class="fa-solid fa-wrench text-gray-400 mr-1"></i> Lavorazioni:</strong> ${g.lavorazioni}
                    </div>

                    ${g.note !== '-' ? `<div class="text-xs text-gray-500 italic bg-white p-2 rounded border border-gray-200">Note: ${g.note}</div>` : ''}

                    <div class="flex justify-end gap-3 pt-2">
                        <button onclick="caricaPerModifica(${realIndex})" class="text-xs font-bold text-fendt hover:underline flex items-center gap-1">
                            <i class="fa-solid fa-pen"></i> Modifica
                        </button>
                        <button onclick="eliminaGiornata(${realIndex})" class="text-xs font-bold text-red-600 hover:underline flex items-center gap-1">
                            <i class="fa-solid fa-trash"></i> Elimina
                        </button>
                    </div>
                `;
                container.appendChild(card);
            });
        }

        function caricaPerModifica(index) {
            const g = giornate[index];
            document.getElementById('editIndex').value = index;
            document.getElementById('inputData').value = g.data;
            aggiornaStringaData(g.data);

            document.getElementById('oraInizio').value = g.oraInizio;
            document.getElementById('oraFine').value = g.oraFine;
            document.getElementById('pausaNonEseguita').checked = g.pausaNonEseguita;
            document.getElementById('nomeCliente').value = g.cliente !== '-' ? g.cliente : '';
            document.getElementById('paeseLavoro').value = g.paese !== '-' ? g.paese : '';
            document.getElementById('lavorazioniEseguite').value = g.lavorazioni !== '-' ? g.lavorazioni : '';
            document.getElementById('noteLavoro').value = g.note !== '-' ? g.note : '';

            calcolaOreLive();

            document.getElementById('btnSalvaGiornata').innerHTML = '<i class="fa-solid fa-arrows-rotate"></i> <span>AGGIORNA GIORNATA</span>';
            document.getElementById('btnAnnullaEdit').classList.remove('hidden');

            switchTab('compila');
        }

        function eliminaGiornata(index) {
            if (confirm("Sei sicuro di voler eliminare questa giornata dal registro?")) {
                giornate.splice(index, 1);
                localStorage.setItem('agricol_giornate', JSON.stringify(giornate));
                renderStorico();
            }
        }

        function esportaBackup() {
            if (giornate.length === 0) {
                alert("Nessun dato da salvare nel backup!");
                return;
            }
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(giornate, null, 2));
            const downloadAnchor = document.createElement('a');
            downloadAnchor.setAttribute("href", dataStr);
            downloadAnchor.setAttribute("download", `Backup_AgricolApp_${new Date().toISOString().split('T')[0]}.json`);
            document.body.appendChild(downloadAnchor);
            downloadAnchor.click();
            downloadAnchor.remove();
        }

        function importaBackup(event) {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const importedData = JSON.parse(e.target.result);
                    if (Array.isArray(importedData)) {
                        if (confirm(`Trovate ${importedData.length} registrazioni nel backup. Vuoi sovrascrivere o unire i dati esistenti?\nPremi OK per SOVRASCRIVERE o Annulla per unire.`)) {
                            giornate = importedData;
                        } else {
                            const existingIds = new Set(giornate.map(g => g.id));
                            importedData.forEach(item => {
                                if (!existingIds.has(item.id)) {
                                    giornate.push(item);
                                }
                            });
                        }
                        localStorage.setItem('agricol_giornate', JSON.stringify(giornate));
                        renderStorico();
                        alert("Backup ripristinato con successo!");
                    } else {
                        alert("Il file di backup non è valido.");
                    }
                } catch (err) {
                    alert("Errore durante la lettura del file di backup.");
                }
            };
            reader.readAsText(file);
            event.target.value = "";
        }

        function esportaExcel() {
            if (giornate.length === 0) {
                alert("Nessun dato presente nel registro da esportare!");
                return;
            }

            const header = ["GIORNO / DATA", "ORARIO INIZIO", "ORARIO FINE", "PAUSA PRANZO (SI/NO)", "N ORE TOTALI", "LOCALITA / CLIENTE", "LAVORAZIONI ESEGUITE"];
            const rows = [];
            let totaleOreDec = 0;

            const ordinate = [...giornate].reverse();

            ordinate.forEach(g => {
                totaleOreDec += g.oreDecimali;

                const locCliente = [g.paese !== '-' ? g.paese : '', g.cliente !== '-' ? g.cliente : ''].filter(Boolean).join(' - ') || '-';
                const pausaStr = g.pausaNonEseguita ? 'NO' : 'SI';

                rows.push([
                    g.data,
                    g.oraInizio,
                    g.oraFine,
                    pausaStr,
                    g.oreTesto,
                    locCliente,
                    g.lavorazioni !== '-' ? g.lavorazioni : '-'
                ]);
            });

            const oreInt = Math.floor(totaleOreDec);
            const haMezza = (totaleOreDec % 1) >= 0.5;
            const totaleTesto = haMezza ? `${oreInt} ORE E 1/2` : `${oreInt} ORE`;

            rows.push([]);
            rows.push(["INTESTARIO:", "WILLIAM NANDO DACCO'", "", "", "", "", ""]);
            rows.push(["TOTALE COMPLESSIVO ORE:", "", "", "", totaleTesto, "", ""]);

            const wsData = [
                ["FENDT - L'AGRICOL-APP - REGISTRO ORE AGRICOLA"],
                ["INTESTARIO: WILLIAM NANDO DACCO'"],
                [],
                header,
                ...rows
            ];

            const ws = XLSX.utils.aoa_to_sheet(wsData);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "Registro Ore");

            XLSX.writeFile(wb, `Ore_Agricola_William_Nando_Dacco.xlsx`);
        }

        function esportaPDF() {
            if (giornate.length === 0) {
                alert("Nessun dato presente nel registro da esportare!");
                return;
            }

            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();

            doc.setFillColor(30, 66, 32);
            doc.rect(0, 0, 210, 25, 'F');

            doc.setTextColor(255, 255, 255);
            doc.setFontSize(16);
            doc.setFont("helvetica", "bold");
            doc.text("EXPORT ORE AGRICOLA 78", 14, 12);
            doc.setFontSize(11);
            doc.text("INTESTARIO: WILLIAM NANDO DACCO'", 14, 19);

            const tableColumn = ["GIORNO", "INIZIO", "FINE", "PAUSA", "N ORE TOTALI", "LOCALITÀ / CLIENTE", "LAVORAZIONI"];
            const tableRows = [];
            let totaleOreDec = 0;

            const ordinate = [...giornate].reverse();

            ordinate.forEach(g => {
                totaleOreDec += g.oreDecimali;

                const parts = g.data.split('-');
                const dataFormatted = `${parts[2]}/${parts[1]}/${parts[0]}`;
                const locCliente = [g.paese !== '-' ? g.paese : '', g.cliente !== '-' ? g.cliente : ''].filter(Boolean).join(' - ') || '-';
                const pausaStr = g.pausaNonEseguita ? 'NO' : 'SI';

                tableRows.push([
                    dataFormatted,
                    g.oraInizio,
                    g.oraFine,
                    pausaStr,
                    g.oreTesto,
                    locCliente,
                    g.lavorazioni !== '-' ? g.lavorazioni : '-'
                ]);
            });

            const oreInt = Math.floor(totaleOreDec);
            const haMezza = (totaleOreDec % 1) >= 0.5;
            const totaleTesto = haMezza ? `${oreInt} ORE E 1/2` : `${oreInt} ORE`;

            doc.autoTable({
                head: [tableColumn],
                body: tableRows,
                startY: 30,
                theme: 'grid',
                headStyles: { fillColor: [46, 99, 48], textColor: [255, 255, 255], fontStyle: 'bold' },
                styles: { fontSize: 8, cellPadding: 2.5 },
                columnStyles: {
                    0: { cellWidth: 22 },
                    1: { cellWidth: 15 },
                    2: { cellWidth: 15 },
                    3: { cellWidth: 15 },
                    4: { cellWidth: 28, fontStyle: 'bold' },
                    5: { cellWidth: 45 },
                    6: { cellWidth: 'auto' }
                }
            });

            const finalY = doc.lastAutoTable.finalY || 40;

            doc.setFillColor(240, 245, 240);
            doc.rect(14, finalY + 5, 182, 12, 'F');
            doc.setDrawColor(46, 99, 48);
            doc.rect(14, finalY + 5, 182, 12, 'S');

            doc.setTextColor(30, 66, 32);
            doc.setFontSize(10);
            doc.setFont("helvetica", "bold");
            doc.text(`TOTALE COMPLESSIVO ORE REGISTRATE: ${totaleTesto}`, 18, finalY + 12);

            doc.save(`Export_Ore_Agricola_78_William_Nando_Dacco.pdf`);
        }

        function renderStatistiche() {
            let totOre = 0;
            const clientiSet = new Set();
            const lavSet = new Set();
            const clientiCounts = {};
            const lavCounts = {};

            giornate.forEach(g => {
                totOre += g.oreDecimali;
                if (g.cliente && g.cliente !== '-') {
                    clientiSet.add(g.cliente);
                    clientiCounts[g.cliente] = (clientiCounts[g.cliente] || 0) + 1;
                }
                if (g.lavorazioni && g.lavorazioni !== '-') {
                    const lList = g.lavorazioni.split(',').map(s => s.trim());
                    lList.forEach(l => {
                        lavSet.add(l);
                        lavCounts[l] = (lavCounts[l] || 0) + 1;
                    });
                }
            });

            document.getElementById('statTotGiornate').innerText = giornate.length;
            document.getElementById('statTotOre').innerText = totOre % 1 >= 0.5 ? `${Math.floor(totOre)} ½` : Math.floor(totOre);
            document.getElementById('statTotClienti').innerText = clientiSet.size;
            document.getElementById('statTotLavorazioni').innerText = lavSet.size;

            const tClienti = document.getElementById('tableClientiStats');
            tClienti.innerHTML = "";
            Object.keys(clientiCounts).forEach(c => {
                tClienti.innerHTML += `
                    <tr>
                        <td class="p-2.5 font-semibold text-gray-800">${c}</td>
                        <td class="p-2.5 text-right font-bold text-fendt">${clientiCounts[c]} gg</td>
                    </tr>
                `;
            });

            const tLav = document.getElementById('tableLavorazioniStats');
            tLav.innerHTML = "";
            Object.keys(lavCounts).forEach(l => {
                tLav.innerHTML += `
                    <tr>
                        <td class="p-2.5 font-semibold text-gray-800">${l}</td>
                        <td class="p-2.5 text-right font-bold text-fendt">${lavCounts[l]} gg</td>
                    </tr>
                `;
            });

            aggiornaGrafico();
        }

        function aggiornaGrafico() {
            const periodo = document.getElementById('selectPeriodoGrafico').value;
            const dataMap = {};

            giornate.forEach(g => {
                let key = g.data;
                const d = new Date(g.data);

                if (periodo === 'settimana') {
                    const year = d.getFullYear();
                    const firstDayOfYear = new Date(year, 0, 1);
                    const pastDaysOfYear = (d - firstDayOfYear) / 86400000;
                    const weekNum = Math.ceil((pastDaysOfYear + firstDayOfYear.getDay() + 1) / 7);
                    key = `Sett. ${weekNum} (${year})`;
                } else if (periodo === 'mese') {
                    key = d.toLocaleDateString('it-IT', { month: 'short', year: 'numeric' });
                } else if (periodo === 'anno') {
                    key = `${d.getFullYear()}`;
                }

                dataMap[key] = (dataMap[key] || 0) + g.oreDecimali;
            });

            const labels = Object.keys(dataMap).reverse();
            const values = Object.values(dataMap).reverse();

            const ctx = document.getElementById('chartOre').getContext('2d');
            if (myChart) myChart.destroy();

            myChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: labels,
                    datasets: [{
                        label: 'Ore Lavorate',
                        data: values,
                        backgroundColor: '#2e6330',
                        borderRadius: 6
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { display: false }
                    },
                    scales: {
                        y: { beginAtZero: true }
                    }
                }
            });
        }
    </script>
</body>
</html>
