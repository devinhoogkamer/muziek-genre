<!DOCTYPE html>
<html lang="nl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Muziek Genre Master</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/tone/14.8.49/Tone.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&family=Outfit:wght@400;600;800&display=swap');

        body {
            font-family: 'Outfit', sans-serif;
            background-color: #0f172a;
            color: #f8fafc;
            overflow-x: hidden;
            touch-action: manipulation;
            user-select: none;
        }

        .pixel-font {
            font-family: 'Press Start 2P', cursive;
        }

        @keyframes pulse-energy {
            0% { transform: scale(1); box-shadow: 0 0 10px #f59e0b; }
            50% { transform: scale(1.08); box-shadow: 0 0 25px #fbbf24, 0 0 35px #f59e0b; }
            100% { transform: scale(1); box-shadow: 0 0 10px #f59e0b; }
        }

        .energy-pulse {
            animation: pulse-energy 1.2s infinite;
        }

        @keyframes float {
            0% { transform: translateY(0px); }
            50% { transform: translateY(-8px); }
            100% { transform: translateY(0px); }
        }

        .floating {
            animation: float 3s ease-in-out infinite;
        }

        .screen {
            display: none;
            flex-direction: column;
            align-items: center;
            justify-content: flex-start;
            min-height: 100vh;
            width: 100vw;
            position: absolute;
            top: 0;
            left: 0;
            padding: 5rem 1rem 2rem 1rem;
            box-sizing: border-box;
        }

        .screen.active {
            display: flex;
        }

        .btn {
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .btn:hover:not(:disabled) {
            transform: translateY(-2px) scale(1.03);
            filter: brightness(1.2);
        }
        .btn:active:not(:disabled) {
            transform: translateY(1px) scale(0.97);
        }

        #minigame-canvas {
            display: block;
            background: linear-gradient(to bottom, #0f172a, #1e1b4b);
            border-radius: 16px;
            box-shadow: 0 15px 30px rgba(0,0,0,0.6);
            width: 100%;
            max-width: 600px;
            height: auto;
            border: 3px solid #6366f1;
        }

        .seq-step {
            width: 32px;
            height: 32px;
            border-radius: 6px;
            cursor: pointer;
            transition: all 0.15s ease;
        }
        .seq-step.active {
            background-color: #38bdf8;
            box-shadow: 0 0 12px #38bdf8;
        }
        .seq-step.playing {
            border: 2px solid #facc15;
        }
    </style>
</head>
<body>

    <div id="hud" class="fixed top-0 left-0 w-full p-3 md:p-4 flex justify-between items-center z-50 bg-slate-900/90 backdrop-blur-md border-b border-slate-700">
        <div class="flex items-center space-x-3 md:space-x-4">
            <div class="flex items-center space-x-1 bg-amber-500/20 border border-amber-500/40 px-3 py-1.5 rounded-full">
                <span class="text-xl">⚡</span>
                <span class="pixel-font text-amber-400 text-sm md:text-lg" id="energy-display">30</span>
            </div>
            <div class="flex items-center space-x-1 bg-indigo-500/20 border border-indigo-500/40 px-3 py-1.5 rounded-full">
                <span class="text-xs font-bold text-indigo-300">SCORE:</span>
                <span class="pixel-font text-indigo-300 text-xs md:text-base" id="score-display">0</span>
            </div>
            <div class="hidden sm:flex items-center space-x-1 bg-emerald-500/20 border border-emerald-500/40 px-3 py-1.5 rounded-full">
                <span class="text-xs font-bold text-emerald-300">NIVEAU:</span>
                <span class="pixel-font text-emerald-400 text-xs" id="level-display">1 (Beginner)</span>
            </div>
        </div>

        <div class="flex space-x-2">
            <button onclick="switchScreen('quiz')" class="bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-1.5 px-3 md:px-4 rounded-xl text-xs md:text-sm shadow-md btn">
                🎧 Quiz
            </button>
            <button onclick="switchScreen('hub')" class="bg-purple-600 hover:bg-purple-500 text-white font-bold py-1.5 px-3 md:px-4 rounded-xl text-xs md:text-sm shadow-md btn">
                🎮 Hub
            </button>
        </div>
    </div>

    <div id="screen-start" class="screen active bg-gradient-to-br from-slate-950 via-indigo-950 to-purple-950 flex flex-col justify-center items-center">
        <h1 class="pixel-font text-2xl md:text-5xl text-center text-transparent bg-clip-text bg-gradient-to-r from-cyan-400 via-fuchsia-400 to-amber-400 mb-6 floating">
            Muziek Genre Master
        </h1>
        <p class="text-sm md:text-lg text-slate-300 mb-8 text-center max-w-2xl px-4">
            Herken genres, speel uitdagende minigames, verdien ⚡ energie en bouw je eigen beats!
        </p>

        <div class="bg-slate-900/80 border border-slate-700 p-6 rounded-2xl max-w-md w-full mb-8 text-center">
            <h3 class="text-lg font-bold text-cyan-300 mb-3">Kies je Startniveau:</h3>
            <div class="grid grid-cols-3 gap-2">
                <button onclick="setDifficulty(1)" id="diff-btn-1" class="bg-emerald-600 text-white font-bold py-2 px-2 rounded-xl text-xs btn border-2 border-emerald-300">
                    Niveau 1<br><span class="text-[10px] opacity-80">3 Genres</span>
                </button>
                <button onclick="setDifficulty(2)" id="diff-btn-2" class="bg-slate-800 text-slate-400 font-bold py-2 px-2 rounded-xl text-xs btn border-2 border-transparent">
                    Niveau 2<br><span class="text-[10px] opacity-80">6 Genres</span>
                </button>
                <button onclick="setDifficulty(3)" id="diff-btn-3" class="bg-slate-800 text-slate-400 font-bold py-2 px-2 rounded-xl text-xs btn border-2 border-transparent">
                    Niveau 3<br><span class="text-[10px] opacity-80">9 Genres</span>
                </button>
            </div>
        </div>

        <button onclick="switchScreen('quiz')" class="bg-gradient-to-r from-emerald-400 to-cyan-500 text-slate-950 font-extrabold py-4 px-10 rounded-full text-xl md:text-2xl shadow-xl btn energy-pulse">
            Start het Spel!
        </button>
    </div>

    <div id="screen-quiz" class="screen bg-gradient-to-br from-slate-900 to-indigo-950">
        <div class="w-full max-w-2xl bg-slate-800/90 border border-slate-700 rounded-3xl p-5 md:p-8 shadow-2xl mt-4">
            <div class="flex justify-between items-center mb-4">
                <div>
                    <span class="text-xs font-bold text-indigo-400 uppercase tracking-widest">Luister & Raad</span>
                    <h2 class="text-xl md:text-2xl font-bold text-cyan-300" id="quiz-title">Welk Genre Hoor Je?</h2>
                </div>
                <div class="text-right">
                    <span class="text-xs text-slate-400">Streak:</span>
                    <div class="pixel-font text-amber-400 text-base md:text-lg" id="quiz-streak">0 🔥</div>
                </div>
            </div>

            <!-- Visualizer & Sound trigger -->
            <div class="flex flex-col items-center mb-6 p-4 bg-slate-900/60 rounded-2xl border border-slate-700/50">
                <canvas id="visualizer-canvas" width="300" height="60" class="mb-3 rounded-lg w-full max-w-xs bg-slate-950 border border-slate-800"></canvas>
                
                <div id="play-sound-btn" onclick="playQuizSound()" class="w-20 h-20 rounded-full bg-gradient-to-tr from-cyan-500 to-indigo-600 flex items-center justify-center cursor-pointer shadow-lg hover:shadow-cyan-500/30 transition-all transform hover:scale-105 mb-2 group">
                    <span id="play-icon" class="text-3xl group-hover:scale-110 transition-transform">▶️</span>
                </div>
                <p class="text-slate-300 text-xs md:text-sm font-medium" id="listen-text">Klik om de audio-sample te beluisteren</p>
            </div>

            <div id="options-container" class="grid grid-cols-1 md:grid-cols-2 gap-3 mb-4">
                <!-- Dynamically populated options -->
            </div>

            <div id="feedback-message" class="text-center text-sm md:text-base font-bold min-h-[28px]"></div>
            <div id="learning-info" class="mt-2 p-3 bg-indigo-950/80 border border-indigo-500/40 rounded-xl text-indigo-200 text-xs md:text-sm hidden"></div>
            
            <div class="mt-4 flex justify-end">
                <button id="next-q-btn" onclick="generateQuestion()" class="hidden bg-emerald-500 hover:bg-emerald-400 text-slate-950 font-bold py-2 px-6 rounded-xl text-sm btn">
                    Volgende Vraag ➔
                </button>
            </div>
        </div>
    </div>

    <div id="screen-hub" class="screen bg-slate-950">
        <h2 class="pixel-font text-xl md:text-3xl text-amber-400 mb-6 text-center">Minigames Hub</h2>
        
        <!-- Niveau Aanpassen Paneel -->
        <div class="bg-slate-900 border border-slate-800 rounded-xl p-3 mb-6 max-w-3xl w-full flex flex-col sm:flex-row items-center justify-center sm:space-x-4">
            <span class="text-xs font-bold text-slate-400 mb-2 sm:mb-0">Moeilijkheid Aanpassen:</span>
            <div class="flex space-x-2">
                <button onclick="setDifficulty(1)" id="hub-diff-btn-1" class="bg-emerald-600 text-white font-bold py-1.5 px-3 rounded-lg text-xs btn border border-emerald-400">Niveau 1</button>
                <button onclick="setDifficulty(2)" id="hub-diff-btn-2" class="bg-slate-800 text-slate-400 font-bold py-1.5 px-3 rounded-lg text-xs btn border border-slate-700">Niveau 2</button>
                <button onclick="setDifficulty(3)" id="hub-diff-btn-3" class="bg-slate-800 text-slate-400 font-bold py-1.5 px-3 rounded-lg text-xs btn border border-slate-700">Niveau 3</button>
            </div>
        </div>
        
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6 max-w-5xl w-full">
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-6 flex flex-col justify-between hover:border-indigo-500 transition-colors">
                <div>
                    <div class="text-4xl mb-3">🏃‍♂️</div>
                    <h3 class="text-xl font-bold text-indigo-300 mb-2">Instrument Jumper</h3>
                    <p class="text-xs md:text-sm text-slate-400 mb-4">
                        Spring over de gaten! <br>
                        <span class="text-amber-400 font-bold">⚠️ Sprong kost 3⚡ Energie.</span><br>
                        <span class="text-slate-400">Verdien ⚡ energie in de Quiz!</span>
                    </p>
                </div>
                <button onclick="switchScreen('jumper')" class="bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-3 px-4 rounded-xl btn text-center w-full">
                    Speel Jumper!
                </button>
            </div>

            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-6 flex flex-col justify-between hover:border-purple-500 transition-colors">
                <div>
                    <div class="text-4xl mb-3">⏳</div>
                    <h3 class="text-xl font-bold text-purple-300 mb-2">Tijdlijn Uitdaging</h3>
                    <p class="text-xs md:text-sm text-slate-400 mb-4">
                        Zet muziekgenres in de juiste historische volgorde en verdien +20⚡ bonus-energie!
                    </p>
                </div>
                <button onclick="switchScreen('timeline')" class="bg-purple-600 hover:bg-purple-500 text-white font-bold py-3 px-4 rounded-xl btn text-center w-full">
                    Speel Tijdlijn!
                </button>
            </div>

            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-6 flex flex-col justify-between hover:border-cyan-500 transition-colors">
                <div>
                    <div class="text-4xl mb-3">🎛️</div>
                    <h3 class="text-xl font-bold text-cyan-300 mb-2">Beat Sequencer & Studio</h3>
                    <p class="text-xs md:text-sm text-slate-400 mb-4">
                        Maak je eigen 8-step beats, speel met sound effect pads en neem je eigen samples op!
                    </p>
                </div>
                <button onclick="switchScreen('soundlab')" class="bg-cyan-600 hover:bg-cyan-500 text-white font-bold py-3 px-4 rounded-xl btn text-center w-full">
                    Open Sound Lab!
                </button>
            </div>
        </div>
    </div>

    <div id="screen-jumper" class="screen bg-slate-950">
        <div class="w-full max-w-2xl flex flex-col items-center">
            <h2 class="pixel-font text-lg md:text-2xl text-amber-400 mb-2">Instrument Jumper Levels</h2>
            
            <div class="flex justify-center space-x-2 mb-3">
                <button onclick="setJumperStage(1)" id="jumper-stage-1" class="bg-indigo-600 text-white text-xs px-3 py-1.5 rounded-lg border border-indigo-400 font-bold btn">Level 1 (50m Goal)</button>
                <button onclick="setJumperStage(2)" id="jumper-stage-2" class="bg-slate-800 text-slate-400 text-xs px-3 py-1.5 rounded-lg border border-slate-700 font-bold btn">Level 2 (100m Goal)</button>
                <button onclick="setJumperStage(3)" id="jumper-stage-3" class="bg-slate-800 text-slate-400 text-xs px-3 py-1.5 rounded-lg border border-slate-700 font-bold btn">Level 3 (150m Goal)</button>
            </div>

            <div id="game-container" class="relative w-full flex flex-col items-center">
                <div id="game-overlay" class="absolute inset-0 bg-slate-950/90 flex flex-col items-center justify-center rounded-2xl z-10 p-4 text-center">
                    <p class="text-white text-base md:text-lg mb-4 font-bold" id="overlay-text">
                        Nodig: minimaal 3⚡ Energie om te springen!
                    </p>
                    <button id="btn-play-jumper" onclick="startJumperGame()" class="bg-emerald-500 hover:bg-emerald-400 text-slate-950 font-extrabold py-3 px-8 rounded-full text-lg shadow-lg btn mb-3">
                        Start Level!
                    </button>
                    <button onclick="switchScreen('quiz')" class="bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-2 px-6 rounded-full text-xs md:text-sm btn">
                        🎧 Beantwoord Vragen in Quiz (+10⚡)
                    </button>
                </div>
                
                <canvas id="minigame-canvas" width="600" height="350"></canvas>
                
                <div id="game-ui" class="absolute top-3 right-4 text-white font-mono text-xs md:text-sm hidden z-20 space-x-4 bg-slate-900/80 px-3 py-1 rounded-lg border border-slate-700">
                    <div>Level <span id="game-stage-num" class="text-cyan-400 font-bold">1</span></div>
                    <div>Afstand: <span id="game-score" class="text-amber-400 font-bold">0</span>/<span id="game-target" class="text-emerald-400 font-bold">50</span>m</div>
                </div>
            </div>
            
            <p class="mt-3 text-slate-400 text-xs text-center">
                Tik op het scherm of druk op <kbd class="bg-slate-800 px-1.5 py-0.5 rounded border border-slate-700">SPATIE</kbd> om te springen. Elke sprong kost 3⚡ energie!
            </p>
        </div>
    </div>

    <div id="screen-timeline" class="screen bg-slate-950">
        <div class="w-full max-w-2xl bg-slate-900 border border-slate-800 rounded-2xl p-6">
            <h2 class="pixel-font text-lg md:text-xl text-purple-400 mb-2 text-center">Tijdlijn Uitdaging</h2>
            <p class="text-slate-300 text-xs md:text-sm text-center mb-6">
                Klik op de genres om ze in de juiste chronologische volgorde van ontstaan te zetten (oudste ➔ nieuwste)!
            </p>

            <div id="timeline-slots" class="grid grid-cols-1 sm:grid-cols-4 gap-3 mb-6">
                <!-- Dynamic slots -->
            </div>

            <div id="timeline-options" class="flex flex-wrap justify-center gap-2 mb-6">
                <!-- Dynamic choices -->
            </div>

            <div class="flex justify-center space-x-3">
                <button onclick="checkTimeline()" class="bg-purple-600 hover:bg-purple-500 text-white font-bold py-2.5 px-6 rounded-xl text-xs md:text-sm btn">
                    Controleer Volgorde!
                </button>
                <button onclick="resetTimeline()" class="bg-slate-800 hover:bg-slate-700 text-slate-300 font-bold py-2.5 px-4 rounded-xl text-xs md:text-sm btn">
                    Reset
                </button>
            </div>
            <div id="timeline-feedback" class="mt-4 text-center font-bold min-h-[24px] text-xs md:text-sm"></div>
        </div>
    </div>

    <div id="screen-soundlab" class="screen bg-slate-950">
        <div class="w-full max-w-3xl bg-slate-900 border border-slate-800 rounded-2xl p-6">
            <h2 class="pixel-font text-lg md:text-xl text-cyan-400 mb-2 text-center">Beat Sequencer & Studio</h2>
            <p class="text-slate-400 text-xs text-center mb-6">Programmeer je eigen ritme op het 8-step grid en speel met effecten.</p>

            <div class="bg-slate-950 p-4 rounded-xl border border-slate-800 mb-6">
                <div class="flex justify-between items-center mb-4">
                    <button id="seq-play-btn" onclick="toggleSequencer()" class="bg-emerald-500 hover:bg-emerald-400 text-slate-950 font-bold py-2 px-6 rounded-lg text-xs md:text-sm btn">
                        ▶ Play Beat
                    </button>
                    <div class="flex items-center space-x-2">
                        <span class="text-xs text-slate-400 font-bold">BPM: <span id="bpm-val">120</span></span>
                        <input type="range" min="80" max="160" value="120" oninput="updateBpm(this.value)" class="w-24 accent-cyan-400">
                    </div>
                </div>

                <div class="space-y-3" id="sequencer-rows">
                    <!-- Rows generated dynamically -->
                </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div class="bg-slate-950 p-4 rounded-xl border border-slate-800">
                    <h4 class="text-xs font-bold text-slate-300 mb-3">SFX Sound Pads</h4>
                    <div class="grid grid-cols-3 gap-2">
                        <button onclick="playSFX('clap')" class="bg-slate-800 hover:bg-indigo-600 text-white text-xs font-bold py-3 rounded-lg btn">👏 Clap</button>
                        <button onclick="playSFX('laser')" class="bg-slate-800 hover:bg-cyan-600 text-white text-xs font-bold py-3 rounded-lg btn">🔫 Laser</button>
                        <button onclick="playSFX('coin')" class="bg-slate-800 hover:bg-amber-600 text-white text-xs font-bold py-3 rounded-lg btn">🪙 Coin</button>
                    </div>
                </div>

                <div class="bg-slate-950 p-4 rounded-xl border border-slate-800 flex flex-col justify-between">
                    <h4 class="text-xs font-bold text-slate-300 mb-1">Microfoon Recorder</h4>
                    <p class="text-[11px] text-slate-500 mb-2">Neem 2 sec op en speel af als effect!</p>
                    <div class="flex space-x-2">
                        <button id="mic-rec-btn" onclick="toggleMicRecord()" class="bg-red-600 hover:bg-red-500 text-white text-xs font-bold py-2 px-3 rounded-lg btn flex-1">
                            🎙️ Opnemen
                        </button>
                        <button id="mic-play-btn" onclick="playMicSample()" disabled class="bg-slate-800 text-slate-500 text-xs font-bold py-2 px-3 rounded-lg btn flex-1 disabled:opacity-50">
                            🔊 Afspelen
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        const gameState = {
            energy: 30,
            score: 0,
            level: 1, // 1: Beginner (3), 2: Gemiddeld (6), 3: Expert (9)
            currentScreen: 'start',
            quiz: {
                currentGenre: null,
                answered: false,
                isPlaying: false,
                streak: 0
            },
            jumper: {
                isPlaying: false,
                score: 0,
                stage: 1,
                targetScore: 50,
                animId: null
            },
            soundlab: {
                isPlaying: false,
                bpm: 120,
                currentStep: 0,
                grid: {
                    kick:  [1, 0, 0, 0, 1, 0, 0, 0],
                    snare: [0, 0, 1, 0, 0, 0, 1, 0],
                    hihat: [1, 1, 1, 1, 1, 1, 1, 1],
                    bass:  [1, 0, 0, 1, 0, 0, 1, 0]
                }
            }
        };

        const JUMP_COST = 3;

        // --- Audio Synths (Tone.js) ---
        let audioInitialized = false;
        let kick, snare, hihat, bassSynth, polySynth, currentQuizLoop = null;

        function initAudio() {
            if (audioInitialized) return;
            Tone.start();
            audioInitialized = true;

            kick = new Tone.MembraneSynth().toDestination();
            snare = new Tone.NoiseSynth({ envelope: { attack: 0.001, decay: 0.2, sustain: 0 } }).toDestination();
            hihat = new Tone.MetalSynth({ envelope: { attack: 0.001, decay: 0.08, release: 0.01 }, harmonicity: 5.1, resonance: 4000 }).toDestination();
            bassSynth = new Tone.MonoSynth({ oscillator: { type: "triangle" }, envelope: { attack: 0.05, decay: 0.2, sustain: 0.4, release: 0.4 } }).toDestination();
            polySynth = new Tone.PolySynth(Tone.Synth).toDestination();
        }

        // --- Genres Database ---
        const genreDatabase = [
            // Level 1
            { id: 'rock', name: 'Rock', minLevel: 1, desc: 'Kenmerkt zich door stevige elektrische gitaren en een sterke drumsilhouet.', icon: '🎸' },
            { id: 'classical', name: 'Klassiek', minLevel: 1, desc: 'Gebruikt akoestische instrumenten zoals viool, piano en symfonische harmoniën.', icon: '🎻' },
            { id: 'synthwave', name: 'Synthwave', minLevel: 1, desc: 'Retro 80s elektronische muziek met melodieuze synthesizers.', icon: '🎹' },
            
            // Level 2
            { id: 'jazz', name: 'Jazz', minLevel: 2, desc: 'Bekend om ingewikkelde akkoorden, saxofoon en swing ritmes.', icon: '🎷' },
            { id: 'reggae', name: 'Reggae', minLevel: 2, desc: 'Oorsprong in Jamaica met het typische off-beat ritme van de gitaar.', icon: '🌴' },
            { id: 'hiphop', name: 'Hip-Hop', minLevel: 2, desc: 'Dikke baslijnen, strakke beats en een ritmische groove.', icon: '🎤' },

            // Level 3
            { id: 'techno', name: 'Techno', minLevel: 3, desc: '4-to-the-floor stampende elektronische drumbeats uit de clubscene.', icon: '⚡' },
            { id: 'chiptune', name: '8-Bit Chiptune', minLevel: 3, desc: 'Retro arcade spellen muziek met piepende vierkante geluidsgolven.', icon: '👾' },
            { id: 'funk', name: 'Funk & Disco', minLevel: 3, desc: 'Dansbare baslijnen, slap bass en vrolijke akkoorden.', icon: '🪩' }
        ];

        function updateHUD() {
            document.getElementById('energy-display').innerText = gameState.energy;
            document.getElementById('score-display').innerText = gameState.score;
            
            const lvlNames = { 1: '1 (Beginner)', 2: '2 (Gemiddeld)', 3: '3 (Expert)' };
            document.getElementById('level-display').innerText = lvlNames[gameState.level];
        }

        function switchScreen(screenId) {
            initAudio();
            stopQuizSound();

            document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
            document.getElementById(`screen-${screenId}`).classList.add('active');
            gameState.currentScreen = screenId;

            if (screenId === 'quiz') {
                generateQuestion();
            } else if (screenId === 'jumper') {
                checkJumperPlayability();
            } else if (screenId === 'timeline') {
                initTimeline();
            } else if (screenId === 'soundlab') {
                initSoundlabUI();
            }
        }

        function setDifficulty(lvl) {
            gameState.level = lvl;
            for (let i = 1; i <= 3; i++) {
                // Knoppen op startscherm
                const btn = document.getElementById(`diff-btn-${i}`);
                if (btn) {
                    btn.className = (i === lvl) 
                        ? "bg-emerald-600 text-white font-bold py-2 px-2 rounded-xl text-xs btn border-2 border-emerald-300"
                        : "bg-slate-800 text-slate-400 font-bold py-2 px-2 rounded-xl text-xs btn border-2 border-transparent";
                }
                
                // Knoppen in de Hub
                const hubBtn = document.getElementById(`hub-diff-btn-${i}`);
                if (hubBtn) {
                    hubBtn.className = (i === lvl) 
                        ? "bg-emerald-600 text-white font-bold py-1.5 px-3 rounded-lg text-xs btn border border-emerald-400"
                        : "bg-slate-800 text-slate-400 font-bold py-1.5 px-3 rounded-lg text-xs btn border border-slate-700";
                }
            }
            updateHUD();
            
            // Genereer nieuwe vraag die past bij het nieuwe niveau als je de quiz speelt
            if (gameState.currentScreen === 'quiz') {
                generateQuestion();
            }
        }

        function generateQuestion() {
            stopQuizSound();
            gameState.quiz.answered = false;
            document.getElementById('next-q-btn').classList.add('hidden');
            document.getElementById('feedback-message').innerText = '';
            document.getElementById('learning-info').classList.add('hidden');

            const availableGenres = genreDatabase.filter(g => g.minLevel <= gameState.level);
            const target = availableGenres[Math.floor(Math.random() * availableGenres.length)];
            gameState.quiz.currentGenre = target;

            // Generate 4 distinct options including correct one
            let options = [target];
            while (options.length < Math.min(4, availableGenres.length)) {
                let randomG = availableGenres[Math.floor(Math.random() * availableGenres.length)];
                if (!options.includes(randomG)) options.push(randomG);
            }
            // Shuffle
            options.sort(() => Math.random() - 0.5);

            const optContainer = document.getElementById('options-container');
            optContainer.innerHTML = '';
            options.forEach(opt => {
                const btn = document.createElement('button');
                btn.className = 'bg-slate-700/80 hover:bg-indigo-600 text-white font-bold py-3 px-4 rounded-xl text-xs md:text-sm btn border border-slate-600 text-left flex items-center space-x-2';
                btn.innerHTML = `<span>${opt.icon}</span> <span>${opt.name}</span>`;
                btn.onclick = () => checkQuizAnswer(opt, btn);
                optContainer.appendChild(btn);
            });
        }

        let quizTimeoutId = null;

        function playQuizSound() {
            if (gameState.quiz.isPlaying) return; // Voorkom dat fragmenten door elkaar heen spelen
            
            initAudio();
            stopQuizSound();

            const genre = gameState.quiz.currentGenre.id;
            const now = Tone.now();
            drawVisualizer();

            document.getElementById('play-icon').innerText = '🔊';
            gameState.quiz.isPlaying = true;

            if (genre === 'rock') {
                // Rock beat met Power Chords (4 seconden)
                for(let i=0; i<8; i++) {
                    kick.triggerAttackRelease("C1", "8n", now + i*0.5);
                    if (i % 2 === 1) snare.triggerAttackRelease("8n", now + i*0.5);
                }
                polySynth.triggerAttackRelease(["E3", "B3", "E4"], "2n", now);
                polySynth.triggerAttackRelease(["E3", "B3", "E4"], "4n", now + 1);
                polySynth.triggerAttackRelease(["G3", "D4", "G4"], "4n", now + 1.5);
                polySynth.triggerAttackRelease(["A3", "E4", "A4"], "2n", now + 2);
                polySynth.triggerAttackRelease(["E3", "B3", "E4"], "1n", now + 3);
                
            } else if (genre === 'classical') {
                // Mozart-stijl Alberti bas en melodie
                const notes = ["C4", "G4", "E4", "G4", "C4", "G4", "E4", "G4", "D4", "G4", "F4", "G4", "C4", "G4", "E4", "G4"];
                notes.forEach((note, i) => {
                    polySynth.triggerAttackRelease(note, "16n", now + i*0.25);
                });
                polySynth.triggerAttackRelease(["C5", "E5"], "2n", now);
                polySynth.triggerAttackRelease(["B4", "D5"], "2n", now + 2);
                
            } else if (genre === 'synthwave') {
                // Drijvende retro bassline en warme synths
                for(let i=0; i<16; i++) {
                    bassSynth.triggerAttackRelease("C2", "16n", now + i*0.25);
                }
                polySynth.triggerAttackRelease(["C4", "Eb4", "G4"], "2n", now);
                polySynth.triggerAttackRelease(["F4", "Ab4", "C5"], "2n", now + 2);
                
            } else if (genre === 'jazz') {
                // Swing ritme met Jazz Akkoorden (Dm9 -> G13)
                for(let i=0; i<4; i++) {
                    hihat.triggerAttackRelease("8n", now + i);
                    hihat.triggerAttackRelease("16n", now + i + 0.66);
                    hihat.triggerAttackRelease("8n", now + i + 0.83);
                }
                polySynth.triggerAttackRelease(["D3", "F3", "A3", "C4", "E4"], "2n", now); // Dm9
                polySynth.triggerAttackRelease(["G2", "B2", "F3", "A3", "E4"], "2n", now + 2); // G13
                bassSynth.triggerAttackRelease("D2", "4n", now);
                bassSynth.triggerAttackRelease("F2", "4n", now + 0.5);
                bassSynth.triggerAttackRelease("G2", "4n", now + 1);
                bassSynth.triggerAttackRelease("B2", "4n", now + 1.5);
                
            } else if (genre === 'reggae') {
                // Offbeat skank akkoorden & bas
                for(let i=0; i<4; i++) {
                    polySynth.triggerAttackRelease(["G3", "C4", "E4"], "16n", now + i + 0.5);
                    polySynth.triggerAttackRelease(["G3", "C4", "E4"], "16n", now + i + 0.75);
                    bassSynth.triggerAttackRelease("C2", "8n", now + i);
                    if (i%2 === 0) bassSynth.triggerAttackRelease("G2", "8n", now + i + 0.25);
                }
                
            } else if (genre === 'hiphop') {
                // Boom-bap ritme met diepe sub-bas
                for(let i=0; i<4; i++) {
                    kick.triggerAttackRelease("C1", "8n", now + i);
                    if (i%2 === 1) snare.triggerAttackRelease("8n", now + i);
                    kick.triggerAttackRelease("C1", "8n", now + i + 0.75);
                    for(let j=0; j<4; j++) {
                        hihat.triggerAttackRelease("16n", now + i + j*0.25);
                    }
                }
                bassSynth.triggerAttackRelease("F1", "1n", now);
                bassSynth.triggerAttackRelease("Eb1", "1n", now + 2);
                
            } else if (genre === 'techno') {
                // 4-on-the-floor kick, offbeat hat en acid line
                for(let i=0; i<16; i++) {
                    if (i%4 === 0) kick.triggerAttackRelease("C1", "8n", now + i*0.25);
                    if (i%4 === 2) hihat.triggerAttackRelease("16n", now + i*0.25);
                    const notes = ["C2", "C3", "C2", "Eb2"];
                    bassSynth.triggerAttackRelease(notes[i%4], "16n", now + i*0.25);
                }
                
            } else if (genre === 'chiptune') {
                // Retro video-game stijl (snelle arpeggio)
                const chipNotes = ["C5", "E5", "G5", "C6", "G5", "E5"];
                for(let i=0; i<24; i++) {
                    polySynth.triggerAttackRelease(chipNotes[i%6], "32n", now + i*0.166);
                }
                bassSynth.triggerAttackRelease("C3", "4n", now);
                bassSynth.triggerAttackRelease("F3", "4n", now + 1);
                bassSynth.triggerAttackRelease("G3", "4n", now + 2);
                bassSynth.triggerAttackRelease("C3", "4n", now + 3);
                
            } else if (genre === 'funk') {
                // Slap bass en hoog tempo syncopatie
                for(let i=0; i<4; i++) {
                    bassSynth.triggerAttackRelease("G2", "16n", now + i);
                    bassSynth.triggerAttackRelease("G2", "16n", now + i + 0.25);
                    bassSynth.triggerAttackRelease("F2", "16n", now + i + 0.75);
                    if (i%2===0) {
                        polySynth.triggerAttackRelease(["G4", "B4", "D5", "F5"], "16n", now + i + 0.5);
                    } else {
                        polySynth.triggerAttackRelease(["C5", "E5", "G5", "Bb5"], "16n", now + i + 0.5);
                    }
                }
            }

            // Fragment duurt nu 4 volledige seconden in plaats van < 2 sec.
            quizTimeoutId = setTimeout(() => {
                stopQuizSound();
            }, 4000);
        }

        function stopQuizSound() {
            gameState.quiz.isPlaying = false;
            document.getElementById('play-icon').innerText = '▶️';
            
            // Annuleer de timeout zodat hij niet te vroeg afslaat bij meerdere kliks
            if (quizTimeoutId) {
                clearTimeout(quizTimeoutId);
                quizTimeoutId = null;
            }
        }

        function checkQuizAnswer(selected, btnElement) {
            if (gameState.quiz.answered) return;
            gameState.quiz.answered = true;

            const isCorrect = selected.id === gameState.quiz.currentGenre.id;
            const fb = document.getElementById('feedback-message');
            const info = document.getElementById('learning-info');

            if (isCorrect) {
                fb.innerText = '🎉 Goed! +10⚡ Energie verdiend!';
                fb.className = 'text-center text-sm md:text-base font-bold text-emerald-400';
                gameState.energy += 10;
                gameState.score += 15;
                gameState.quiz.streak++;
                polySynth.triggerAttackRelease(["C5", "E5", "G5"], "8n");
            } else {
                fb.innerText = `❌ Helaas! Het juiste antwoord was ${gameState.quiz.currentGenre.name}.`;
                fb.className = 'text-center text-sm md:text-base font-bold text-rose-400';
                gameState.quiz.streak = 0;
                bassSynth.triggerAttackRelease("C2", "4n");
            }

            document.getElementById('quiz-streak').innerText = `${gameState.quiz.streak} 🔥`;
            info.innerText = `${gameState.quiz.currentGenre.name}: ${gameState.quiz.currentGenre.desc}`;
            info.classList.remove('hidden');
            document.getElementById('next-q-btn').classList.remove('hidden');

            updateHUD();
        }

        function drawVisualizer() {
            const canvas = document.getElementById('visualizer-canvas');
            const ctx = canvas.getContext('2d');
            let frame = 0;

            function animate() {
                if (!gameState.quiz.isPlaying) {
                    ctx.clearRect(0, 0, canvas.width, canvas.height);
                    return;
                }
                ctx.fillStyle = 'rgba(15, 23, 42, 0.3)';
                ctx.fillRect(0, 0, canvas.width, canvas.height);

                ctx.fillStyle = '#38bdf8';
                for (let i = 0; i < 20; i++) {
                    let h = Math.sin(frame * 0.2 + i) * 20 + 25;
                    ctx.fillRect(i * 15 + 5, canvas.height - h, 10, h);
                }
                frame++;
                requestAnimationFrame(animate);
            }
            animate();
        }

        const jumperCanvas = document.getElementById('minigame-canvas');
        const jCtx = jumperCanvas.getContext('2d');

        let jumperSpeed = 3.8;
        let jumperPlatforms = [];
        let player = { x: 60, y: 150, w: 24, h: 24, vy: 0, gravity: 0.55, onGround: false };

        function setJumperStage(stageNum) {
            gameState.jumper.stage = stageNum;
            const targets = { 1: 50, 2: 100, 3: 150 };
            gameState.jumper.targetScore = targets[stageNum];

            for (let i = 1; i <= 3; i++) {
                const btn = document.getElementById(`jumper-stage-${i}`);
                btn.className = (i === stageNum) 
                    ? "bg-indigo-600 text-white text-xs px-3 py-1.5 rounded-lg border border-indigo-400 font-bold btn"
                    : "bg-slate-800 text-slate-400 text-xs px-3 py-1.5 rounded-lg border border-slate-700 font-bold btn";
            }
            checkJumperPlayability();
        }

        function checkJumperPlayability() {
            const btn = document.getElementById('btn-play-jumper');
            const txt = document.getElementById('overlay-text');

            if (gameState.energy >= JUMP_COST) {
                btn.disabled = false;
                txt.innerText = `Level ${gameState.jumper.stage} Klaar voor start! Doel: ${gameState.jumper.targetScore}m (⚡ ${gameState.energy} Over)`;
                txt.className = "text-emerald-400 text-sm md:text-lg font-bold mb-4 text-center";
            } else {
                btn.disabled = true;
                txt.innerText = `Te weinig energie! (Kost ${JUMP_COST}⚡). Speel de Quiz voor ⚡.`;
                txt.className = "text-rose-400 text-sm md:text-base font-bold mb-4 text-center";
            }
        }

        function jumperJump() {
            if (!gameState.jumper.isPlaying) return;
            
            if (gameState.energy >= JUMP_COST && player.onGround) {
                player.vy = -11;
                player.onGround = false;
                gameState.energy -= JUMP_COST;
                updateHUD();
                polySynth.triggerAttackRelease("G4", "16n");

                if (gameState.energy < JUMP_COST) {
                    stopJumperGame(false, false, true);
                }
            }
        }

        function startJumperGame() {
            if (gameState.energy < JUMP_COST) return;

            gameState.jumper.isPlaying = true;
            gameState.jumper.score = 0;
            document.getElementById('game-score').innerText = 0;
            document.getElementById('game-stage-num').innerText = gameState.jumper.stage;
            document.getElementById('game-target').innerText = gameState.jumper.targetScore;

            const speeds = { 1: 3.5, 2: 4.5, 3: 5.5 };
            jumperSpeed = speeds[gameState.jumper.stage];
            player.x = 60;
            player.y = 150;
            player.vy = 0;

            jumperPlatforms = [{ x: 0, y: 260, w: 700, h: 100 }];

            document.getElementById('game-overlay').style.display = 'none';
            document.getElementById('game-ui').style.display = 'flex';

            jumperLoop();
        }

        function stopJumperGame(crashed = false, cleared = false, outOfEnergy = false) {
            gameState.jumper.isPlaying = false;
            if (gameState.jumper.animId) cancelAnimationFrame(gameState.jumper.animId);

            document.getElementById('game-overlay').style.display = 'flex';
            document.getElementById('game-ui').style.display = 'none';

            const txt = document.getElementById('overlay-text');
            if (cleared) {
                txt.innerText = `🎉 GEFELICITEERD! Level ${gameState.jumper.stage} Voltooid! (+25 Score)`;
                txt.className = "text-emerald-400 text-base md:text-lg font-bold mb-4 text-center";
                gameState.score += 25;
                updateHUD();
            } else if (outOfEnergy) {
                txt.innerText = "⚡ Energie is op! Speel de quiz om energie op te laden.";
                txt.className = "text-amber-400 text-base md:text-lg font-bold mb-4 text-center";
            } else if (crashed) {
                txt.innerText = `Gevallen! Behaalde afstand: ${gameState.jumper.score}m`;
                txt.className = "text-rose-400 text-base md:text-lg font-bold mb-4 text-center";
            }
            checkJumperPlayability();
        }

        function jumperLoop() {
            if (!gameState.jumper.isPlaying) return;

            player.vy += player.gravity;
            player.y += player.vy;
            player.onGround = false;

            for (let p of jumperPlatforms) {
                if (player.x + player.w > p.x + 4 &&
                    player.x < p.x + p.w - 4 &&
                    player.y + player.h >= p.y &&
                    player.y + player.h - player.vy <= p.y + 12) {
                    
                    player.y = p.y - player.h;
                    player.vy = 0;
                    player.onGround = true;
                }
            }

            if (player.y > jumperCanvas.height) {
                stopJumperGame(true);
                return;
            }

            let last = jumperPlatforms[jumperPlatforms.length - 1];
            if (last.x + last.w < jumperCanvas.width + 120) {
                let gapBase = { 1: 65, 2: 85, 3: 110 };
                let gap = Math.random() * 40 + gapBase[gameState.jumper.stage];
                let newY = Math.max(180, Math.min(280, last.y + (Math.random() * 60 - 30)));
                jumperPlatforms.push({
                    x: last.x + last.w + gap,
                    y: newY,
                    w: Math.random() * 120 + 90,
                    h: 100
                });
            }

            for (let i = jumperPlatforms.length - 1; i >= 0; i--) {
                jumperPlatforms[i].x -= jumperSpeed;
                if (jumperPlatforms[i].x + jumperPlatforms[i].w < -50) {
                    jumperPlatforms.splice(i, 1);
                    gameState.jumper.score += 10;
                    gameState.score += 5;
                    document.getElementById('game-score').innerText = gameState.jumper.score;
                    updateHUD();

                    if (gameState.jumper.score >= gameState.jumper.targetScore) {
                        stopJumperGame(false, true);
                        return;
                    }
                }
            }

            // Render
            jCtx.clearRect(0, 0, jumperCanvas.width, jumperCanvas.height);
            
            jumperPlatforms.forEach(p => {
                jCtx.fillStyle = '#4f46e5';
                jCtx.fillRect(p.x, p.y, p.w, p.h);
                jCtx.fillStyle = '#818cf8';
                jCtx.fillRect(p.x, p.y, p.w, 6);
            });

            // Player Note
            jCtx.fillStyle = '#f59e0b';
            jCtx.beginPath();
            jCtx.arc(player.x + player.w/2, player.y + player.h/2, 12, 0, Math.PI * 2);
            jCtx.fill();

            gameState.jumper.animId = requestAnimationFrame(jumperLoop);
        }

        const timelineData = [
            { name: 'Klassiek', year: 1750 },
            { name: 'Jazz', year: 1910 },
            { name: 'Rock', year: 1950 },
            { name: 'Techno', year: 1980 }
        ];

        let selectedTimeline = [];

        function initTimeline() {
            selectedTimeline = [];
            document.getElementById('timeline-feedback').innerText = '';
            renderTimelineUI();
        }

        function renderTimelineUI() {
            const slots = document.getElementById('timeline-slots');
            const options = document.getElementById('timeline-options');
            slots.innerHTML = '';
            options.innerHTML = '';

            for (let i = 0; i < 4; i++) {
                const item = selectedTimeline[i];
                const slot = document.createElement('div');
                slot.className = 'h-14 border-2 border-dashed border-purple-500/50 rounded-xl flex items-center justify-center font-bold text-xs md:text-sm bg-slate-900/50';
                slot.innerText = item ? item.name : `Plek ${i+1}`;
                slots.appendChild(slot);
            }

            timelineData.forEach(genre => {
                if (!selectedTimeline.includes(genre)) {
                    const btn = document.createElement('button');
                    btn.className = 'bg-purple-900/80 hover:bg-purple-800 text-purple-200 border border-purple-500/40 px-3 py-2 rounded-xl text-xs font-bold btn';
                    btn.innerText = genre.name;
                    btn.onclick = () => {
                        if (selectedTimeline.length < 4) {
                            selectedTimeline.push(genre);
                            renderTimelineUI();
                        }
                    };
                    options.appendChild(btn);
                }
            });
        }

        function resetTimeline() {
            selectedTimeline = [];
            document.getElementById('timeline-feedback').innerText = '';
            renderTimelineUI();
        }

        function checkTimeline() {
            if (selectedTimeline.length < 4) {
                const fb = document.getElementById('timeline-feedback');
                fb.innerText = 'Vul eerst alle 4 de plekken in!';
                fb.className = 'mt-4 text-center font-bold text-amber-400';
                return;
            }

            let isCorrect = true;
            for (let i = 0; i < 3; i++) {
                if (selectedTimeline[i].year > selectedTimeline[i+1].year) {
                    isCorrect = false;
                    break;
                }
            }

            const fb = document.getElementById('timeline-feedback');
            if (isCorrect) {
                fb.innerText = 'Perfect! Volgorde is juist! +20⚡ Bonus Energie!';
                fb.className = 'mt-4 text-center font-bold text-emerald-400';
                gameState.energy += 20;
                gameState.score += 30;
                updateHUD();
            } else {
                fb.innerText = 'Helaas, de volgorde van oud naar nieuw klopt nog niet helemaal.';
                fb.className = 'mt-4 text-center font-bold text-rose-400';
            }
        }

        function initSoundlabUI() {
            const container = document.getElementById('sequencer-rows');
            container.innerHTML = '';

            const instruments = ['kick', 'snare', 'hihat', 'bass'];
            instruments.forEach(inst => {
                const row = document.createElement('div');
                row.className = 'flex items-center space-x-2';
                
                const label = document.createElement('span');
                label.className = 'w-14 text-[11px] font-bold text-slate-400 uppercase';
                label.innerText = inst;
                row.appendChild(label);

                const stepsDiv = document.createElement('div');
                stepsDiv.className = 'flex space-x-1 flex-1 justify-between';

                for (let i = 0; i < 8; i++) {
                    const step = document.createElement('div');
                    const isActive = gameState.soundlab.grid[inst][i] === 1;
                    step.className = `seq-step ${isActive ? 'active' : 'bg-slate-800'}`;
                    step.onclick = () => {
                        gameState.soundlab.grid[inst][i] = isActive ? 0 : 1;
                        initSoundlabUI();
                    };
                    stepsDiv.appendChild(step);
                }
                row.appendChild(stepsDiv);
                container.appendChild(row);
            });
        }

        function updateBpm(val) {
            gameState.soundlab.bpm = val;
            document.getElementById('bpm-val').innerText = val;
            if (gameState.soundlab.isPlaying) Tone.Transport.bpm.value = val;
        }

        function toggleSequencer() {
            initAudio();
            const btn = document.getElementById('seq-play-btn');

            if (gameState.soundlab.isPlaying) {
                Tone.Transport.stop();
                Tone.Transport.cancel(0);
                gameState.soundlab.isPlaying = false;
                btn.innerText = '▶ Play Beat';
            } else {
                Tone.Transport.bpm.value = gameState.soundlab.bpm;
                let step = 0;

                Tone.Transport.scheduleRepeat((time) => {
                    const g = gameState.soundlab.grid;
                    if (g.kick[step]) kick.triggerAttackRelease("C1", "8n", time);
                    if (g.snare[step]) snare.triggerAttackRelease("8n", time);
                    if (g.hihat[step]) hihat.triggerAttackRelease("16n", time);
                    if (g.bass[step]) bassSynth.triggerAttackRelease("C2", "8n", time);

                    step = (step + 1) % 8;
                }, "8n");

                Tone.Transport.start();
                gameState.soundlab.isPlaying = true;
                btn.innerText = '⏸️ Stop Beat';
            }
        }

        function playSFX(type) {
            initAudio();
            if (type === 'clap') snare.triggerAttackRelease("8n");
            if (type === 'laser') polySynth.triggerAttackRelease("C6", "16n");
            if (type === 'coin') polySynth.triggerAttackRelease(["E6", "B6"], "16n");
        }

        let mediaRecorder = null;
        let audioChunks = [];
        let recordedBuffer = null;

        async function toggleMicRecord() {
            const btn = document.getElementById('mic-rec-btn');
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(stream);
                audioChunks = [];

                mediaRecorder.ondataavailable = e => audioChunks.push(e.data);
                mediaRecorder.onstop = async () => {
                    const blob = new Blob(audioChunks, { type: 'audio/ogg; codecs=opus' });
                    const url = URL.createObjectURL(blob);
                    recordedBuffer = new Tone.Player(url).toDestination();
                    document.getElementById('mic-play-btn').disabled = false;
                    document.getElementById('mic-play-btn').className = "bg-emerald-600 hover:bg-emerald-500 text-white text-xs font-bold py-2 px-3 rounded-lg btn flex-1";
                };

                mediaRecorder.start();
                btn.innerText = "⏺️ Opnemen...";
                btn.className = "bg-amber-600 text-white text-xs font-bold py-2 px-3 rounded-lg btn flex-1 animate-pulse";

                setTimeout(() => {
                    if (mediaRecorder && mediaRecorder.state === 'recording') {
                        mediaRecorder.stop();
                        btn.innerText = "🎙️ Opnemen";
                        btn.className = "bg-red-600 hover:bg-red-500 text-white text-xs font-bold py-2 px-3 rounded-lg btn flex-1";
                    }
                }, 2000);

            } catch (err) {
                const msg = document.getElementById('feedback-message');
                if (msg) msg.innerText = "Microfoon toegang geweigerd of niet beschikbaar.";
            }
        }

        function playMicSample() {
            if (recordedBuffer) {
                recordedBuffer.start();
            }
        }

        window.addEventListener('keydown', (e) => {
            if (e.code === 'Space') {
                if (gameState.currentScreen === 'jumper') {
                    jumperJump();
                    e.preventDefault();
                }
            }
        });

        jumperCanvas.addEventListener('mousedown', jumperJump);
        jumperCanvas.addEventListener('touchstart', (e) => {
            e.preventDefault();
            jumperJump();
        }, { passive: false });

        // Initialize HUD
        updateHUD();
    </script>
</body>
</html>
