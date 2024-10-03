# PC-portables-
Windows 10 portable 
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulateur de PC Virtuel</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div id="desktop" oncontextmenu="showContextMenu(event); return false;" onclick="hideContextMenu()">
        <!-- Menu Démarrer -->
        <div id="start-menu">
            <div class="start-button" onclick="toggleStartMenu()">&#x2630;</div>
            <div id="menu-content">
                <h3>Menu Démarrer</h3>
                <ul>
                    <li onclick="openApp('Calendar')">Calendrier</li>
                    <li onclick="openApp('Gmail')">Gmail</li>
                    <li onclick="openApp('Microsoft Edge')">Microsoft Edge</li>
                    <li onclick="openApp('Firefox')">Firefox</li>
                    <li onclick="openApp('WhatsApp')">WhatsApp</li> <!-- Ajout de WhatsApp -->
                    <li onclick="openApp('Settings')">Paramètres</li>
                </ul>
            </div>
        </div>
        <!-- Icônes sur le bureau -->
        <div id="firefox-icon" class="icon" ondblclick="openApp('Firefox')">🦊 Firefox</div>
        <!-- Fenêtre Simulée -->
        <div id="window" class="window hidden">
            <div class="title-bar">
                <span id="app-title">App</span>
                <button onclick="closeWindow()">X</button>
            </div>
            <div class="window-content" id="window-content"></div>
        </div>
        <!-- Menu contextuel (clic droit) -->
        <div id="context-menu" class="context-menu hidden">
            <ul>
                <li onclick="openApp('Properties')">Propriétés</li>
                <li onclick="openApp('Open')">Ouvrir</li>
                <li onclick="deleteFile()">Supprimer</li>
            </ul>
        </div>
        <!-- Paramètres -->
        <div id="settings-window" class="window hidden">
            <div class="title-bar">
                <span>Paramètres</span>
                <button onclick="closeWindow()">X</button>
            </div>
            <div class="window-content">
                <h4>Paramètres de Réseau</h4>
                <p>Configurer votre réseau : Wi-Fi, Ethernet, etc.</p>
                <hr>
                <h4>Options d'Alimentation</h4>
                <button onclick="restartPC()">Redémarrer</button>
                <button onclick="shutdownPC()">Éteindre</button>
                <hr>
                <h4>Clavier Virtuel</h4>
                <button onclick="showKeyboard()">Afficher le Clavier</button>
            </div>
        </div>
        <!-- Clavier virtuel -->
        <div id="virtual-keyboard" class="keyboard hidden">
            <div class="row">
                <button onclick="typeKey('Q')">Q</button>
                <button onclick="typeKey('W')">W</button>
                <button onclick="typeKey('E')">E</button>
            </div>
            <div class="row">
                <button onclick="typeKey('A')">A</button>
                <button onclick="typeKey('S')">S</button>
                <button onclick="typeKey('D')">D</button>
            </div>
            <div class="row">
                <button onclick="typeKey(' ')">Espace</button>
                <button onclick="hideKeyboard()">Fermer</button>
            </div>
        </div>
    </div>
    <script src="script.js"></script>
</body>
</html>
body {
    background-color: #3b5998; /* Couleur de fond de l'interface */
    color: white;
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
}

#desktop {
    width: 100vw;
    height: 100vh;
    position: relative;
}

.icon {
    width: 60px;
    height: 60px;
    text-align: center;
    line-height: 60px;
    background-color: rgba(0, 0, 0, 0.5);
    border-radius: 8px;
    margin: 10px;
    display: inline-block;
    cursor: pointer;
}

.window {
    width: 400px;
    height: 300px;
    background-color: white;
    color: black;
    position: absolute;
    top: 100px;
    left: 100px;
    border: 1px solid #000;
    z-index: 10;
}

.title-bar {
    background-color: #f0f0f0;
    padding: 5px;
    cursor: move;
}

.hidden {
    display: none;
}

.context-menu {
    position: absolute;
    background-color: white;
    border: 1px solid #000;
    z-index: 20;
}

.context-menu ul {
    list-style-type: none;
    padding: 5px;
}

.context-menu li {
    padding: 5px;
    cursor: pointer;
}

.context-menu li:hover {
    background-color: #e0e0e0;
}

.keyboard {
    position: absolute;
    bottom: 20px;
    left: 20px;
    background-color: rgba(255, 255, 255, 0.9);
    border: 1px solid #000;
    padding: 10px;
    border-radius: 10px;
}
// Simulateur de mémoire et stockage
const TOTAL_MEMORY = 600 * 1024 * 1024 * 1024; // 600 GB pour le stockage
const TOTAL_RAM = 36 * 1024; // 36 GB de RAM en MB
let usedMemory = 0; // Mémoire utilisée (stockage interne)
let usedRAM = 0; // RAM utilisée pour les applications ouvertes
let openApps = []; // Liste des applications ouvertes
let storage = []; // Stockage des fichiers

function openApp(appName) {
    if (!openApps.includes(appName)) {
        openApps.push(appName);
        updateRAMUsage(appName);
        showAppWindow(appName);
    }
}

function updateRAMUsage(appName) {
    const appRAMUsage = 512; // Exemple : 512 MB par application
    usedRAM += appRAMUsage;

    if (usedRAM > TOTAL_RAM) {
        alert('RAM insuffisante. Fermez certaines applications.');
        closeApp(appName);
    } else {
        console.log(`RAM utilisée : ${usedRAM} MB / ${TOTAL_RAM} MB`);
    }
}

function closeApp(appName) {
    const appIndex = openApps.indexOf(appName);
    if (appIndex !== -1) {
        openApps.splice(appIndex, 1);
        releaseRAM(appName);
        hideAppWindow();
    }
}

function releaseRAM(appName) {
    const appRAMUsage = 512;
    usedRAM -= appRAMUsage;
    console.log(`RAM libérée : ${usedRAM} MB / ${TOTAL_RAM} MB`);
}

function showAppWindow(appName) {
    const windowContent = document.getElementById('window-content');
    const windowElement = document.getElementById('window');
    const appTitle = document.getElementById('app-title');

    appTitle.textContent = appName;
    windowContent.textContent = `Bienvenue dans ${appName}!`;
    windowElement.classList.remove('hidden');
}

function hideAppWindow() {
    const windowElement = document.getElementById('window');
    windowElement.classList.add('hidden');
}

function typeKey(key) {
    const windowContent = document.getElementById('window-content');
    windowContent.textContent += key; // Ajoute la touche au contenu de la fenêtre
}

function showKeyboard() {
    const keyboard = document.getElementById('virtual-keyboard');
    keyboard.classList.remove('hidden');
}

function hideKeyboard() {
    const keyboard = document.getElementById('virtual-keyboard');
    keyboard.classList.add('hidden
    
