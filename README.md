
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador de Civilização Tipo 0 - Escala Kardashev</title>
    <style>
        :root {
            --bg-color: #030712;
            --panel-bg: rgba(15, 23, 42, 0.85);
            --border-color: rgba(56, 189, 248, 0.3);
            --accent-cyan: #38bdf8;
            --accent-amber: #f59e0b;
            --accent-red: #ef4444;
            --accent-green: #10b981;
            --text-main: #f3f4f6;
            --text-muted: #9ca3af;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
            user-select: none;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            height: 100vh;
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        header {
            height: 60px;
            background: linear-gradient(180deg, rgba(15,23,42,0.9) 0%, rgba(3,7,18,0.6) 100%);
            border-bottom: 1px solid var(--border-color);
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 25px;
            z-index: 10;
        }

        header h1 {
            font-size: 1.1rem;
            letter-spacing: 2px;
            color: var(--accent-cyan);
            text-transform: uppercase;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .status-badge {
            font-size: 0.75rem;
            padding: 3px 10px;
            border-radius: 12px;
            background: rgba(245, 158, 11, 0.2);
            border: 1px solid var(--accent-amber);
            color: var(--accent-amber);
        }

        .main-container {
            display: grid;
            grid-template-columns: 360px 1fr;
            height: calc(100vh - 60px);
            position: relative;
        }

        /* PAINEL DE CONTROLE */
        .control-panel {
            background: var(--panel-bg);
            border-right: 1px solid var(--border-color);
            backdrop-filter: blur(12px);
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 18px;
            overflow-y: auto;
            z-index: 5;
        }

        .section-title {
            font-size: 0.8rem;
            text-transform: uppercase;
            letter-spacing: 1.5px;
            color: var(--accent-cyan);
            border-bottom: 1px solid var(--border-color);
            padding-bottom: 6px;
        }

        .info-card {
            background: rgba(0, 0, 0, 0.4);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 8px;
            padding: 12px;
            font-size: 0.82rem;
            line-height: 1.4;
            color: var(--text-muted);
        }

        .info-card strong {
            color: var(--text-main);
        }

        .metric-group {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        .metric-header {
            display: flex;
            justify-content: space-between;
            font-size: 0.8rem;
        }

        .metric-value {
            font-family: monospace;
            font-weight: bold;
            color: var(--accent-cyan);
        }

        input[type="range"] {
            width: 100%;
            height: 6px;
            border-radius: 3px;
            background: rgba(255, 255, 255, 0.1);
            outline: none;
            accent-color: var(--accent-cyan);
            cursor: pointer;
        }

        .kardashev-box {
            background: radial-gradient(circle, rgba(56,189,248,0.15) 0%, rgba(0,0,0,0.5) 100%);
            border: 1px solid var(--accent-cyan);
            border-radius: 8px;
            padding: 15px;
            text-align: center;
        }

        .kardashev-score {
            font-size: 2.2rem;
            font-family: monospace;
            font-weight: bold;
            color: #fff;
            text-shadow: 0 0 15px var(--accent-cyan);
            margin: 5px 0;
        }

        /* VIEWPORT CANVAS */
        .viewport {
            position: relative;
            width: 100%;
            height: 100%;
            background: radial-gradient(circle at center, #0B132B 0%, #030712 100%);
            overflow: hidden;
        }

        canvas {
            width: 100%;
            height: 100%;
            display: block;
        }

        .hud-overlay {
            position: absolute;
            bottom: 20px;
            right: 20px;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 15px;
            font-family: monospace;
            font-size: 0.75rem;
            pointer-events: none;
            display: flex;
            flex-direction: column;
            gap: 6px;
            backdrop-filter: blur(10px);
        }

        .hud-line {
            display: flex;
            justify-content: space-between;
            gap: 20px;
        }

        ::-webkit-scrollbar { width: 5px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: var(--border-color); border-radius: 3px; }
    </style>
</head>
<body>

    <header>
        <h1>
            <span>NÍVEL DE CIVILIZAÇÃO: TIPO 0</span>
        </h1>
        <div class="status-badge">DIAGNÓSTICO: PLANETA FRAGILIZADO</div>
    </header>

    <div class="main-container">
        <!-- PAINEL DE CONTROLE -->
        <aside class="control-panel">
            <div class="kardashev-box">
                <div style="font-size: 0.75rem; color: var(--text-muted); text-transform: uppercase;">Índice na Escala Kardashev</div>
                <div class="kardashev-score" id="k-score">K 0.732</div>
                <div style="font-size: 0.75rem; color: var(--accent-amber);" id="power-display">2.10 × 10¹³ W</div>
            </div>

            <div class="section-title">1. Matriz Energética Global</div>

            <div class="metric-group">
                <div class="metric-header">
                    <span>Combustíveis Fósseis (Petróleo/Carvão/Gás)</span>
                    <span class="metric-value" id="val-fossil" style="color: var(--accent-red)">80%</span>
                </div>
                <input type="range" id="input-fossil" min="0" max="100" value="80">
            </div>

            <div class="metric-group">
                <div class="metric-header">
                    <span>Energia Nuclear (Fissão)</span>
                    <span class="metric-value" id="val-nuclear" style="color: #a855f7">10%</span>
                </div>
                <input type="range" id="input-nuclear" min="0" max="50" value="10">
            </div>

            <div class="metric-group">
                <div class="metric-header">
                    <span>Renováveis (Solar, Eólica, Hidro)</span>
                    <span class="metric-value" id="val-renewable" style="color: var(--accent-green)">10%</span>
                </div>
                <input type="range" id="input-renewable" min="0" max="100" value="10">
            </div>

            <div class="section-title">2. Consumo Energético Total</div>
            <div class="metric-group">
                <div class="metric-header">
                    <span>Demanda Global (x10¹³ W)</span>
                    <span class="metric-value" id="val-demand">2.10 W</span>
                </div>
                <input type="range" id="input-demand" min="0.5" max="10.0" step="0.1" value="2.1">
            </div>

            <div class="section-title">3. Parâmetros do Cenário Tipo 0</div>
            <div class="info-card">
                <strong>Características Atuais:</strong><br>
                • Dependência crítica de fontes não renováveis.<br>
                • Sub-aproveitamento do fluxo solar direto.<br>
                • Vulcanismo, clima e tectônica não controlados.<br>
                • Vulnerabilidade a catástrofes naturais.
            </div>
        </aside>

        <!-- VIEWPORT SIMULAÇÃO 2D/3D -->
        <main class="viewport">
            <canvas id="simCanvas"></canvas>

            <div class="hud-overlay">
                <div class="hud-line"><span>TEMPERATURA MÉDIA:</span><span id="hud-temp" style="color: var(--accent-amber)">15.8 °C</span></div>
                <div class="hud-line"><span>POLUIÇÃO ATMOSFÉRICA:</span><span id="hud-pollution" style="color: var(--accent-red)">ALTISSIMA</span></div>
                <div class="hud-line"><span>CONTROLE CLIMÁTICO:</span><span style="color: var(--accent-red)">NULO (0%)</span></div>
                <div class="hud-line"><span>ESTABILIDADE ECOSSISTÊMICA:</span><span id="hud-stability" style="color: var(--accent-green)">64%</span></div>
            </div>
        </main>
    </div>

    <script>
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        // Estado da Simulação
        const state = {
            fossilPct: 80,
            nuclearPct: 10,
            renewablePct: 10,
            powerWatts: 2.1e13,
            kardashevScale: 0.732,
            pollutionLevel: 0.8,
            temperature: 15.8,
            stability: 64,
            rotationAngle: 0
        };

        // Redimensionar Canvas
        function resizeCanvas() {
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // Elementos DOM
        const inputFossil = document.getElementById('input-fossil');
        const inputNuclear = document.getElementById('input-nuclear');
        const inputRenewable = document.getElementById('input-renewable');
        const inputDemand = document.getElementById('input-demand');

        // Partículas de Energia
        const particles = [];
        const NUM_PARTICLES = 120;

        class Particle {
            constructor() {
                this.reset();
            }

            reset() {
                this.angle = Math.random() * Math.PI * 2;
                this.distance = 120 + Math.random() * 180;
                this.speed = (Math.random() * 0.008) + 0.002;
                this.size = Math.random() * 2.5 + 1;
                this.life = Math.random();
                
                // Determina o tipo de partícula baseado na matriz
                const rand = Math.random() * 100;
                if (rand < state.fossilPct) {
                    this.type = 'fossil'; // Laranja / Vermelho
                } else if (rand < state.fossilPct + state.nuclearPct) {
                    this.type = 'nuclear'; // Violeta
                } else {
                    this.type = 'renewable'; // Cyan / Verde
                }
            }

            update() {
                this.angle += this.speed;
                this.life -= 0.003;
                if (this.life <= 0) this.reset();
            }

            draw(cx, cy) {
                const x = cx + Math.cos(this.angle) * this.distance;
                const y = cy + Math.sin(this.angle) * (this.distance * 0.5); // Órbita elíptica

                ctx.beginPath();
                ctx.arc(x, y, this.size, 0, Math.PI * 2);

                if (this.type === 'fossil') {
                    ctx.fillStyle = `rgba(239, 68, 68, ${this.life})`;
                    ctx.shadowColor = '#ef4444';
                } else if (this.type === 'nuclear') {
                    ctx.fillStyle = `rgba(168, 85, 247, ${this.life})`;
                    ctx.shadowColor = '#a855f7';
                } else {
                    ctx.fillStyle = `rgba(56, 189, 248, ${this.life})`;
                    ctx.shadowColor = '#38bdf8';
                }
                ctx.shadowBlur = 8;
                ctx.fill();
                ctx.shadowBlur = 0;
            }
        }

        for (let i = 0; i < NUM_PARTICLES; i++) {
            particles.push(new Particle());
        }

        // Cálculos do Sistema
        function updatePhysics() {
            const total = state.fossilPct + state.nuclearPct + state.renewablePct;
            
            // Fórmula Kardashev: K = (log10(P) - 6) / 10
            state.kardashevScale = (Math.log10(state.powerWatts) - 6) / 10;

            // Nível de poluição e temperatura baseado no uso de fósseis
            const fossilRatio = state.fossilPct / (total || 1);
            state.pollutionLevel = fossilRatio * (state.powerWatts / 2.1e13);
            state.temperature = 14.0 + (state.pollutionLevel * 3.5);
            state.stability = Math.max(10, Math.min(100, Math.round(100 - (state.pollutionLevel * 45))));

            // Atualizar UI Textual
            document.getElementById('k-score').innerText = `K ${state.kardashevScale.toFixed(3)}`;
            
            const powerScaled = (state.powerWatts / 1e13).toFixed(2);
            document.getElementById('power-display').innerText = `${powerScaled} × 10¹³ W`;
            
            document.getElementById('val-fossil').innerText = `${state.fossilPct}%`;
            document.getElementById('val-nuclear').innerText = `${state.nuclearPct}%`;
            document.getElementById('val-renewable').innerText = `${state.renewablePct}%`;
            document.getElementById('val-demand').innerText = `${(state.powerWatts / 1e13).toFixed(2)} W`;

            document.getElementById('hud-temp').innerText = `${state.temperature.toFixed(1)} °C`;
            document.getElementById('hud-stability').innerText = `${state.stability}%`;

            const polHUD = document.getElementById('hud-pollution');
            if (state.pollutionLevel > 0.6) {
                polHUD.innerText = 'CRÍTICA';
                polHUD.style.color = 'var(--accent-red)';
            } else if (state.pollutionLevel > 0.3) {
                polHUD.innerText = 'MODERADA';
                polHUD.style.color = 'var(--accent-amber)';
            } else {
                polHUD.innerText = 'CONTROLADA';
                polHUD.style.color = 'var(--accent-green)';
            }
        }

        // Escutadores de Eventos
        inputFossil.addEventListener('input', (e) => {
            state.fossilPct = parseInt(e.target.value);
            updatePhysics();
        });

        inputNuclear.addEventListener('input', (e) => {
            state.nuclearPct = parseInt(e.target.value);
            updatePhysics();
        });

        inputRenewable.addEventListener('input', (e) => {
            state.renewablePct = parseInt(e.target.value);
            updatePhysics();
        });

        inputDemand.addEventListener('input', (e) => {
            state.powerWatts = parseFloat(e.target.value) * 1e13;
            updatePhysics();
        });

        // Loop de Renderização
        function render() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            const cx = canvas.width / 2;
            const cy = canvas.height / 2;
            const earthRadius = 90;

            state.rotationAngle += 0.003;

            // 1. Desenhar Fundo Sci-Fi (Grelha)
            ctx.strokeStyle = 'rgba(56, 189, 248, 0.05)';
            ctx.lineWidth = 1;
            const gridSize = 40;
            for (let x = 0; x < canvas.width; x += gridSize) {
                ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, canvas.height); ctx.stroke();
            }
            for (let y = 0; y < canvas.height; y += gridSize) {
                ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(canvas.width, y); ctx.stroke();
            }

            // 2. Anéis HUD em volta da Terra
            ctx.save();
            ctx.translate(cx, cy);
            
            // Anel externo pontilhado
            ctx.beginPath();
            ctx.arc(0, 0, earthRadius + 50, 0, Math.PI * 2);
            ctx.setLineDash([4, 8]);
            ctx.strokeStyle = 'rgba(56, 189, 248, 0.3)';
            ctx.stroke();

            // Anel giratório Kardashev
            ctx.rotate(state.rotationAngle);
            ctx.beginPath();
            ctx.arc(0, 0, earthRadius + 30, 0, Math.PI * 1.2);
            ctx.setLineDash([]);
            ctx.strokeStyle = 'rgba(245, 158, 11, 0.5)';
            ctx.lineWidth = 2;
            ctx.stroke();
            ctx.restore();

            // 3. Desenhar a Terra (Tipo 0)
            // Oceano/Superfície
            const earthGrad = ctx.createRadialGradient(cx - 20, cy - 20, 10, cx, cy, earthRadius);
            earthGrad.addColorStop(0, '#1d4ed8');
            earthGrad.addColorStop(0.7, '#1e3a8a');
            earthGrad.addColorStop(1, '#0f172a');

            ctx.beginPath();
            ctx.arc(cx, cy, earthRadius, 0, Math.PI * 2);
            ctx.fillStyle = earthGrad;
            ctx.fill();

            // Continentes Simulados (Formas Estáticas/Simples)
            ctx.fillStyle = '#15803d';
            ctx.beginPath();
            ctx.arc(cx - 30, cy - 20, 35, 0, Math.PI * 2);
            ctx.arc(cx + 25, cy + 30, 28, 0, Math.PI * 2);
            ctx.arc(cx + 30, cy - 35, 22, 0, Math.PI * 2);
            ctx.fill();

            // 4. Camada de Atmosfera e Poluição (Dinâmica)
            const polAlpha = Math.min(0.85, state.pollutionLevel * 0.7);
            const atmosGrad = ctx.createRadialGradient(cx, cy, earthRadius - 5, cx, cy, earthRadius + 25);
            
            // Cor varia de Azul (Limpo) a Marrom/Cinza (Poluído)
            atmosGrad.addColorStop(0, 'rgba(0,0,0,0)');
            atmosGrad.addColorStop(0.6, `rgba(${120 + state.pollutionLevel * 100}, ${180 - state.pollutionLevel * 100}, 200, ${0.4 - polAlpha * 0.2})`);
            atmosGrad.addColorStop(1, `rgba(${180 * state.pollutionLevel}, ${100 * (1 - state.pollutionLevel)}, 50, ${polAlpha})`);

            ctx.beginPath();
            ctx.arc(cx, cy, earthRadius + 25, 0, Math.PI * 2);
            ctx.fillStyle = atmosGrad;
            ctx.fill();

            // 5. Partículas de Energia e Consumo
            particles.forEach(p => {
                p.update();
                p.draw(cx, cy);
            });

            requestAnimationFrame(render);
        }

        // Inicialização
        updatePhysics();
        render();
    </script>
</body>
</html>
