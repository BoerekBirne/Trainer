<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Physik Trainer Pro</title>
    <style>
        /* --- DESIGN VARIABLES --- */
        :root {
            --bg-body: #f8f9fa;
            --bg-card: #ffffff;
            --text-main: #212529;
            --text-muted: #6c757d;
            --primary: #2c3e50; /* Dunkles Blau für Seriösität */
            --accent: #3498db; /* Helles Blau für Interaktion */
            --success: #27ae60;
            --error: #c0392b;
            --border: #dee2e6;
            --shadow: 0 2px 10px rgba(0,0,0,0.05);
            --font-stack: 'Helvetica Neue', Helvetica, Arial, sans-serif;
        }

        .dark-mode {
            --bg-body: #121212;
            --bg-card: #1e1e1e;
            --text-main: #e0e0e0;
            --text-muted: #a0a0a0;
            --primary: #4a6fa5;
            --accent: #64b5f6;
            --border: #333;
            --shadow: 0 2px 10px rgba(0,0,0,0.5);
        }

        /* --- BASIC RESET & TYPO --- */
        * { box-sizing: border-box; }
        body {
            font-family: var(--font-stack);
            background-color: var(--bg-body);
            color: var(--text-main);
            margin: 0;
            transition: background 0.3s, color 0.3s;
            line-height: 1.6;
        }

        h1, h2, h3 { margin-top: 0; color: var(--primary); }
        
        /* --- LAYOUT --- */
        header {
            position: fixed;
            top: 0; left: 0; right: 0;
            height: 60px;
            background: var(--bg-card);
            border-bottom: 1px solid var(--border);
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 20px;
            z-index: 1000;
        }

        .brand { font-weight: 700; font-size: 1.1rem; letter-spacing: 0.5px; }
        
        button { cursor: pointer; border: none; outline: none; font-family: inherit; }
        
        .icon-btn {
            background: none;
            font-size: 1.2rem;
            color: var(--text-main);
            padding: 8px;
            border-radius: 4px;
        }
        .icon-btn:hover { background: rgba(0,0,0,0.05); }

        /* --- SIDE MENU --- */
        #side-menu {
            position: fixed;
            top: 60px; left: -300px;
            width: 300px;
            height: calc(100vh - 60px);
            background: var(--bg-card);
            border-right: 1px solid var(--border);
            padding: 20px;
            transition: left 0.3s ease;
            z-index: 999;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
        }
        #side-menu.open { left: 0; }

        .search-container { margin-bottom: 20px; }
        #search-input {
            width: 100%;
            padding: 10px;
            border: 1px solid var(--border);
            background: var(--bg-body);
            color: var(--text-main);
            border-radius: 4px;
        }

        .menu-group { margin-bottom: 20px; }
        .menu-label {
            font-size: 0.75rem;
            text-transform: uppercase;
            color: var(--text-muted);
            margin-bottom: 10px;
            font-weight: 700;
            letter-spacing: 1px;
        }

        .menu-item {
            display: block;
            padding: 12px;
            margin-bottom: 5px;
            border-radius: 6px;
            background: transparent;
            text-align: left;
            width: 100%;
            color: var(--text-main);
            font-weight: 500;
            transition: background 0.2s;
            border: 1px solid transparent;
        }
        .menu-item:hover {
            background: rgba(52, 152, 219, 0.1);
            color: var(--accent);
            border-color: rgba(52, 152, 219, 0.2);
        }

        /* --- MAIN CONTENT --- */
        main {
            margin-top: 60px;
            padding: 30px 20px;
            max-width: 800px;
            margin-left: auto;
            margin-right: auto;
        }

        .card {
            background: var(--bg-card);
            border: 1px solid var(--border);
            border-radius: 8px;
            padding: 30px;
            box-shadow: var(--shadow);
            margin-bottom: 20px;
        }

        /* --- QUIZ UI --- */
        .progress-container {
            height: 6px;
            background: var(--border);
            border-radius: 3px;
            margin-bottom: 25px;
            overflow: hidden;
        }
        .progress-fill {
            height: 100%;
            background: var(--accent);
            width: 0%;
            transition: width 0.4s ease;
        }

        .question-text {
            font-size: 1.3rem;
            margin-bottom: 25px;
            font-weight: 600;
        }

        .options-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 10px;
        }
        @media(min-width: 600px) { .options-grid { grid-template-columns: 1fr 1fr; } }

        .btn-option {
            padding: 15px;
            background: var(--bg-body);
            border: 2px solid var(--border);
            border-radius: 6px;
            color: var(--text-main);
            font-size: 1rem;
            transition: all 0.2s;
        }
        .btn-option:hover:not(:disabled) {
            border-color: var(--accent);
            background: rgba(52, 152, 219, 0.05);
        }
        .btn-option.correct {
            border-color: var(--success);
            background: rgba(39, 174, 96, 0.1);
            color: var(--success);
        }
        .btn-option.wrong {
            border-color: var(--error);
            background: rgba(192, 57, 43, 0.1);
            color: var(--error);
        }

        .input-field {
            width: 100%;
            padding: 15px;
            font-size: 1.1rem;
            border: 2px solid var(--border);
            border-radius: 6px;
            background: var(--bg-body);
            color: var(--text-main);
            margin-bottom: 15px;
        }
        
        .feedback-box {
            margin-top: 20px;
            padding: 15px;
            border-radius: 6px;
            font-size: 0.95rem;
            display: none;
        }
        .feedback-box.show { display: block; animation: fadeIn 0.3s; }
        
        .controls {
            margin-top: 25px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        
        .btn-primary {
            background: var(--primary);
            color: white;
            padding: 12px 24px;
            border-radius: 6px;
            font-weight: 600;
        }
        .btn-primary:hover { opacity: 0.9; }
        
        .btn-secondary {
            background: transparent;
            border: 1px solid var(--border);
            color: var(--text-muted);
            padding: 10px 20px;
            border-radius: 6px;
        }
        .btn-secondary:hover { border-color: var(--text-main); color: var(--text-main); }

        .hidden { display: none !important; }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

    </style>
</head>
<body>

    <header>
        <button class="icon-btn" onclick="toggleMenu()">
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="3" y1="12" x2="21" y2="12"></line><line x1="3" y1="6" x2="21" y2="6"></line><line x1="3" y1="18" x2="21" y2="18"></line></svg>
        </button>
        <div class="brand">Physik Trainer Pro</div>
        <button class="icon-btn" onclick="toggleTheme()">
            <svg id="theme-icon" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="5"></circle><line x1="12" y1="1" x2="12" y2="3"></line><line x1="12" y1="21" x2="12" y2="23"></line><line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line><line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line><line x1="1" y1="12" x2="3" y2="12"></line><line x1="21" y1="12" x2="23" y2="12"></line><line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line><line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line></svg>
        </button>
    </header>

    <nav id="side-menu">
        <div class="search-container">
            <input type="text" id="search-input" placeholder="Themen filtern..." onkeyup="filterMenu()">
        </div>

        <div class="menu-group">
            <div class="menu-label">Lernmethoden</div>
            <button class="menu-item" onclick="startQuiz('mc')">📝 Multiple Choice (20+)</button>
            <button class="menu-item" onclick="startQuiz('bool')">⚖️ Ja / Nein (20+)</button>
            <button class="menu-item" onclick="startQuiz('match')">🔗 Formel-Chaos (20+)</button>
            <button class="menu-item" onclick="startQuiz('fill')">✍️ Lückentext (20+)</button>
        </div>

        <div class="menu-group">
            <div class="menu-label">Herausforderung</div>
            <button class="menu-item" onclick="startQuiz('calc')">🧮 Mathe-Profi (Unendlich)</button>
        </div>
    </nav>

    <main>
        <div id="start-screen" class="card">
            <h2>Willkommen zurück.</h2>
            <p>Wähle im Menü eine Kategorie. Alle Aufgaben basieren auf deinem Physik-Spickzettel. Wir decken ab:</p>
            <ul style="color: var(--text-muted); font-size: 0.9rem;">
                <li>Das Ohmsche Gesetz (U, R, I)</li>
                <li>Elektrische Leistung & Energie</li>
                <li>Reihen- & Parallelschaltung</li>
                <li>Spezifischer Widerstand</li>
            </ul>
        </div>

        <div id="quiz-interface" class="card hidden">
            <div class="progress-container">
                <div class="progress-fill" id="progress-bar"></div>
            </div>

            <div class="quiz-header">
                <span style="font-size: 0.8rem; text-transform: uppercase; color: var(--accent); font-weight: bold;" id="quiz-category-label">Kategorie</span>
                <h3 id="question-text" class="question-text">Frage lädt...</h3>
            </div>

            <div id="interaction-area"></div>

            <div id="feedback" class="feedback-box"></div>

            <div class="controls">
                <button class="btn-secondary" onclick="showHint()" id="hint-btn">Tipp anzeigen</button>
                <button class="btn-primary hidden" onclick="nextQuestion()" id="next-btn">Nächste Frage</button>
                <button class="btn-primary hidden" onclick="checkInputAnswer()" id="check-btn">Prüfen</button>
            </div>
        </div>

        <div id="result-screen" class="card hidden" style="text-align: center;">
            <h2>Zusammenfassung</h2>
            <div style="font-size: 3rem; font-weight: bold; margin: 20px 0; color: var(--primary);" id="final-score">0/20</div>
            <p id="result-message" style="margin-bottom: 30px;"></p>
            <button class="btn-primary" onclick="location.reload()">Zurück zum Menü</button>
        </div>
    </main>

    <script>
        // --- DATA POOL ---

        // 1. Multiple Choice (Konzepte, Einheiten, Zusammenhänge)
        const poolMC = [
            { q: "Was besagt das Ohmsche Gesetz?", options: ["U = R · I", "U = R / I", "I = R · U", "R = U · I"], a: 0, hint: "Spannung = Widerstand mal Stromstärke." },
            { q: "Welches Formelzeichen steht für die elektrische Ladung?", options: ["Q", "C", "L", "I"], a: 0, hint: "Denke an 'Quantity' oder Coulomb." },
            { q: "In welcher Einheit wird der elektrische Widerstand gemessen?", options: ["Ohm (Ω)", "Volt (V)", "Ampere (A)", "Watt (W)"], a: 0, hint: "Das Symbol sieht aus wie ein Hufeisen." },
            { q: "Was gilt in einer Reihenschaltung für die Stromstärke I?", options: ["Ist überall gleich", "Addiert sich", "Ist null", "Teilt sich auf"], a: 0, hint: "Es gibt nur einen Weg für die Elektronen." },
            { q: "Wie verhält sich die Spannung in einer Parallelschaltung?", options: ["Ist überall gleich", "Addiert sich zur Gesamtspannung", "Ist abhängig vom Widerstand", "Ist immer 230V"], a: 0, hint: "Alle Bauteile hängen direkt an der Quelle." },
            { q: "Die Formel P = U · I berechnet welche Größe?", options: ["Elektrische Leistung", "Elektrische Arbeit", "Widerstand", "Spannung"], a: 0, hint: "P steht für Power." },
            { q: "Was ist die Einheit der elektrischen Energie?", options: ["Joule (Ws)", "Watt (W)", "Volt (V)", "Ampere (A)"], a: 0, hint: "Leistung mal Zeit." },
            { q: "Wie berechnet man den Gesamtwiderstand in Reihe?", options: ["R1 + R2", "1/R1 + 1/R2", "R1 · R2", "R1 - R2"], a: 0, hint: "Einfach addieren." },
            { q: "Was bedeutet das Formelzeichen 'ρ' (Rho)?", options: ["Spezifischer Widerstand", "Dichte", "Radius", "Leistung"], a: 0, hint: "Materialkonstante für den Widerstand." },
            { q: "Welche Größe wird in Ampere (A) angegeben?", options: ["Stromstärke", "Spannung", "Ladung", "Energie"], a: 0, hint: "Der Fluss der Elektronen." },
            { q: "Wie lautet die Formel für Widerstand mit Drahtlänge?", options: ["R = ρ · l / A", "R = ρ · A / l", "R = l · A / ρ", "R = U / P"], a: 0, hint: "Je länger, desto mehr Widerstand. Je dicker, desto weniger." },
            { q: "Was passiert mit dem Gesamtwiderstand bei Parallelschaltung?", options: ["Er wird kleiner als der kleinste Einzelwiderstand", "Er addiert sich", "Er verdoppelt sich", "Er bleibt gleich"], a: 0, hint: "Mehr Wege bedeuten einfacheren Fluss." },
            { q: "Die Formel I = Q / t beschreibt...", options: ["Stromstärke als Ladung pro Zeit", "Spannung als Energie pro Ladung", "Leistung als Arbeit pro Zeit", "Widerstand"], a: 0, hint: "Wie viele Elektronen fließen pro Sekunde?" },
            { q: "Was ist 1 kΩ in Ohm?", options: ["1000 Ω", "100 Ω", "10 Ω", "10000 Ω"], a: 0, hint: "k steht für Kilo." },
            { q: "Welche Formel für Leistung ist korrekt?", options: ["P = I² · R", "P = U / R", "P = R / I²", "P = U · R"], a: 0, hint: "Setze U=R·I in P=U·I ein." },
            { q: "U = P / I ist eine Umstellung der...", options: ["Leistungsformel", "Widerstandsformel", "Energieformel", "Ladungsformel"], a: 0, hint: "P = U · I nach U aufgelöst." },
            { q: "Wofür steht 't' in den Formeln?", options: ["Zeit", "Temperatur", "Ton", "Tesla"], a: 0, hint: "Time (in Sekunden)." },
            { q: "Was ist die Formel für elektrische Arbeit?", options: ["E = P · t", "E = U / t", "E = R · I", "E = Q / U"], a: 0, hint: "Energie ist Leistung über Zeit." },
            { q: "In einer Reihenschaltung von zwei gleichen Lampen...", options: ["teilt sich die Spannung auf beide auf", "leuchten beide heller als eine einzelne", "fließt unterschiedlicher Strom", "ist die Spannung überall gleich"], a: 0, hint: "U_ges = U1 + U2." },
            { q: "Der Querschnitt A eines Leiters wird angegeben in...", options: ["mm²", "m", "cm", "Ω"], a: 0, hint: "Es ist eine Fläche." }
        ];

        // 2. Boolean (Schnelle Fakten)
        const poolBool = [
            { q: "In der Parallelschaltung ist die Spannung an allen Widerständen gleich.", a: true, hint: "U_ges = U1 = U2" },
            { q: "Die Einheit der elektrischen Spannung ist Watt.", a: false, hint: "Spannung wird in Volt gemessen." },
            { q: "Der Gesamtwiderstand in Reihe ist die Summe der Einzelwiderstände.", a: true, hint: "R_ges = R1 + R2 + ..." },
            { q: "Stromstärke wird in Volt gemessen.", a: false, hint: "Ampere ist richtig." },
            { q: "Elektrische Arbeit E berechnet sich aus Leistung mal Zeit.", a: true, hint: "E = P · t" },
            { q: "Ein dickerer Draht (größeres A) hat einen höheren Widerstand.", a: false, hint: "Mehr Platz für Elektronen = weniger Widerstand." },
            { q: "Kupfer hat einen spezifischen Widerstand.", a: true, hint: "Jedes Material leitet anders." },
            { q: "I = U / R ist eine Form des Ohmschen Gesetzes.", a: true, hint: "Ja, nach I umgestellt." },
            { q: "In einer Reihenschaltung addieren sich die Stromstärken.", a: false, hint: "Der Strom muss überall durch." },
            { q: "P = U² / R ist eine korrekte Leistungsformel.", a: true, hint: "Durch Einsetzen hergeleitet." },
            { q: "Die elektrische Ladung hat das Formelzeichen Q.", a: true, hint: "Korrekt." },
            { q: "Wenn man die Spannung erhöht (bei gleichem R), sinkt der Strom.", a: false, hint: "Mehr Druck (U) = mehr Fluss (I)." },
            { q: "1 Wattsekunde (Ws) entspricht 1 Joule.", a: true, hint: "Das ist die Definition." },
            { q: "Der Kehrwert des Gesamtwiderstands in Parallel ist die Summe der Kehrwerte.", a: true, hint: "1/Rges = 1/R1 + 1/R2" },
            { q: "Länge des Leiters hat keinen Einfluss auf den Widerstand.", a: false, hint: "Je länger, desto anstrengender für Elektronen." },
            { q: "Ein Voltmeter wird immer in Reihe geschaltet.", a: false, hint: "Spannung misst man parallel." },
            { q: "Ein Amperemeter wird in Reihe geschaltet.", a: true, hint: "Der Strom muss durch das Messgerät fließen." },
            { q: "E = U · Q ist eine Formel für Energie.", a: true, hint: "Siehe Spickzettel unten links." },
            { q: "Spezifischer Widerstand wird in Ωm oder Ωmm²/m angegeben.", a: true, hint: "Materialabhängig." },
            { q: "Bei unterbrochenem Stromkreis fließt trotzdem Strom.", a: false, hint: "Logisch, oder?" }
        ];

        // 3. Formula Matching (Was gehört zusammen?)
        const poolMatch = [
            { q: "Gesuchte Größe: U (Spannung)", options: ["R · I", "P · t", "Q / t", "ρ · l"], a: 0 },
            { q: "Gesuchte Größe: P (Leistung)", options: ["U · I", "U / I", "R · t", "E / U"], a: 0 },
            { q: "Gesuchte Größe: R (Widerstand)", options: ["ρ · l / A", "U · I", "P · t", "Q · U"], a: 0 },
            { q: "Gesuchte Größe: E (Energie)", options: ["P · t", "U / I", "R / t", "I / U"], a: 0 },
            { q: "Reihenschaltung: U_ges", options: ["U1 + U2", "U1 = U2", "U1 · U2", "U1 / U2"], a: 0 },
            { q: "Parallelschaltung: I_ges", options: ["I1 + I2", "I1 = I2", "I1 · I2", "I1 - I2"], a: 0 },
            { q: "Gesuchte Größe: I (Strom)", options: ["U / R", "R / U", "P · U", "E · t"], a: 0 },
            { q: "Formel für: P mit R und I", options: ["I² · R", "U² · R", "R / I", "I / R"], a: 0 },
            { q: "Formel für: P mit U und R", options: ["U² / R", "U · R", "R / U", "U² · I"], a: 0 },
            { q: "Formel für: I mit Q und t", options: ["Q / t", "Q · t", "t / Q", "Q + t"], a: 0 },
            { q: "Formel für: U mit E und Q", options: ["E / Q", "E · Q", "Q / E", "E + Q"], a: 0 },
            { q: "Reihenschaltung: R_ges", options: ["R1 + R2", "1/R1 + 1/R2", "R1 · R2", "R1 / R2"], a: 0 },
            { q: "Parallelschaltung: U_ges", options: ["U1 = U2", "U1 + U2", "U1 / U2", "0"], a: 0 },
            { q: "Reihenschaltung: I_ges", options: ["I1 = I2", "I1 + I2", "I1 / I2", "I1 · I2"], a: 0 },
            { q: "Einheit: Elektrische Ladung", options: ["Coulomb (As)", "Watt", "Volt", "Hertz"], a: 0 },
            { q: "Einheit: Spezifischer Widerstand", options: ["Ω · mm² / m", "Ω / m", "A · m", "V / m"], a: 0 },
            { q: "Berechnung: Zeit t aus I und Q", options: ["Q / I", "Q · I", "I / Q", "Q²"], a: 0 },
            { q: "Berechnung: R aus P und I", options: ["P / I²", "P · I", "I / P", "P²"], a: 0 },
            { q: "Berechnung: U aus P und R", options: ["√(P · R)", "P · R", "P / R", "R²"], a: 0 },
            { q: "Symbol für Querschnitt", options: ["A", "Q", "d", "V"], a: 0 }
        ];

        // 4. Lückentext (Strings)
        const poolFill = [
            { q: "Das Ohmsche Gesetz lautet U = R · ...", a: "I", hint: "Stromstärke" },
            { q: "Die Einheit der Spannung ist ...", a: "Volt", hint: "V" },
            { q: "Bei einer ...schaltung ist die Spannung überall gleich.", a: "Parallel", hint: "Gegenteil von Reihe" },
            { q: "Elektrische Leistung P wird in der Einheit ... gemessen.", a: "Watt", hint: "James ..." },
            { q: "R = ρ · l / ...", a: "A", hint: "Querschnittsfläche" },
            { q: "In einer Reihenschaltung addieren sich die ...stände.", a: "Wider", hint: "Wider..." },
            { q: "Formelzeichen für die Zeit ist ...", a: "t", hint: "Kleinbuchstabe" },
            { q: "Energie E = P · ...", a: "t", hint: "Zeit" },
            { q: "Stromstärke I = ... / R", a: "U", hint: "Spannung" },
            { q: "Die Einheit für Widerstand ist ...", a: "Ohm", hint: "Ω" },
            { q: "U_ges = U1 + U2 gilt für die ...schaltung.", a: "Reihen", hint: "Nicht parallel" },
            { q: "I_ges = I1 + I2 gilt für die ...schaltung.", a: "Parallel", hint: "Ströme teilen sich auf" },
            { q: "Das Formelzeichen für elektrische Arbeit ist ...", a: "E", hint: "Oder W (Energy)" },
            { q: "Spezifischer Widerstand wird mit dem Buchstaben ... abgekürzt.", a: "Rho", hint: "Griechischer Buchstabe" },
            { q: "Die Ladung Q hat die Einheit ...", a: "Coulomb", hint: "Oder As" },
            { q: "Ein Voltmeter misst die ...", a: "Spannung", hint: "Größe U" },
            { q: "1000 Watt sind ein ...", a: "Kilowatt", hint: "kW" },
            { q: "P = U · ...", a: "I", hint: "Strom" },
            { q: "R = U / ...", a: "I", hint: "Strom" },
            { q: "Wenn R steigt und U gleich bleibt, wird I ...", a: "kleiner", hint: "Sinkt oder steigt?" }
        ];


        // 5. COMPLEX CALCULATOR GENERATOR
        // Generiert endlose Mathe-Aufgaben
        function generateMathProblem() {
            const types = ['ohm', 'power', 'series', 'parallel', 'wire', 'energy'];
            const type = types[Math.floor(Math.random() * types.length)];
            let qText = "", correctVal = 0, unit = "", hintText = "";

            const rand = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min;
            const fix = (n) => parseFloat(n.toFixed(2));

            if (type === 'ohm') {
                // U = R * I
                const missing = Math.random() < 0.33 ? 'U' : (Math.random() < 0.5 ? 'R' : 'I');
                const R = rand(5, 100);
                const I = rand(1, 10);
                const U = R * I;
                
                if(missing === 'U') {
                    qText = `Berechne die Spannung U, wenn R = ${R} Ω und I = ${I} A.`;
                    correctVal = U; unit = "V"; hintText = "U = R · I";
                } else if (missing === 'R') {
                    qText = `Berechne den Widerstand R, wenn U = ${U} V und I = ${I} A.`;
                    correctVal = R; unit = "Ω"; hintText = "R = U / I";
                } else {
                    qText = `Berechne die Stromstärke I, wenn U = ${U} V und R = ${R} Ω.`;
                    correctVal = I; unit = "A"; hintText = "I = U / R";
                }
            } 
            else if (type === 'power') {
                // P = U * I
                const U = rand(10, 230);
                const I = rand(1, 5);
                const P = U * I;
                // Variation P = I^2 * R
                const R = rand(10, 50);
                
                const subType = Math.random();
                if(subType < 0.33) {
                    qText = `Ein Gerät verbraucht bei ${U} V einen Strom von ${I} A. Berechne die Leistung P.`;
                    correctVal = P; unit = "W"; hintText = "P = U · I";
                } else if (subType < 0.66) {
                    qText = `Ein Widerstand von ${R} Ω wird von ${I} A durchflossen. Berechne die Leistung P.`;
                    correctVal = I * I * R; unit = "W"; hintText = "P = I² · R";
                } else {
                    qText = `Ein Gerät hat 200 W Leistung und läuft bei 50 V. Wie groß ist der Widerstand R?`;
                    // P = U^2 / R -> R = U^2 / P
                    // Feste Werte für saubere Zahlen
                    const uFix = 50; const pFix = 200;
                    correctVal = (uFix * uFix) / pFix; unit = "Ω"; hintText = "R = U² / P";
                }
            }
            else if (type === 'series') {
                const r1 = rand(10, 50);
                const r2 = rand(10, 50);
                qText = `Reihenschaltung: R1 = ${r1} Ω, R2 = ${r2} Ω. Berechne den Gesamtwiderstand.`;
                correctVal = r1 + r2; unit = "Ω"; hintText = "Einfach addieren.";
            }
            else if (type === 'parallel') {
                const r1 = rand(10, 60);
                const r2 = rand(10, 60);
                qText = `Parallelschaltung: R1 = ${r1} Ω, R2 = ${r2} Ω. Berechne R_ges (auf 2 Stellen runden).`;
                // 1/Rges = 1/R1 + 1/R2 -> Rges = (R1*R2)/(R1+R2)
                correctVal = (r1 * r2) / (r1 + r2);
                unit = "Ω"; hintText = "R_ges = (R1 · R2) / (R1 + R2)";
            }
            else if (type === 'wire') {
                // R = rho * l / A
                const rho = 0.017; // Kupfer ca.
                const l = rand(50, 500); // Meter
                const A = rand(1, 4); // mm2
                qText = `Kupferdraht (ρ ≈ 0.017): Länge l = ${l} m, Querschnitt A = ${A} mm². Berechne R.`;
                correctVal = (rho * l) / A;
                unit = "Ω"; hintText = "R = ρ · l / A";
            }
            else if (type === 'energy') {
                // E = P * t
                const P = rand(50, 2000);
                const t = rand(1, 10); // Stunden
                qText = `Ein Gerät leistet ${P} Watt für ${t} Stunden. Berechne die Energie in Wattstunden (Wh).`;
                correctVal = P * t; unit = "Wh"; hintText = "E = P · t";
            }

            return {
                q: qText,
                a: fix(correctVal),
                unit: unit,
                hint: hintText,
                type: 'calc'
            };
        }


        // --- APP LOGIC ---

        let currentMode = '';
        let quizQueue = [];
        let currentIndex = 0;
        let score = 0;

        function toggleMenu() {
            document.getElementById('side-menu').classList.toggle('open');
        }

        function toggleTheme() {
            document.body.classList.toggle('dark-mode');
        }

        function filterMenu() {
            const term = document.getElementById('search-input').value.toLowerCase();
            const btns = document.querySelectorAll('.menu-item');
            btns.forEach(btn => {
                const txt = btn.innerText.toLowerCase();
                btn.style.display = txt.includes(term) ? 'block' : 'none';
            });
        }

        function startQuiz(mode) {
            currentMode = mode;
            toggleMenu();
            
            // Vorbereitung
            score = 0;
            currentIndex = 0;
            
            // Queue füllen
            if(mode === 'calc') {
                // Generiere 20 frische Aufgaben
                quizQueue = [];
                for(let i=0; i<20; i++) quizQueue.push(generateMathProblem());
                document.getElementById('quiz-category-label').innerText = "Mathe-Profi (Schwer)";
            } else {
                let source = [];
                let label = "";
                if(mode === 'mc') { source = poolMC; label = "Multiple Choice"; }
                if(mode === 'bool') { source = poolBool; label = "Ja / Nein"; }
                if(mode === 'match') { source = poolMatch; label = "Formel Zuordnung"; }
                if(mode === 'fill') { source = poolFill; label = "Lückentext"; }
                
                // Shuffle und nimm 20
                quizQueue = [...source].sort(() => 0.5 - Math.random()).slice(0, 20);
                document.getElementById('quiz-category-label').innerText = label;
            }

            document.getElementById('start-screen').classList.add('hidden');
            document.getElementById('result-screen').classList.add('hidden');
            document.getElementById('quiz-interface').classList.remove('hidden');
            
            loadQuestion();
        }

        function loadQuestion() {
            const data = quizQueue[currentIndex];
            const qEl = document.getElementById('question-text');
            const area = document.getElementById('interaction-area');
            const feedback = document.getElementById('feedback');
            const nextBtn = document.getElementById('next-btn');
            const checkBtn = document.getElementById('check-btn');
            const hintBtn = document.getElementById('hint-btn');

            // Reset UI
            feedback.style.display = 'none';
            feedback.className = 'feedback-box';
            area.innerHTML = '';
            nextBtn.classList.add('hidden');
            checkBtn.classList.add('hidden');
            hintBtn.disabled = false;
            qEl.innerText = data.q;

            // Progress Update
            const pct = (currentIndex / quizQueue.length) * 100;
            document.getElementById('progress-bar').style.width = pct + "%";

            // Render Inputs
            if (currentMode === 'mc' || currentMode === 'match') {
                const grid = document.createElement('div');
                grid.className = 'options-grid';
                
                // Optionen mischen für MC, aber nicht für Match wenn es Strings sind? 
                // Einfachheitshalber: Optionen in Data Object sind fest, wir mappen sie.
                // data.options enthält Strings. data.a ist der Index der richtigen Lösung.
                
                // Wir müssen die Optionen mit ihren originalen Indexen speichern, um zu prüfen
                let indexedOptions = data.options.map((val, idx) => ({val, idx}));
                // Mischen
                indexedOptions.sort(() => 0.5 - Math.random());

                indexedOptions.forEach(opt => {
                    const btn = document.createElement('button');
                    btn.className = 'btn-option';
                    btn.innerText = opt.val;
                    btn.onclick = () => resolveChoice(btn, opt.idx === data.a, data.explanation || "");
                    grid.appendChild(btn);
                });
                area.appendChild(grid);

            } else if (currentMode === 'bool') {
                const grid = document.createElement('div');
                grid.className = 'options-grid';
                
                const btnYes = document.createElement('button');
                btnYes.className = 'btn-option';
                btnYes.innerText = "Ja / Richtig";
                btnYes.onclick = () => resolveChoice(btnYes, data.a === true, "");

                const btnNo = document.createElement('button');
                btnNo.className = 'btn-option';
                btnNo.innerText = "Nein / Falsch";
                btnNo.onclick = () => resolveChoice(btnNo, data.a === false, "");

                grid.appendChild(btnYes);
                grid.appendChild(btnNo);
                area.appendChild(grid);

            } else if (currentMode === 'fill' || currentMode === 'calc') {
                const input = document.createElement('input');
                input.type = currentMode === 'calc' ? 'number' : 'text';
                input.className = 'input-field';
                input.placeholder = currentMode === 'calc' ? 'Zahl eingeben...' : 'Antwort...';
                input.id = 'user-input';
                
                // Enter Taste support
                input.addEventListener("keypress", function(event) {
                    if (event.key === "Enter") checkInputAnswer();
                });

                area.appendChild(input);
                checkBtn.classList.remove('hidden');
            }
        }

        function resolveChoice(btnElement, isCorrect, explanation) {
            // Alle Buttons deaktivieren
            const btns = document.querySelectorAll('.btn-option');
            btns.forEach(b => b.disabled = true);

            const feedback = document.getElementById('feedback');
            feedback.style.display = 'block';

            if (isCorrect) {
                score++;
                btnElement.classList.add('correct');
                feedback.innerHTML = `<span style="color:var(--success); font-weight:bold">Richtig!</span> ${explanation}`;
                feedback.style.border = "1px solid var(--success)";
                feedback.style.background = "rgba(39, 174, 96, 0.1)";
            } else {
                btnElement.classList.add('wrong');
                feedback.innerHTML = `<span style="color:var(--error); font-weight:bold">Falsch.</span> ${explanation}`;
                feedback.style.border = "1px solid var(--error)";
                feedback.style.background = "rgba(192, 57, 43, 0.1)";
            }
            
            document.getElementById('next-btn').classList.remove('hidden');
            document.getElementById('hint-btn').disabled = true;
        }

        function checkInputAnswer() {
            const input = document.getElementById('user-input');
            const val = input.value.trim();
            if(!val) return;

            const data = quizQueue[currentIndex];
            let isCorrect = false;

            if (currentMode === 'calc') {
                // Toleranz von +/- 0.1 oder 5%
                const numVal = parseFloat(val.replace(',', '.')); // Komma support
                const correct = data.a;
                const tolerance = Math.max(0.1, correct * 0.05);
                if (Math.abs(numVal - correct) <= tolerance) isCorrect = true;
            } else {
                // String Vergleich (Lückentext)
                if (val.toLowerCase() === data.a.toLowerCase()) isCorrect = true;
            }

            const feedback = document.getElementById('feedback');
            feedback.style.display = 'block';
            input.disabled = true;
            document.getElementById('check-btn').classList.add('hidden');

            if(isCorrect) {
                score++;
                feedback.innerHTML = `<span style="color:var(--success); font-weight:bold">Richtig!</span>`;
                feedback.style.border = "1px solid var(--success)";
                feedback.style.background = "rgba(39, 174, 96, 0.1)";
            } else {
                feedback.innerHTML = `<span style="color:var(--error); font-weight:bold">Falsch.</span> Lösung: ${data.a} ${data.unit || ''}`;
                feedback.style.border = "1px solid var(--error)";
                feedback.style.background = "rgba(192, 57, 43, 0.1)";
            }

            document.getElementById('next-btn').classList.remove('hidden');
        }

        function showHint() {
            const data = quizQueue[currentIndex];
            alert("Tipp: " + (data.hint || "Kein Tipp verfügbar."));
        }

        function nextQuestion() {
            currentIndex++;
            if(currentIndex < quizQueue.length) {
                loadQuestion();
            } else {
                showResults();
            }
        }

        function showResults() {
            document.getElementById('quiz-interface').classList.add('hidden');
            document.getElementById('result-screen').classList.remove('hidden');
            
            const pct = Math.round((score / quizQueue.length) * 100);
            document.getElementById('final-score').innerText = `${score} / ${quizQueue.length}`;
            
            let msg = "";
            if(pct === 100) msg = "Perfekt! Du hast alles gemeistert.";
            else if(pct >= 80) msg = "Sehr gut! Du bist fit für die Arbeit.";
            else if(pct >= 50) msg = "Ganz okay, aber übe noch ein bisschen.";
            else msg = "Das war noch nichts. Schau dir den Spickzettel nochmal genau an.";
            
            document.getElementById('result-message').innerText = msg;
        }

    </script>
</body>
</html>
