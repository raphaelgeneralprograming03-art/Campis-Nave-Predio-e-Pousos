
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador Tático: Zero-Latency Stream & Sensor-to-Shooter</title>
    <style>
        :root {
            --bg-color: #020617;
            --panel-bg: rgba(15, 23, 42, 0.9);
            --border-color: #06b6d4;
            --accent-green: #10b981;
            --accent-red: #ef4444;
            --accent-amber: #f59e0b;
            --text-main: #cbd5e1;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Courier New', Courier, monospace;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            height: 100vh;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        header {
            background: linear-gradient(90deg, #020617, #0f172a, #020617);
            border-bottom: 2px solid var(--border-color);
            padding: 10px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        header h1 {
            font-size: 1.05rem;
            color: var(--border-color);
            letter-spacing: 2px;
        }

        .badge-classification {
            background: var(--accent-red);
            color: #fff;
            padding: 4px 10px;
            font-size: 0.7rem;
            font-weight: bold;
            border-radius: 3px;
            letter-spacing: 1px;
            animation: pulse 2s infinite;
        }

        .main-container {
            display: flex;
            flex: 1;
            gap: 15px;
            padding: 15px;
            overflow: hidden;
        }

        /* Painel de Controle Esquerdo */
        .control-panel {
            width: 340px;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 15px;
            display: flex;
            flex-direction: column;
            gap: 12px;
            box-shadow: 0 0 15px rgba(6, 182, 212, 0.15);
        }

        .panel-section h3 {
            font-size: 0.8rem;
            color: var(--border-color);
            margin-bottom: 6px;
            border-bottom: 1px solid rgba(6, 182, 212, 0.3);
            padding-bottom: 3px;
            letter-spacing: 1px;
        }

        .btn-group {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        button {
            background: rgba(6, 182, 212, 0.1);
            border: 1px solid var(--border-color);
            color: var(--text-main);
            padding: 8px 10px;
            text-align: left;
            font-size: 0.75rem;
            cursor: pointer;
            border-radius: 4px;
            transition: all 0.3s ease;
        }

        button:hover {
            background: rgba(6, 182, 212, 0.3);
            color: #fff;
            box-shadow: 0 0 8px var(--border-color);
        }

        button.active {
            background: var(--border-color);
            color: var(--bg-color);
            font-weight: bold;
        }

        button.danger-btn {
            border-color: var(--accent-red);
            background: rgba(239, 68, 68, 0.1);
            color: #fca5a5;
        }
        button.danger-btn.active {
            background: var(--accent-red);
            color: #fff;
        }

        /* Área do Canvas Central */
        .viewport-container {
            flex: 1;
            background: #010409;
            border: 1px solid var(--border-color);
            border-radius: 6px;
            display: flex;
            flex-direction: column;
            position: relative;
            overflow: hidden;
        }

        .viewport-header {
            background: rgba(6, 182, 212, 0.05);
            padding: 6px 12px;
            font-size: 0.7rem;
            display: flex;
            justify-content: space-between;
            border-bottom: 1px solid rgba(6, 182, 212, 0.2);
        }

        canvas {
            flex: 1;
            display: block;
            width: 100%;
            height: 100%;
        }

        /* Painel de Telemetria Direito */
        .telemetry-panel {
            width: 290px;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 15px;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .metric-box {
            background: rgba(2, 6, 23, 0.7);
            border: 1px solid rgba(6, 182, 212, 0.3);
            border-radius: 4px;
            padding: 8px;
        }

        .metric-title {
            font-size: 0.65rem;
            color: var(--border-color);
            margin-bottom: 3px;
        }

        .metric-value {
            font-size: 1rem;
            font-weight: bold;
            color: #fff;
        }

        .status-ok { color: var(--accent-green); }
        .status-alert { color: var(--accent-red); }
        .status-warn { color: var(--accent-amber); }

        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.4; }
            100% { opacity: 1; }
        }
    </style>
</head>
<body>

    <header>
        <h1>PROJETO ESPELHAMENTO SÍNCRONO: ZERO-LATENCY STREAM</h1>
        <div class="badge-classification">SISTEMA TÁTICO DE ARTILHARIA</div>
    </header>

    <div class="main-container">
        <!-- Controles -->
        <div class="control-panel">
            <div class="panel-section">
                <h3>1. GUERRA ELETRÔNICA (EW)</h3>
                <div class="btn-group">
                    <button id="btn-ew" class="danger-btn" onclick="toggleEW()">Ativar Bloqueio GPS (Spoofing Russo)</button>
                </div>
            </div>

            <div class="panel-section">
                <h3>2. AÇÃO SENSOR-TO-SHOOTER</h3>
                <div class="btn-group">
                    <button onclick="triggerArtilleryStrike()">Disparar Fogo de Contrabateria Imediato</button>
                    <button onclick="spawnNewTarget()">Simular Nova Posição Inimiga</button>
                </div>
            </div>

            <div class="panel-section">
                <h3>3. SEGURANÇA DE TRANSMISSÃO</h3>
                <div class="btn-group">
                    <button id="btn-stealth" class="active" onclick="toggleStealth()">Modo Passivo (Ocultação de Emissão)</button>
                    <button onclick="resetSimulation()">Reiniciar Simulação</button>
                </div>
            </div>

            <div class="panel-section">
                <h3>ARQUITETURA DE REDE</h3>
                <div style="font-size: 0.7rem; line-height: 1.3; color: #94a3b8;">
                    • Drone: Vídeo Bruto (Sem Telemetria RF)<br>
                    • Central: Cálculo Óptico & NPU<br>
                    • Campo: Receptor 100% Passivo
                </div>
            </div>
        </div>

        <!-- Visualizador Canvas -->
        <div class="viewport-container">
            <div class="viewport-header">
                <span id="view-status">STATUS: DRONE EM VOO ESTÁVEL [FLUXO AO VIVO]</span>
                <span id="latency-display">LATÊNCIA SÍNCRONA: 0.38 ms</span>
            </div>
            <canvas id="simCanvas"></canvas>
        </div>

        <!-- Telemetria -->
        <div class="telemetry-panel">
            <div class="panel-section">
                <h3>TELEMETRIA DE COMBATE</h3>
            </div>
            <div class="metric-box">
                <div class="metric-title">ESTADO DO GPS (SATELLITE)</div>
                <div class="metric-value status-ok" id="stat-gps">CONECTADO</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">NAVEGAÇÃO ÓPTICA (TELESCÓPIO)</div>
                <div class="metric-value status-ok" id="stat-optical">ATIVO (100% PRECISÃO)</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">TEMPO SENSOR-TO-SHOOTER</div>
                <div class="metric-value status-ok" id="stat-sts">0.8 SEGUNDOS</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">ESTADO DE TRIANGULAÇÃO INIMIGA</div>
                <div class="metric-value status-ok" id="stat-stealth">OCULTO (NÃO LOCALIZADO)</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">ALVOS DE CONTRABATERIA</div>
                <div class="metric-value" id="stat-targets">1 NEUTRALIZADO</div>
            </div>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        let ewActive = false;
        let stealthActive = true;
        let targetsNeutralized = 1;

        // Posições no mapa simulado
        let drone = { x: 300, y: 150, targetX: 300, targetY: 150 };
        let enemyArtillery = { x: 550, y: 220, firing: true, flashTimer: 0 };
        let friendlyBattery = { x: 120, y: 350 };
        let projectiles = [];

        function resizeCanvas() {
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight - 30;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        function toggleEW() {
            ewActive = !ewActive;
            let btn = document.getElementById('btn-ew');
            if (ewActive) {
                btn.classList.add('active');
                document.getElementById('stat-gps').innerText = 'BLOQUEADO / SPOOFING';
                document.getElementById('stat-gpsclassName = metric-value status-alert';
                document.getElementById('stat-gps').className = 'metric-value status-alert';
                document.getElementById('view-status').innerText = 'STATUS: GPS INTERFERIDO! ALTERNANDO AUTOMATICAMENTE PARA NAVEGAÇÃO ÓPTICA';
                document.getElementById('view-status').style.color = 'var(--accent-amber)';
            } else {
                btn.classList.remove('active');
                document.getElementById('stat-gps').innerText = 'CONECTADO';
                document.getElementById('stat-gps').className = 'metric-value status-ok';
                document.getElementById('view-status').innerText = 'STATUS: DRONE EM VOO ESTÁVEL [FLUXO AO VIVO]';
                document.getElementById('view-status').style.color = 'var(--text-main)';
            }
        }

        function toggleStealth() {
            stealthActive = !stealthActive;
            let btn = document.getElementById('btn-stealth');
            if (stealthActive) {
                btn.classList.add('active');
                document.getElementById('stat-stealth').innerText = 'OCULTO (NÃO LOCALIZADO)';
                document.getElementById('stat-stealth').className = 'metric-value status-ok';
            } else {
                btn.classList.remove('active');
                document.getElementById('stat-stealth').innerText = 'ALERTA: SINAL DE RÁDIO VULNERÁVEL!';
                document.getElementById('stat-stealth').className = 'metric-value status-alert';
            }
        }

        function triggerArtilleryStrike() {
            projectiles.push({
                x: friendlyBattery.x,
                y: friendlyBattery.y,
                destX: enemyArtillery.x,
                destY: enemyArtillery.y,
                progress: 0
            });
            document.getElementById('stat-sts').innerText = '0.4s (IMEDIATO)';
        }

        function spawnNewTarget() {
            enemyArtillery.x = 200 + Math.random() * (canvas.width - 300);
            enemyArtillery.y = 100 + Math.random() * (canvas.height - 200);
            enemyArtillery.firing = true;
        }

        function resetSimulation() {
            projectiles = [];
            targetsNeutralized = 0;
            document.getElementById('stat-targets').innerText = targetsNeutralized;
            spawnNewTarget();
        }

        // Loop Principal de Renderização Canvas 2D
        function animate() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // 1. Desenhar grade tática do terreno
            ctx.strokeStyle = 'rgba(6, 182, 212, 0.05)';
            ctx.lineWidth = 1;
            let gridSize = 50;
            for (let x = 0; x < canvas.width; x += gridSize) {
                ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, canvas.height); ctx.stroke();
            }
            for (let y = 0; y < canvas.height; y += gridSize) {
                ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(canvas.width, y); ctx.stroke();
            }

            // 2. Movimentação suave do Drone
            drone.x += (drone.targetX - drone.x) * 0.05;
            drone.y += (drone.targetY - drone.y) * 0.05;
            if (Math.abs(drone.x - drone.targetX) < 5) {
                drone.targetX = 150 + Math.random() * (canvas.width - 300);
                drone.targetY = 80 + Math.random() * (canvas.height - 200);
            }

            // 3. Desenhar Campo de Visão Óptico do Drone (Telescópio Calculador)
            ctx.strokeStyle = 'rgba(6, 182, 212, 0.25)';
            ctx.lineWidth = 1.5;
            ctx.beginPath();
            ctx.arc(drone.x, drone.y, 120, 0, Math.PI * 2);
            ctx.stroke();

            // Linha de mira síncrona Central -> Alvo
            ctx.strokeStyle = ewActive ? 'rgba(245, 158, 11, 0.6)' : 'rgba(16, 185, 129, 0.6)';
            ctx.setLineDash([4, 4]);
            ctx.beginPath();
            ctx.moveTo(drone.x, drone.y);
            ctx.lineTo(enemyArtillery.x, enemyArtillery.y);
            ctx.stroke();
            ctx.setLineDash([]); // Reset dash

            // 4. Desenhar Artilharia Inimiga (Alvo)
            enemyArtillery.flashTimer += 0.1;
            let isFlashing = Math.sin(enemyArtillery.flashTimer) > 0;
            
            ctx.fillStyle = isFlashing ? '#ef4444' : '#b91c1c';
            ctx.beginPath();
            ctx.arc(enemyArtillery.x, enemyArtillery.y, 12, 0, Math.PI * 2);
            ctx.fill();

            ctx.fillStyle = '#fff';
            ctx.font = '10px Courier New';
            ctx.fillText('ARTILHARIA INIMIGA [ALVO]', enemyArtillery.x + 16, enemyArtillery.y + 4);

            // 5. Desenhar Bateria Amiga
            ctx.fillStyle = '#06b6d4';
            ctx.fillRect(friendlyBattery.x - 10, friendlyBattery.y - 10, 20, 20);
            ctx.fillText('NOSSAS FORÇAS (RECEPTOR PASSIVO)', friendlyBattery.x - 10, friendlyBattery.y + 25);

            // 6. Desenhar Drone
            ctx.fillStyle = '#38bdf8';
            ctx.beginPath();
            ctx.arc(drone.x, drone.y, 6, 0, Math.PI * 2);
            ctx.fill();
            ctx.fillText('DRONE DE RECONHECIMENTO', drone.x + 10, drone.y - 8);

            // 7. Atualizar e Desenhar Projéteis de Contrabateria
            for (let i = projectiles.length - 1; i >= 0; i--) {
                let p = projectiles[i];
                p.progress += 0.03;
                let curX = p.x + (p.destX - p.x) * p.progress;
                let curY = p.y + (p.destY - p.y) * p.progress;

                ctx.fillStyle = '#f59e0b';
                ctx.beginPath();
                ctx.arc(curX, curY, 4, 0, Math.PI * 2);
                ctx.fill();

                if (p.progress >= 1) {
                    // Impacto!
                    projectiles.splice(i, 1);
                    targetsNeutralized++;
                    document.getElementById('stat-targets').innerText = targetsNeutralized;
                    spawnNewTarget();
                }
            }

            requestAnimationFrame(animate);
        }

        animate();
    </script>
</body>
</html>
