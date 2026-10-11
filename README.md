
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SYSTEM C.A.M.P.I.S. v4.2.6 - Simulador Tático</title>
    <style>
        :root {
            --bg-color: #020617;
            --panel-bg: rgba(10, 22, 40, 0.9);
            --border-color: #00f0ff;
            --accent-green: #00ff66;
            --accent-red: #ff3333;
            --accent-amber: #ffb703;
            --text-main: #c0d8ef;
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
            background: linear-gradient(90deg, #021226, #0a2540, #021226);
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

        .status-badge {
            background: rgba(0, 255, 102, 0.2);
            border: 1px solid var(--accent-green);
            color: var(--accent-green);
            padding: 4px 10px;
            font-size: 0.75rem;
            font-weight: bold;
            border-radius: 3px;
            letter-spacing: 1px;
        }

        .main-container {
            display: flex;
            flex: 1;
            gap: 12px;
            padding: 12px;
            overflow: hidden;
        }

        /* Painel de Controle Esquerdo */
        .control-panel {
            width: 320px;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 12px;
            display: flex;
            flex-direction: column;
            gap: 12px;
            box-shadow: 0 0 15px rgba(0, 240, 255, 0.1);
        }

        .panel-section h3 {
            font-size: 0.8rem;
            color: var(--border-color);
            margin-bottom: 6px;
            border-bottom: 1px solid rgba(0, 240, 255, 0.3);
            padding-bottom: 3px;
            letter-spacing: 1px;
        }

        .btn-group {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        button {
            background: rgba(0, 240, 255, 0.1);
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
            background: rgba(0, 240, 255, 0.3);
            color: #fff;
            box-shadow: 0 0 8px var(--border-color);
        }

        button.danger-btn {
            border-color: var(--accent-red);
            background: rgba(255, 51, 51, 0.1);
            color: #ff9999;
        }
        button.danger-btn:hover {
            background: rgba(255, 51, 51, 0.3);
        }

        /* Área do Canvas e Log Central */
        .center-container {
            flex: 1;
            display: flex;
            flex-direction: column;
            gap: 12px;
            overflow: hidden;
        }

        .viewport-container {
            flex: 1.3;
            background: #010409;
            border: 1px solid var(--border-color);
            border-radius: 6px;
            display: flex;
            flex-direction: column;
            position: relative;
            overflow: hidden;
        }

        .viewport-header {
            background: rgba(0, 240, 255, 0.05);
            padding: 6px 12px;
            font-size: 0.7rem;
            display: flex;
            justify-content: space-between;
            border-bottom: 1px solid rgba(0, 240, 255, 0.2);
        }

        canvas {
            flex: 1;
            display: block;
            width: 100%;
            height: 100%;
        }

        /* Painel de Logs / Diagnóstico IA */
        .ai-log-panel {
            flex: 1;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 10px;
            display: flex;
            flex-direction: column;
            gap: 6px;
            font-size: 0.72rem;
            overflow-y: auto;
        }

        .log-title {
            color: var(--accent-green);
            font-weight: bold;
            border-bottom: 1px solid rgba(0, 255, 102, 0.3);
            padding-bottom: 2px;
        }

        .log-content {
            color: #a5d8ff;
            line-height: 1.4;
        }

        /* Painel de Telemetria Direito */
        .telemetry-panel {
            width: 310px;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 12px;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .metric-box {
            background: rgba(2, 12, 38, 0.7);
            border: 1px solid rgba(0, 240, 255, 0.3);
            border-radius: 4px;
            padding: 8px;
        }

        .metric-title {
            font-size: 0.65rem;
            color: var(--border-color);
            margin-bottom: 3px;
        }

        .metric-value {
            font-size: 0.95rem;
            font-weight: bold;
            color: #fff;
        }

        .status-ok { color: var(--accent-green); }
        .status-alert { color: var(--accent-red); }
        .status-warn { color: var(--accent-amber); }
    </style>
</head>
<body>

    <header>
        <h1>SYSTEM C.A.M.P.I.S. v4.2.6</h1>
        <div class="status-badge">STATUS: OPERACIONAL - OK</div>
    </header>

    <div class="main-container">
        <!-- 1. Painel de Controle Esquerdo -->
        <div class="control-panel">
            <div class="panel-section">
                <h3>CONTROLE DE EVENTOS</h3>
                <div class="btn-group">
                    <button class="danger-btn" onclick="triggerImpact()">Simular Impacto Hiperveloz / Pouso</button>
                    <button onclick="triggerCrosswind()">Simular Vento Cruzado (42 nós)</button>
                    <button onclick="runXRayScan()">Executar Varredura Raio-X</button>
                    <button onclick="resetSystem()">Resetar Sistema</button>
                </div>
            </div>

            <div class="panel-section">
                <h3>MODO DE ATUAÇÃO DA IA</h3>
                <div class="btn-group">
                    <button id="btn-auto" class="active" onclick="toggleAutoMode()">Modo Circuito Fechado: ATIVO</button>
                </div>
            </div>

            <div class="panel-section">
                <h3>ESPECIFICAÇÕES TÉCNICAS</h3>
                <div style="font-size: 0.68rem; line-height: 1.3; color: #8ab4f8;">
                    • Massa 1: Estrutura Principal (Nave 1)<br>
                    • Massa 2: Escudo / Sapata (Nave 2)<br>
                    • Molas: Titânio-Nitinol (Shape Memory)<br>
                    • Atuadores: Ímãs Permanentes (1.4 T)<br>
                    • Sensores: Acelerômetro, IR, Lidar, Raio-X
                </div>
            </div>
        </div>

        <!-- 2. Centro: Visualizador Canvas & Logs da IA -->
        <div class="center-container">
            <div class="viewport-container">
                <div class="viewport-header">
                    <span id="view-status">VISUALIZAÇÃO HÍBRIDA: NAVE 1 (PRINCIPAL) & NAVE 2 (ESCUDO)</span>
                    <span id="fps-display">120 FPS [IR + LIDAR]</span>
                </div>
                <canvas id="simCanvas"></canvas>
            </div>

            <div class="ai-log-panel">
                <div class="log-title">[ DIAGNÓSTICO E RELATÓRIO DE COMPORTAMENTO DA IA ]</div>
                <div class="log-content" id="ai-log-text">
                    [00:00:01] Sistema C.A.M.P.I.S. inicializado com sucesso.<br>
                    [00:00:02] Sensores ópticos térmicos e magnetômetros calibrados (1.4 Tesla).<br>
                    [00:00:03] Pronto para monitoramento de impactos e estabilização dinâmica em circuito fechado.
                </div>
            </div>
        </div>

        <!-- 3. Painel de Telemetria Direito -->
        <div class="telemetry-panel">
            <div class="panel-section">
                <h3>SENSORES & ATUADORES</h3>
            </div>
            <div class="metric-box">
                <div class="metric-title">CAM 1 (IR / TÉRMICA)</div>
                <div class="metric-value status-ok" id="stat-cam1">ATIVA - 120 FPS</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">CAM 2 (RAIO-X / INTEGRIDADE)</div>
                <div class="metric-value" id="stat-cam2">STANDBY</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">MOLA NITINOL (ESQ / DIR)</div>
                <div class="metric-value" id="stat-springs">12% / 12% Compressão</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">FLUXO MAGNÉTICO (ÍMÃS)</div>
                <div class="metric-value status-ok" id="stat-magnet">1.4 Tesla (Estável)</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">VIBRAÇÃO ESTRUTURAL (NAVE 1)</div>
                <div class="metric-value status-ok" id="stat-vibe">0.02 G (Mínima)</div>
            </div>
            <div class="metric-box">
                <div class="metric-title">TEMPERATURA NAVE 1 / NAVE 2</div>
                <div class="metric-value" id="stat-temp">22.4°C / 45.1°C</div>
            </div>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        let state = 'NORMAL'; // NORMAL, IMPACT, CROSSWIND, XRAY
        let vibration = 0.02;
        let tempNave1 = 22.4;
        let tempNave2 = 45.1;
        let compressionLeft = 12;
        let compressionRight = 12;
        let magneticFlux = 1.4;
        let offsetNave2X = 0;
        let xrayProgress = 100;
        let xrayActive = false;

        function resizeCanvas() {
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight - 30;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        function logAI(message) {
            let logDiv = document.getElementById('ai-log-text');
            let timeStr = new Date().toTimeString().split(' ')[0];
            logDiv.innerHTML += `<br>[${timeStr}] ${message}`;
            logDiv.scrollTop = logDiv.scrollHeight;
        }

        function triggerImpact() {
            state = 'IMPACT';
            offsetNave2X = -70; // Impacto empurra Nave 2 contra as molas
            vibration = 1.85;
            tempNave2 = 88.5;
            compressionLeft = 68;
            compressionRight = 65;
            magneticFlux = 2.8;

            document.getElementById('stat-vibe').innerText = '1.85 G (AMORTECENDO)';
            document.getElementById('stat-vibe').className = 'metric-value status-alert';
            document.getElementById('stat-temp').innerText = '24.1°C / 88.5°C (ATRITO)';
            document.getElementById('stat-springs').innerText = '68% / 65% Compressão';
            document.getElementById('stat-magnet').innerText = '2.8 Tesla (Damping Ativo)';

            logAI("⚠️ ALERTA: Impacto hiperveloz detectado na Massa 2 (Escudo de Sacrifício)!");
            logAI(">>> AÇÃO EXECUTADA: Circuito fechado acionou campo magnético máximo (2.8T) e molas de Nitinol absorveram 98% da energia cinética sem deformação permanente.");

            setTimeout(() => {
                state = 'NORMAL';
                vibration = 0.02;
                tempNave2 = 45.1;
                compressionLeft = 12;
                compressionRight = 12;
                magneticFlux = 1.4;
                document.getElementById('stat-vibe').innerText = '0.02 G (Mínima)';
                document.getElementById('stat-vibe').className = 'metric-value status-ok';
                document.getElementById('stat-temp').innerText = '22.4°C / 45.1°C';
                document.getElementById('stat-springs').innerText = '12% / 12% Compressão';
                document.getElementById('stat-magnet').innerText = '1.4 Tesla (Estável)';
                logAI(">>> ESTABILIZAÇÃO CONCLUÍDA: Sistema retornou ao alinhamento nominal.");
            }, 4000);
        }

        function triggerCrosswind() {
            state = 'CROSSWIND';
            compressionRight = 27; // Aumenta rigidez lado direito
            document.getElementById('stat-springs').innerText = '12% / 27% (Rigidez Ajustada)';
            logAI(">>> DIAGNÓSTICO IA: Vetor de vento cruzado detectado a 42 nós.");
            logAI(">>> AÇÃO EXECUTADA: Rigidez da Mola Direita aumentada em 15% preventivamente.");

            setTimeout(() => {
                state = 'NORMAL';
                compressionRight = 12;
                document.getElementById('stat-springs').innerText = '12% / 12% Compressão';
            }, 3500);
        }

        function runXRayScan() {
            xrayActive = true;
            xrayProgress = 0;
            document.getElementById('stat-cam2').innerText = 'VARREDURA EM ANDAMENTO...';
            document.getElementById('stat-cam2').className = 'metric-value status-warn';
            logAI("🔍 Varredura preventiva por difração de Raios-X iniciada em toda a estrutura de Nitinol.");

            let interval = setInterval(() => {
                xrayProgress += 20;
                if (xrayProgress >= 100) {
                    clearInterval(interval);
                    xrayActive = false;
                    document.getElementById('stat-cam2').innerText = 'STANDBY (100% ÍNTEGRO)';
                    document.getElementById('stat-cam2').className = 'metric-value status-ok';
                    logAI("✅ RESULTADO RAIO-X: 100% Concluído. 0 micro-fissuras internas encontradas no Nitinol e na estrutura.");
                }
            }, 400);
        }

        function resetSystem() {
            state = 'NORMAL';
            vibration = 0.02;
            tempNave1 = 22.4;
            tempNave2 = 45.1;
            compressionLeft = 12;
            compressionRight = 12;
            magneticFlux = 1.4;
            offsetNave2X = 0;
            logAI("🔄 Sistema C.A.M.P.I.S. reinicializado para padrões de fábrica.");
        }

        function toggleAutoMode() {
            let btn = document.getElementById('btn-auto');
            btn.classList.toggle('active');
            let active = btn.classList.contains('active');
            btn.innerText = `Modo Circuito Fechado: ${active ? 'ATIVO' : 'MANUAL'}`;
            logAI(`⚙️ Modo de operação alterado para: ${active ? 'Circuito Fechado Autônomo' : 'Manual'}`);
        }

        // Loop principal de renderização Canvas 2D
        function animate() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Desenhar grid de fundo cibernético
            ctx.strokeStyle = 'rgba(0, 240, 255, 0.04)';
            ctx.lineWidth = 1;
            let gridSize = 40;
            for (let x = 0; x < canvas.width; x += gridSize) {
                ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, canvas.height); ctx.stroke();
            }
            for (let y = 0; y < canvas.height; y += gridSize) {
                ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(canvas.width, y); ctx.stroke();
            }

            let cx = canvas.width / 2;
            let cy = canvas.height / 2;

            // Retorno suave da Nave 2 caso tenha sofrido impacto
            offsetNave2X += (0 - offsetNave2X) * 0.08;

            // Coordenadas das massas
            let nave1Width = 180;
            let nave1Height = 90;
            let nave2Width = 80;
            let nave2Height = 110;

            let nave1X = cx - 120;
            let nave1Y = cy - nave1Height / 2;

            let nave2X = cx + 80 + offsetNave2X;
            let nave2Y = cy - nave2Height / 2;

            // 1. Desenhar Molas de Nitinol e Ímãs de Fluxo entre Nave 1 e Nave 2
            ctx.strokeStyle = state === 'IMPACT' ? '#ff3333' : '#00f0ff';
            ctx.lineWidth = 3;
            ctx.beginPath();
            // Mola superior (Nitinol)
            let springStartX = nave1X + nave1Width;
            let springStartY = cy - 25;
            let springEndX = nave2X;
            let springEndY = cy - 25;

            let coils = 8;
            let step = (springEndX - springStartX) / coils;
            ctx.moveTo(springStartX, springStartY);
            for (let i = 0; i < coils; i++) {
                let sx = springStartX + i * step;
                let sy = springStartY + (i % 2 === 0 ? -12 : 12);
                ctx.lineTo(sx + step/2, sy);
            }
            ctx.lineTo(springEndX, springEndY);
            ctx.stroke();

            // Mola inferior (Nitinol)
            ctx.beginPath();
            let springStartY2 = cy + 25;
            let springEndY2 = cy + 25;
            ctx.moveTo(springStartX, springStartY2);
            for (let i = 0; i < coils; i++) {
                let sx = springStartX + i * step;
                let sy = springStartY2 + (i % 2 === 0 ? -12 : 12);
                ctx.lineTo(sx + step/2, sy);
            }
            ctx.lineTo(springEndX, springEndY2);
            ctx.stroke();

            // Atuadores de Campo Magnético (Linhas de Fluxo Central)
            ctx.strokeStyle = 'rgba(0, 255, 102, 0.6)';
            ctx.setLineDash([4, 4]);
            ctx.beginPath();
            ctx.moveTo(springStartX, cy);
            ctx.lineTo(springEndX, cy);
            ctx.stroke();
            ctx.setLineDash([]); // Reset

            // 2. Desenhar Nave 1 (Massa Principal / Estrutura)
            ctx.fillStyle = '#0f172a';
            ctx.strokeStyle = '#00f0ff';
            ctx.lineWidth = 2;
            ctx.fillRect(nave1X, nave1Y, nave1Width, nave1Height);
            ctx.strokeRect(nave1X, nave1Y, nave1Width, nave1Height);

            ctx.fillStyle = '#c0d8ef';
            ctx.font = '11px Courier New';
            ctx.fillText('NAVE 1 (PRINCIPAL)', nave1X + 15, nave1Y + 30);
            ctx.fillStyle = '#00ff66';
            ctx.font = '9px Courier New';
            ctx.fillText(`Vibração: ${vibration.toFixed(2)} G`, nave1X + 15, nave1Y + 50);
            ctx.fillText(`Temp: ${tempNave1}°C`, nave1X + 15, nave1Y + 68);

            // 3. Desenhar Nave 2 (Massa de Atenuação / Escudo / Sapata)
            ctx.fillStyle = state === 'IMPACT' ? 'rgba(255, 51, 51, 0.2)' : '#1e293b';
            ctx.strokeStyle = state === 'IMPACT' ? '#ff3333' : '#ffb703';
            ctx.lineWidth = 2;
            ctx.fillRect(nave2X, nave2Y, nave2Width, nave2Height);
            ctx.strokeRect(nave2X, nave2Y, nave2Width, nave2Height);

            ctx.fillStyle = '#ffb703';
            ctx.font = '10px Courier New';
            ctx.fillText('NAVE 2', nave2X + 18, nave2Y + 30);
            ctx.fillText('(ESCUDO)', nave2X + 14, nave2Y + 45);
            ctx.fillStyle = '#ff3333';
            ctx.fillText(`Temp: ${tempNave2.toFixed(1)}°C`, nave2X + 8, nave2Y + 75);

            // Efeito de Varredura de Raio-X se ativo
            if (xrayActive) {
                ctx.strokeStyle = 'rgba(0, 255, 102, 0.8)';
                ctx.lineWidth = 2;
                let scanX = nave1X + (xrayProgress / 100) * (nave2X + nave2Width - nave1X);
                ctx.beginPath();
                ctx.moveTo(scanX, cy - 80);
                ctx.lineTo(scanX, cy + 80);
                ctx.stroke();

                ctx.fillStyle = '#00ff66';
                ctx.font = '10px Courier New';
                ctx.fillText(`RAIO-X: ${xrayProgress}%`, scanX - 35, cy - 90);
            }

            requestAnimationFrame(animate);
        }

        animate();
    </script>
</body>
</html>
