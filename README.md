<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>The Lost MC - Gestão de Metas</title>
    <style>
        :root {
            --primary: #1a1a1a;
            --accent: #e67e22; /* Laranja Biker */
            --success: #27ae60;
            --danger: #c0392b;
            --text: #ffffff;
        }

        body {
            font-family: 'Segoe UI', Arial, sans-serif;
            background: radial-gradient(circle, #2c3e50 0%, #000000 100%);
            color: var(--text);
            margin: 0;
            padding: 20px;
            min-height: 100vh;
        }

        /* Container Principal */
        .container {
            max-width: 850px;
            margin: 0 auto;
            background: rgba(0, 0, 0, 0.85);
            border: 2px solid var(--accent);
            border-radius: 10px;
            padding: 30px;
            box-shadow: 0 0 30px rgba(230, 126, 34, 0.3);
        }

        /* Logo em SVG (Garante que nunca desaparece) */
        .header-logo {
            text-align: center;
            margin-bottom: 20px;
        }

        header h1 {
            text-align: center;
            text-transform: uppercase;
            letter-spacing: 5px;
            margin: 10px 0;
            color: var(--accent);
            text-shadow: 2px 2px 5px #000;
        }

        .signature {
            text-align: center;
            font-size: 0.8rem;
            color: #888;
            margin-bottom: 30px;
            text-transform: uppercase;
        }

        /* Controles */
        .controls {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-bottom: 30px;
        }

        select, button {
            background: #222;
            color: white;
            border: 1px solid var(--accent);
            padding: 12px;
            font-size: 1rem;
            cursor: pointer;
            border-radius: 5px;
        }

        button:hover {
            background: var(--accent);
            color: black;
            font-weight: bold;
        }

        /* Seções de Cargos */
        .role-section {
            margin-bottom: 25px;
        }

        .role-title {
            background: linear-gradient(90deg, var(--accent), transparent);
            padding: 8px 15px;
            font-weight: bold;
            text-transform: uppercase;
            margin-bottom: 10px;
            border-left: 5px solid white;
        }

        .member-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
            gap: 10px;
        }

        .member-card {
            background: #252525;
            padding: 15px;
            border-radius: 5px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            cursor: pointer;
            border: 1px solid #333;
            transition: 0.2s;
        }

        .member-card:hover {
            border-color: var(--accent);
        }

        .member-card.done {
            background: rgba(39, 174, 96, 0.2);
            border-color: var(--success);
        }

        .status-icon {
            font-size: 1.2rem;
            opacity: 0.3;
        }

        .done .status-icon {
            opacity: 1;
            color: var(--success);
        }

        /* Barra de Progresso */
        .progress-box {
            margin-top: 30px;
            background: #111;
            height: 25px;
            border-radius: 15px;
            overflow: hidden;
            border: 1px solid #444;
            position: relative;
        }

        .progress-bar {
            height: 100%;
            background: var(--accent);
            width: 0%;
            transition: 0.5s;
        }

        .progress-text {
            position: absolute;
            width: 100%;
            text-align: center;
            line-height: 25px;
            font-size: 0.9rem;
            font-weight: bold;
        }

        /* Relatório Discord */
        #reportOutput {
            margin-top: 20px;
            background: #000;
            color: #0f0;
            padding: 15px;
            border-radius: 5px;
            display: none;
            white-space: pre-wrap;
            font-family: 'Courier New', monospace;
            font-size: 0.85rem;
            border: 1px dashed var(--accent);
        }
    </style>
</head>
<body>

<div class="container">
    <div class="header-logo">
        <!-- Ícone de Caveira Biker (SVG) -->
        <svg width="80" height="80" viewBox="0 0 100 100" fill="var(--accent)">
            <path d="M50 10C30 10 15 25 15 45C15 55 20 65 25 72V85C25 88 27 90 30 90H70C73 90 75 88 75 85V72C80 65 85 55 85 45C85 25 70 10 50 10ZM35 40C35 37 37 35 40 35C43 35 45 37 45 40C45 43 43 45 40 45C37 45 35 43 35 40ZM65 45C62 45 60 43 60 40C60 37 62 35 65 35C68 35 70 37 70 40C70 43 68 45 65 45ZM40 70H60V75H40V70Z"/>
        </svg>
        <h1>The Lost MC</h1>
        <div class="signature">Organizado por: <strong>Marta Martins</strong></div>
    </div>

    <div class="controls">
        <select id="weekSelect" onchange="loadData()"></select>
        <button onclick="generateDiscordReport()">Gerar Relatório Discord</button>
    </div>

    <div id="memberContainer"></div>

    <div class="progress-box">
        <div class="progress-text" id="progText">0% CONCLUÍDO</div>
        <div class="progress-bar" id="progBar"></div>
    </div>

    <pre id="reportOutput"></pre>
    <button id="copyBtn" style="display:none; width: 100%; margin-top: 10px;" onclick="copyReport()">Copiar Texto do Relatório</button>
