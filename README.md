# -nibus-simulator-nos-estados-do-Brasil-
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🚌 Ônibus Simulator - Estados do Brasil</title>
    <style>
        :root {
            --bg-dark: #0d0d0d;
            --panel-bg: #1a1a1a;
            --card-bg: #252525;
            --accent: #ff8c00;
            --text: #e0e0e0;
            --font: 'Segoe UI', sans-serif;
        }

        body, html {
            margin: 0;
            padding: 0;
            width: 100vw;
            height: 100vh;
            font-family: var(--font);
            color: var(--text);
            overflow: hidden;
            background: var(--bg-dark);
            user-select: none;
        }

        /* Layout do Menu */
        #menu-screen {
            display: flex;
            width: 100vw;
            height: 100vh;
        }

        .sidebar {
            width: 80px;
            background: #111;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding-top: 20px;
            gap: 20px;
            border-right: 2px solid #333;
        }

        .btn-nav {
            font-size: 28px;
            cursor: pointer;
            padding: 12px;
            border-radius: 12px;
            color: #666;
            transition: 0.3s;
            background: none;
            border: none;
        }

        .btn-nav.active, .btn-nav:hover {
            color: var(--accent);
            background: #222;
        }

        .main-center {
            flex: 1;
            display: flex;
            flex-direction: column;
            padding: 30px;
            background: linear-gradient(135deg, #0d0d0d, #1a1a1a);
            justify-content: space-between;
        }

        .header-loc {
            font-size: 26px;
            font-weight: bold;
            letter-spacing: 1px;
            text-transform: uppercase;
        }

        #bus-preview {
            width: 100%;
            max-width: 500px;
            height: 280px;
            background: #000;
            border: 2px solid #444;
            border-radius: 15px;
            margin: 0 auto;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 80px;
            box-shadow: inset 0 0 20px rgba(0,0,0,0.8);
        }

        .line-indicator {
            font-size: 24px;
            color: var(--accent);
            text-align: center;
            font-weight: bold;
            background: #222;
            padding: 10px;
            border-radius: 8px;
            border-left: 5px solid var(--accent);
        }

        #btn-start {
            background: var(--accent);
            color: white;
            padding: 18px 60px;
            border: none;
            border-radius: 50px;
            font-size: 22px;
            font-weight: bold;
            cursor: pointer;
            align-self: center;
            box-shadow: 0 5px 20px rgba(255,140,0,0.4);
            transition: 0.2s;
        }

        #btn-start:active {
            transform: scale(0.95);
        }

        .right-panel {
            width: 320px;
            background: var(--panel-bg);
            border-left: 2px solid #333;
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        .panel-title {
            font-size: 18px;
            font-weight: bold;
            color: #888;
            margin-bottom: 5px;
        }

        .card {
            background: var(--card-bg);
            padding: 15px;
            border-radius: 10px;
            cursor: pointer;
            border: 2px solid transparent;
            transition: 0.2s;
        }

        .card:hover, .card.selected {
            border-color: var(--accent);
            background: #2a2a2a;
        }

        /* Tela de Jogo (Simulação Básica) */
        #game-screen {
            display: none;
            width: 100vw;
            height: 100vh;
            background: #444;
            position: relative;
            align-items: center;
            justify-content: center;
        }

        .controls {
            position: absolute;
            bottom: 20px;
            width: 100%;
            display: flex;
            justify-content: space-between;
            padding: 0 40px;
            box-sizing: border-box;
        }

        .btn-game {
            background: rgba(0,0,0,0.6);
            color: white;
            border: 2px solid #fff;
            padding: 20px;
            border-radius: 50%;
            font-size: 24px;
            width: 70px;
            height: 70px;
            cursor: pointer;
        }

        #btn-back {
            position: absolute;
            top: 20px;
            left: 20px;
        }
    </style>
</head>
<body>

    <!-- TELA DE MENU -->
    <div id="menu-screen">
        <div class="sidebar">
            <button class="btn-nav active" title="Garagem">🚌</button>
            <button class="btn-nav" title="Mapas">🗺️</button>
            <button class="btn-nav" title="Configurações">⚙️</button>
        </div>

        <div class="main-center">
            <div class="header-loc">📍 Brasil - Simulador</div>
            
            <div id="bus-preview">🚌</div>
            
            <div class="line-indicator" id="current-line">
                Linha: Carregando...
            </div>
            
            <button id="btn-start" onclick="startGame()">JOGAR</button>
        </div>

        <div class="right-panel">
            <div class="panel-title">Selecione a Rota</div>
            <div class="card selected" onclick="selectRoute('Linha 102 - Av. Paulista (SP)', '🚌')">
                <strong>São Paulo</strong>
                <div style="font-size: 14px; color: #aaa; margin-top: 5px;">Linha 102 - Av. Paulista</div>
            </div>
            <div class="card" onclick="selectRoute('Linha 305 - Copacabana (RJ)', '🚎')">
                <strong>Rio de Janeiro</strong>
                <div style="font-size: 14px; color: #aaa; margin-top: 5px;">Linha 305 - Copacabana</div>
            </div>
            <div class="card" onclick="selectRoute('Linha 501 - Centro (MG)', '🚍')">
                <strong>Minas Gerais</strong>
                <div style="font-size: 14px; color: #aaa; margin-top: 5px;">Linha 501 - Centro Histórico</div>
            </div>
        </div>
    </div>

    <!-- TELA DE JOGO -->
    <div id="game-screen">
        <button class="btn-game" id="btn-back" onclick="goToMenu()">⬅️</button>
        <h2 id="game-info" style="position: absolute; top: 20px; text-align: center; width: 100%;">Dirigindo...</h2>
        
        <div class="controls">
            <button class="btn-game">🔄</button> <!-- Volante/Direção -->
            <div>
                <button class="btn-game" style="background: #900;">🛑</button> <!-- Freio -->
                <button class="btn-game" style="background: #090;">⚡</button> <!-- Acelerador -->
            </div>
        </div>
    </div>

    <script>
        let selectedLine = "Linha 102 - Av. Paulista (SP)";

        function selectRoute(lineName, busEmoji) {
            selectedLine = lineName;
            document.getElementById('current-line').innerText = "Linha: " + lineName;
            document.getElementById('bus-preview').innerText = busEmoji;
            
            // Remove seleção visual dos outros cards
            document.querySelectorAll('.card').forEach(card => card.classList.remove('selected'));
            // Adiciona seleção ao card clicado
            event.currentTarget.classList.add('selected');
        }

        function startGame() {
            document.getElementById('menu-screen').style.display = 'none';
            document.getElementById('game-screen').style.display = 'flex';
            document.getElementById('game-info').innerText = "Simulando: " + selectedLine;
        }

        function goToMenu() {
            document.getElementById('game-screen').style.display = 'none';
            document.getElementById('menu-screen').style.display = 'flex';
        }

        // Inicializa com a primeira linha
        selectRoute('Linha 102 - Av. Paulista (SP)', '🚌');
    </style>
</body>
</html>