</div>

<script>
    const members = {
        "💼 Gerente": ["Jéssica", "Josefino Flor", "DeadManel / Ruan Cardoso", "M4rtinh4"],
        "🏍️ Capitão": ["Jalaias"],
        "🔨 Motard/Engenheiro": ["tiago.21", "Elio CeGoNhA", "Gameiro✝️", "Careca", "Diogo Palhares"],
        "🧹 Aspirante": ["Fomigaa", "ANTI VITAMINAS", "Padeiro Moretti", "Karol Moretti", "Demon"],
        "🥾 Aprendiz": ["xupas", "Diogo Ferreira / Lyam Ferreira", "Ana Ferreira"]
    };

    function initWeeks() {
        const select = document.getElementById('weekSelect');
        // Começa em 27/04/2026
        let start = new Date(2026, 3, 27);
        let end = new Date(2026, 7, 31); // Fim de Agosto

        while (start <= end) {
            let next = new Date(start);
            next.setDate(next.getDate() + 6);
            let label = start.toLocaleDateString('pt-PT') + " até " + next.toLocaleDateString('pt-PT');
            let val = start.toISOString().split('T')[0];
            select.innerHTML += `<option value="${val}">${label}</option>`;
            start.setDate(start.getDate() + 7);
        }
    }

    function renderMembers() {
        const container = document.getElementById('memberContainer');
        container.innerHTML = '';
        
        for (const [role, names] of Object.entries(members)) {
            let roleHtml = `<div class="role-section"><div class="role-title">${role}</div><div class="member-grid">`;
            names.forEach(name => {
                roleHtml += `
                    <div class="member-card" onclick="toggleMember('${name}', this)" data-name="${name}">
                        <span>${name}</span>
                        <span class="status-icon">💀</span>
                    </div>`;
            });
            roleHtml += `</div></div>`;
            container.innerHTML += roleHtml;
        }
    }

    function toggleMember(name, element) {
        const week = document.getElementById('weekSelect').value;
        element.classList.toggle('done');
        const status = element.classList.contains('done');
        localStorage.setItem(`lostMC_${week}_${name}`, status);
        updateProgress();
    }

    function loadData() {
        const week = document.getElementById('weekSelect').value;
        document.querySelectorAll('.member-card').forEach(card => {
            const name = card.getAttribute('data-name');
            const isDone = localStorage.getItem(`lostMC_${week}_${name}`) === 'true';
            card.classList.toggle('done', isDone);
        });
        updateProgress();
        document.getElementById('reportOutput').style.display = 'none';
        document.getElementById('copyBtn').style.display = 'none';
    }

    function updateProgress() {
        const total = document.querySelectorAll('.member-card').length;
        const done = document.querySelectorAll('.member-card.done').length;
        const perc = Math.round((done / total) * 100) || 0;
        document.getElementById('progBar').style.width = perc + '%';
        document.getElementById('progText').innerText = `${perc}% CONCLUÍDO (${done}/${total})`;
    }

    function generateDiscordReport() {
        const weekLabel = document.getElementById('weekSelect').options[document.getElementById('weekSelect').selectedIndex].text;
        let txt = `**🦅 THE LOST MC - METAS SEMANAIS**\n`;
        txt += `📅 **Semana:** ${weekLabel}\n`;
        txt += `✍️ **Tratado por:** Marta Martins\n`;
        txt += `──────────────────────────\n`;

        for (const [role, names] of Object.entries(members)) {
            txt += `\n**${role}**\n`;
            names.forEach(name => {
                const isDone = localStorage.getItem(`lostMC_${document.getElementById('weekSelect').value}_${name}`) === 'true';
                txt += `${isDone ? '✅' : '❌'} ${name}\n`;
            });
        }

        const out = document.getElementById('reportOutput');
        out.innerText = txt;
        out.style.display = 'block';
        document.getElementById('copyBtn').style.display = 'block';
    }

    function copyReport() {
        const text = document.getElementById('reportOutput').innerText;
        navigator.clipboard.writeText(text);
        alert("Relatório copiado!");
    }

    initWeeks();
    renderMembers();
    loadData();
</script>

</body>
</html>
