
<!DOCTYPE html>
<html lang="nl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Muziek Genre Master</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/tone/14.8.49/Tone.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&family=Roboto:wght@400;700&display=swap');

        body {
            font-family: 'Roboto', sans-serif;
            background-color: #1a202c;
            color: white;
            overflow: hidden;
            touch-action: manipulation;
        }

        .pixel-font {
            font-family: 'Press Start 2P', cursive;
        }

        /* Custom Animations */
        @keyframes pulse-energy {
            0% { transform: scale(1); box-shadow: 0 0 10px #fbbf24; }
            50% { transform: scale(1.05); box-shadow: 0 0 20px #fbbf24, 0 0 30px #f59e0b; }
            100% { transform: scale(1); box-shadow: 0 0 10px #fbbf24; }
        }

        .energy-pulse {
            animation: pulse-energy 1.5s infinite;
        }

        @keyframes float {
            0% { transform: translateY(0px); }
            50% { transform: translateY(-10px); }
            100% { transform: translateY(0px); }
        }

        .floating {
            animation: float 3s ease-in-out infinite;
        }

        /* Game Canvas Styling */
        #minigame-canvas {
            display: none;
            background: linear-gradient(to bottom, #4f46e5, #9333ea);
            border-radius: 12px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.5);
            max-width: 100%;
            border: 4px solid #fff;
        }

        /* Custom scrollbar */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #2d3748;
        }
        ::-webkit-scrollbar-thumb {
            background: #4a5568;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #718096;
        }

        .screen {
            display: none;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            width: 100vw;
            position: absolute;
            top: 0;
            left: 0;
            padding: 1rem;
            box-sizing: border-box;
        }

        .screen.active {
            display: flex;
        }

        .btn {
            transition: all 0.2s;
        }
        .btn:hover:not(:disabled) {
            transform: scale(1.05);
            filter: brightness(1.2);
        }
        .btn:active:not(:disabled) {
            transform: scale(0.95);
        }
        .btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        /* Note particles for correct answers */
        .note-particle {
            position: absolute;
            font-size: 24px;
            pointer-events: none;
            animation: floatUp 1.5s ease-out forwards;
        }

        @keyframes floatUp {
            0% { opacity: 1; transform: translateY(0) scale(1) rotate(0deg); }
            100% { opacity: 0; transform: translateY(-100px) scale(1.5) rotate(45deg); }
        }
    </style>
</head>
<body>

    <!-- Top HUD (Heads Up Display) -->
    <div id="hud" class="fixed top-0 left-0 w-full p-4 flex justify-between items-center z-50 bg-gray-900 bg-opacity-80 backdrop-blur-sm border-b border-gray-700 hidden">
        <div class="flex items-center space-x-2">
            <span class="text-3xl">⚡</span>
            <span class="pixel-font text-yellow-400 text-xl" id="energy-display">0</span>
        </div>
        <button id="nav-btn" class="bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-2 px-4 rounded-full shadow-lg btn">
            Naar Minigame
        </button>
    </div>

    <!-- Start Screen -->
    <div id="screen-start" class="screen active bg-gradient-to-br from-indigo-900 to-purple-900">
        <h1 class="pixel-font text-4xl md:text-6xl text-center text-transparent bg-clip-text bg-gradient-to-r from-cyan-400 to-purple-400 mb-8 floating text-white">Muziek Genre Master</h1>
        <p class="text-lg md:text-xl text-gray-300 mb-8 text-center max-w-2xl">
            Luister naar de geluiden, raad het genre, en verdien energie ⚡ om de minigame te spelen!
        </p>
        <button id="btn-start" class="bg-gradient-to-r from-green-400 to-blue-500 text-white font-bold py-4 px-8 rounded-full text-2xl shadow-xl btn pulse-energy">
            Start het Spel!
        </button>
        <p class="text-sm text-gray-400 mt-4">(Zet je geluid aan!)</p>
    </div>

    <!-- Quiz Screen -->
    <div id="screen-quiz" class="screen bg-gradient-to-br from-gray-900 to-blue-900">
        <div class="w-full max-w-3xl bg-gray-800 rounded-2xl p-6 md:p-8 shadow-2xl mt-16">
            
            <div class="flex justify-between items-center mb-6">
                <h2 class="text-2xl font-bold text-cyan-300" id="quiz-title">Raad het Genre!</h2>
                <div class="text-gray-400 font-mono">Vraag: <span id="question-count">1/10</span></div>
            </div>

            <div class="flex flex-col items-center mb-8" id="audio-question-container">
                <div id="play-sound-container" class="w-32 h-32 rounded-full bg-gradient-to-tr from-cyan-500 to-blue-500 flex items-center justify-center cursor-pointer shadow-lg hover:shadow-cyan-500/50 transition-all transform hover:scale-105 mb-4 group">
                    <span id="play-icon" class="text-5xl group-hover:text-white transition-colors">▶️</span>
                </div>
                <p class="text-gray-300" id="listen-text">Klik om te luisteren</p>
            </div>

            <div class="flex flex-col items-center mb-8 hidden" id="text-question-container">
                <p class="text-2xl text-white text-center font-bold" id="question-text"></p>
            </div>

            <div id="options-container" class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <!-- Options will be generated here -->
            </div>

            <div id="feedback-message" class="mt-6 text-center text-xl font-bold h-8 hidden opacity-0 transition-opacity duration-300">
                <!-- Feedback text -->
            </div>
            
            <div id="learning-info" class="mt-4 p-4 bg-indigo-900 border border-indigo-500 rounded-xl text-indigo-100 hidden opacity-0 transition-opacity duration-300">
                <!-- Educational info -->
            </div>
        </div>
    </div>

    <!-- Minigame Screen -->
    <div id="screen-minigame" class="screen bg-gray-900">
        <div class="mt-16 w-full flex flex-col items-center">
            <h2 class="pixel-font text-2xl text-yellow-400 mb-4">Muziek Parkour</h2>
            
            <div class="bg-gray-800 p-4 rounded-xl border border-gray-700 w-full max-w-2xl mb-4 flex justify-between items-center">
                <div>
                    <span class="text-gray-400">Kosten:</span> 
                    <span class="text-yellow-400 font-bold ml-2">⚡ 30 Energie</span>
                </div>
                <button id="btn-play-minigame" class="bg-green-500 hover:bg-green-400 text-white font-bold py-2 px-6 rounded-lg shadow btn disabled:opacity-50 disabled:cursor-not-allowed">
                    Speel!
                </button>
            </div>

            <div id="game-container" class="relative w-full max-w-2xl">
                <!-- Overlay before playing -->
                <div id="game-overlay" class="absolute inset-0 bg-black bg-opacity-75 flex flex-col items-center justify-center rounded-xl z-10">
                    <p class="text-white text-xl mb-4 text-center px-4" id="overlay-text">Verzamel 30 energie in de quiz om te spelen!</p>
                </div>
                
                <canvas id="minigame-canvas" width="600" height="400" class="mx-auto block"></canvas>
                
                <!-- Game UI Overlay -->
                <div id="game-ui" class="absolute top-2 right-2 text-white font-mono text-xl hidden z-20">
                    Score: <span id="game-score">0</span>
                </div>
            </div>
            
            <div class="mt-4 text-gray-400 text-center max-w-md hidden" id="game-instructions">
                Tik of klik op het canvas om te springen over de gaten.<br>
                Ren over de platforms en pak de groene noten!
            </div>
        </div>
    </div>

    <script>
        // --- State Management ---
        const gameState = {
            energy: 0,
            currentScreen: 'start', // 'start', 'quiz', 'minigame'
            quiz: {
                currentQuestionIndex: 0,
                questions: [],
                isPlaying: false,
                answered: false
            },
            minigame: {
                cost: 30,
                isPlaying: false,
                score: 0,
                animationId: null
            }
        };

        // --- DOM Elements ---
        const screens = {
            start: document.getElementById('screen-start'),
            quiz: document.getElementById('screen-quiz'),
            minigame: document.getElementById('screen-minigame')
        };
        
        const hud = document.getElementById('hud');
        const energyDisplay = document.getElementById('energy-display');
        const navBtn = document.getElementById('nav-btn');
        const btnStart = document.getElementById('btn-start');

        // Quiz elements
        const quizTitle = document.getElementById('quiz-title');
        const audioQuestionContainer = document.getElementById('audio-question-container');
        const textQuestionContainer = document.getElementById('text-question-container');
        const questionText = document.getElementById('question-text');
        const playSoundBtn = document.getElementById('play-sound-container');
        const playIcon = document.getElementById('play-icon');
        const optionsContainer = document.getElementById('options-container');
        const feedbackMessage = document.getElementById('feedback-message');
        const learningInfo = document.getElementById('learning-info');
        const questionCount = document.getElementById('question-count');

        // Minigame elements
        const btnPlayMinigame = document.getElementById('btn-play-minigame');
        const canvas = document.getElementById('minigame-canvas');
        const ctx = canvas.getContext('2d');
        const gameOverlay = document.getElementById('game-overlay');
        const overlayText = document.getElementById('overlay-text');
        const gameUi = document.getElementById('game-ui');
        const gameScoreDisplay = document.getElementById('game-score');
        const gameInstructions = document.getElementById('game-instructions');

        // --- Tone.js Synthesizers and Sounds ---
        let isToneInitialized = false;
        
        // Polyphonic synth for chords
        const polySynth = new Tone.PolySynth(Tone.Synth).toDestination();
        polySynth.volume.value = -10;
        
        // Monophonic synth for bass
        const bassSynth = new Tone.FMSynth({
            harmonicity: 1,
            modulationIndex: 3.5,
            oscillator: { type: "square" },
            envelope: { attack: 0.01, decay: 0.2, sustain: 0.2, release: 0.1 }
        }).toDestination();
        bassSynth.volume.value = -5;

        // Drum machine basic (using NoiseSynth for snare/hihat, MembraneSynth for kick)
        const kick = new Tone.MembraneSynth().toDestination();
        kick.volume.value = -5;
        
        const snare = new Tone.NoiseSynth({
            noise: { type: 'white' },
            envelope: { attack: 0.005, decay: 0.1, sustain: 0, release: 0.1 }
        }).toDestination();
        snare.volume.value = -10;

        const hihat = new Tone.MetalSynth({
            frequency: 200,
            envelope: { attack: 0.001, decay: 0.1, release: 0.01 },
            harmonicity: 5.1,
            modulationIndex: 32,
            resonance: 4000,
            octaves: 1.5
        }).toDestination();
        hihat.volume.value = -15;

        // --- Educational Data & Generators ---
        
        const audioGenerators = {
            'Techno': () => {
                Tone.Transport.bpm.value = 135;
                const loop = new Tone.Loop(time => {
                    kick.triggerAttackRelease("C1", "8n", time);
                    if (Math.random() > 0.5) hihat.triggerAttackRelease("16n", time + Tone.Time("16n").toSeconds());
                    if (Math.random() > 0.8) snare.triggerAttackRelease("16n", time + Tone.Time("8n").toSeconds());
                }, "4n");
                const bassLoop = new Tone.Loop(time => {
                    if (Math.random() > 0.3) bassSynth.triggerAttackRelease("C2", "16n", time + Tone.Time("16n").toSeconds());
                }, "8n");
                return [loop, bassLoop];
            },
            'Klassiek': () => {
                Tone.Transport.bpm.value = 90;
                const seq = new Tone.Sequence((time, note) => {
                    polySynth.triggerAttackRelease(note, "8n", time);
                }, [["C4", "E4", "G4"], null, ["G3", "B3", "D4"], null, ["A3", "C4", "E4"], ["F3", "A3", "C4"]], "2n");
                return [seq];
            },
            'Rock': () => {
                Tone.Transport.bpm.value = 110;
                const drumLoop = new Tone.Loop(time => {
                    kick.triggerAttackRelease("C1", "8n", time);
                    snare.triggerAttackRelease("8n", time + Tone.Time("4n").toSeconds());
                    hihat.triggerAttackRelease("16n", time);
                    hihat.triggerAttackRelease("16n", time + Tone.Time("8n").toSeconds());
                }, "2n");
                const bassLoop = new Tone.Sequence((time, note) => {
                    bassSynth.triggerAttackRelease(note, "8n", time);
                }, ["E2", "E2", "E2", "E2", "A2", "A2", "G2", "E2"], "8n");
                return [drumLoop, bassLoop];
            },
            'Jazz': () => {
                Tone.Transport.bpm.value = 120;
                const swingPattern = new Tone.Sequence((time, note) => {
                    hihat.triggerAttackRelease("32n", time);
                    if(note === "swing") hihat.triggerAttackRelease("32n", time + Tone.Time("8t").toSeconds() * 2);
                }, ["hit", null, "swing", null], "8n");
                const bassLine = new Tone.Sequence((time, note) => {
                    bassSynth.triggerAttackRelease(note, "4n", time);
                }, ["C2", "E2", "G2", "Bb2", "F2", "A2", "C3", "Eb3"], "4n");
                return [swingPattern, bassLine];
            },
            'Drums Solo': () => {
                Tone.Transport.bpm.value = 100;
                const loop = new Tone.Loop(time => {
                    kick.triggerAttackRelease("C1", "4n", time);
                    snare.triggerAttackRelease("8n", time + Tone.Time("4n").toSeconds());
                    hihat.triggerAttackRelease("8n", time);
                    hihat.triggerAttackRelease("8n", time + Tone.Time("8n").toSeconds());
                }, "2n");
                return [loop];
            },
            'Piano Solo': () => {
                Tone.Transport.bpm.value = 90;
                const seq = new Tone.Sequence((time, note) => {
                    polySynth.triggerAttackRelease(note, "4n", time);
                }, ["C4", "G4", "E4", "C5"], "4n");
                return [seq];
            },
            'Basgitaar Solo': () => {
                Tone.Transport.bpm.value = 110;
                const seq = new Tone.Sequence((time, note) => {
                    bassSynth.triggerAttackRelease(note, "8n", time);
                }, ["C2", null, "C2", "D2", "Eb2", null, "F2", "G2"], "8n");
                return [seq];
            }
        };

        const allQuestionsData = [
            { type: 'audio', category: 'Genre', correct: 'Techno', audioKey: 'Techno', options: ['Techno', 'Rock', 'Klassiek', 'Reggae'], info: '💡 Techno ontstond in de jaren 80 in Detroit. Het gebruikt elektronische instrumenten en heeft vaak een strakke 4/4 "four-on-the-floor" beat.' },
            { type: 'audio', category: 'Genre', correct: 'Klassiek', audioKey: 'Klassiek', options: ['Klassiek', 'Jazz', 'Rock', 'Techno'], info: '💡 Klassieke muziek wordt vaak gespeeld door een orkest met akoestische instrumenten zoals violen, cello\'s en piano\'s.' },
            { type: 'audio', category: 'Genre', correct: 'Rock', audioKey: 'Rock', options: ['Rock', 'Reggae', 'Jazz', 'Techno'], info: '💡 Rockmuziek draait meestal om de elektrische gitaar, basgitaar en een krachtig drumstel.' },
            { type: 'audio', category: 'Genre', correct: 'Jazz', audioKey: 'Jazz', options: ['Jazz', 'Klassiek', 'Reggae', 'Rock'], info: '💡 Jazz staat bekend om improvisatie (ter plekke verzinnen) en "swingende" ritmes. Het ontstond in New Orleans.' },
            { type: 'audio', category: 'Instrument', correct: 'Drums', audioKey: 'Drums Solo', options: ['Drums', 'Piano', 'Basgitaar', 'Viool'], info: '💡 Het drumstel vormt de ritmische basis van veel muziek. Het bestaat uit trommels (zoals de kick en snare) en bekkens.' },
            { type: 'audio', category: 'Instrument', correct: 'Piano', audioKey: 'Piano Solo', options: ['Piano', 'Basgitaar', 'Synthesizer', 'Gitaar'], info: '💡 De piano is een bijzonder instrument: binnenin slaan hamertjes tegen snaren als je een toets indrukt!' },
            { type: 'audio', category: 'Instrument', correct: 'Basgitaar', audioKey: 'Basgitaar Solo', options: ['Basgitaar', 'Elektrische Gitaar', 'Piano', 'Drums'], info: '💡 De basgitaar heeft meestal 4 dikke snaren en zorgt voor de lage tonen. Het verbindt het ritme met de melodie.' },
            { type: 'text', category: 'Muziektheorie', text: 'Wat geeft een metronoom aan in de muziek?', correct: 'Het tempo (snelheid)', options: ['Het tempo (snelheid)', 'De toonhoogte', 'Het volume', 'De songtekst'], info: '💡 Een metronoom tikt in een vast tempo (BPM: Beats Per Minute) om muzikanten te helpen in de maat te blijven spelen.' },
            { type: 'text', category: 'Muziektheorie', text: 'Wat is de functie van een refrein in een liedje?', correct: 'Het is het deel dat vaak herhaald wordt', options: ['Het is het deel dat vaak herhaald wordt', 'Het is het begin van het liedje', 'Het is een instrumentale solo', 'Het is de stilte in een lied'], info: '💡 Een refrein (chorus) is makkelijk mee te zingen, komt meerdere keren terug en bevat vaak de hoofdboodschap van het nummer.' },
            { type: 'text', category: 'Muziektheorie', text: 'Wat betekent "A capella" zingen?', correct: 'Zingen zonder muziekinstrumenten', options: ['Zingen zonder muziekinstrumenten', 'Heel hard zingen', 'Zingen met een orkest', 'Fluisterend zingen'], info: '💡 A capella is Italiaans voor "als in de kapel". Omdat instrumenten vroeger niet altijd in de kerk mochten, werd er alleen gezongen.' }
        ];

        function generateQuiz() {
            // Shuffle and pick 5 questions for this round
            let shuffled = [...allQuestionsData].sort(() => 0.5 - Math.random());
            gameState.quiz.questions = shuffled.slice(0, 5).map(q => {
                let shuffledOptions = [...q.options].sort(() => 0.5 - Math.random());
                return { ...q, options: shuffledOptions };
            });
            gameState.quiz.currentQuestionIndex = 0;
        }

        // --- Core Functions ---

        function switchScreen(screenName) {
            // Stop any playing audio
            Tone.Transport.stop();
            Tone.Transport.cancel(0);
            if(gameState.quiz.isPlaying) stopSound();
            if(gameState.minigame.isPlaying) stopMinigame();

            // Hide all screens
            Object.values(screens).forEach(s => s.classList.remove('active'));
            
            // Show target screen
            screens[screenName].classList.add('active');
            gameState.currentScreen = screenName;

            // Handle HUD visibility and Navigation button text
            if (screenName === 'start') {
                hud.classList.add('hidden');
            } else {
                hud.classList.remove('hidden');
                if (screenName === 'quiz') {
                    navBtn.innerText = 'Naar Minigame';
                    navBtn.onclick = () => switchScreen('minigame');
                    loadQuestion();
                } else if (screenName === 'minigame') {
                    navBtn.innerText = 'Terug naar Quiz';
                    navBtn.onclick = () => switchScreen('quiz');
                    checkMinigamePlayability();
                }
            }
            updateEnergyDisplay();
        }

        function updateEnergyDisplay() {
            energyDisplay.innerText = gameState.energy;
            
            // Highlight effect if energy changes
            energyDisplay.parentElement.classList.add('energy-pulse');
            setTimeout(() => {
                energyDisplay.parentElement.classList.remove('energy-pulse');
            }, 500);

            if(gameState.currentScreen === 'minigame') {
                checkMinigamePlayability();
            }
        }

        let currentLoops = [];

        async function initTone() {
            if (!isToneInitialized) {
                await Tone.start();
                isToneInitialized = true;
            }
        }

        async function toggleSound() {
            if (gameState.quiz.answered) return; 
            
            await initTone();

            if (gameState.quiz.isPlaying) {
                stopSound();
            } else {
                playSound();
            }
        }

        function playSound() {
            const currentQ = gameState.quiz.questions[gameState.quiz.currentQuestionIndex];
            if (currentQ.type !== 'audio') return;
            
            const generateFunc = audioGenerators[currentQ.audioKey];
            
            // Stop previous
            Tone.Transport.stop();
            Tone.Transport.cancel(0);
            currentLoops.forEach(loop => loop.dispose());
            currentLoops = [];

            // Generate new
            currentLoops = generateFunc();
            currentLoops.forEach(loop => loop.start(0));
            
            Tone.Transport.start();
            
            gameState.quiz.isPlaying = true;
            playIcon.innerText = '⏸️';
            playSoundBtn.classList.add('animate-pulse');
            document.getElementById('listen-text').innerText = 'Geluid speelt...';
        }

        function stopSound() {
            Tone.Transport.stop();
            gameState.quiz.isPlaying = false;
            playIcon.innerText = '▶️';
            playSoundBtn.classList.remove('animate-pulse');
            if(!gameState.quiz.answered) {
                document.getElementById('listen-text').innerText = 'Klik om te luisteren';
            }
        }

        function loadQuestion() {
            if (gameState.quiz.currentQuestionIndex >= gameState.quiz.questions.length) {
                generateQuiz();
            }

            const q = gameState.quiz.questions[gameState.quiz.currentQuestionIndex];
            gameState.quiz.answered = false;
            
            questionCount.innerText = `${gameState.quiz.currentQuestionIndex + 1}/${gameState.quiz.questions.length}`;
            quizTitle.innerText = `Raad het ${q.category}!`;
            
            feedbackMessage.classList.add('hidden');
            feedbackMessage.classList.remove('opacity-100');
            learningInfo.classList.add('hidden');
            learningInfo.classList.remove('opacity-100');
            
            stopSound();

            if (q.type === 'audio') {
                audioQuestionContainer.classList.remove('hidden');
                textQuestionContainer.classList.add('hidden');
                document.getElementById('listen-text').innerText = 'Klik om te luisteren';
            } else {
                audioQuestionContainer.classList.add('hidden');
                textQuestionContainer.classList.remove('hidden');
                questionText.innerText = q.text;
            }

            // Render options
            optionsContainer.innerHTML = '';
            q.options.forEach(option => {
                const btn = document.createElement('button');
                btn.className = 'bg-gray-700 hover:bg-gray-600 text-white font-bold py-4 px-6 rounded-xl border border-gray-600 shadow-md transition-all btn text-lg';
                btn.innerText = option;
                btn.onclick = () => checkAnswer(option, btn);
                optionsContainer.appendChild(btn);
            });
        }

        function checkAnswer(selectedOption, btnElement) {
            if (gameState.quiz.answered) return;
            
            gameState.quiz.answered = true;
            stopSound();
            
            const currentQ = gameState.quiz.questions[gameState.quiz.currentQuestionIndex];
            const correctOption = currentQ.correct;
            const buttons = optionsContainer.querySelectorAll('button');
            
            // Disable all buttons
            buttons.forEach(b => {
                b.disabled = true;
                b.classList.remove('hover:bg-gray-600');
            });

            feedbackMessage.classList.remove('hidden');
            learningInfo.classList.remove('hidden');
            learningInfo.innerHTML = currentQ.info;
            
            // Trigger reflow to ensure transition works
            void feedbackMessage.offsetWidth;
            feedbackMessage.classList.add('opacity-100');
            learningInfo.classList.add('opacity-100');

            if (selectedOption === correctOption) {
                // Correct
                btnElement.classList.replace('bg-gray-700', 'bg-green-600');
                btnElement.classList.replace('border-gray-600', 'border-green-500');
                
                feedbackMessage.innerText = 'Goed gedaan! +10 Energie ⚡';
                feedbackMessage.className = 'mt-6 text-center text-xl font-bold h-8 text-green-400 opacity-100 transition-opacity duration-300';
                
                gameState.energy += 10;
                updateEnergyDisplay();
                createParticles(btnElement);
                
                polySynth.triggerAttackRelease(["C5", "E5", "G5"], "8n");
            } else {
                // Incorrect
                btnElement.classList.replace('bg-gray-700', 'bg-red-600');
                btnElement.classList.replace('border-gray-600', 'border-red-500');
                
                // Highlight correct one
                buttons.forEach(b => {
                    if (b.innerText === correctOption) {
                        b.classList.replace('bg-gray-700', 'bg-green-600');
                        b.classList.replace('border-gray-600', 'border-green-500');
                    }
                });

                feedbackMessage.innerText = `Fout! Het was: ${correctOption}.`;
                feedbackMessage.className = 'mt-6 text-center text-xl font-bold h-8 text-red-400 opacity-100 transition-opacity duration-300';
                
                bassSynth.triggerAttackRelease("C2", "8n");
            }

            // Next question after a delay so they can read the info
            setTimeout(() => {
                gameState.quiz.currentQuestionIndex++;
                loadQuestion();
            }, 5000);
        }

        function createParticles(element) {
            const rect = element.getBoundingClientRect();
            const centerX = rect.left + rect.width / 2;
            const centerY = rect.top + rect.height / 2;

            for (let i = 0; i < 5; i++) {
                const particle = document.createElement('div');
                particle.innerHTML = '🎵';
                particle.className = 'note-particle';
                particle.style.left = `${centerX + (Math.random() * 40 - 20)}px`;
                particle.style.top = `${centerY + (Math.random() * 20 - 10)}px`;
                document.body.appendChild(particle);

                setTimeout(() => {
                    particle.remove();
                }, 1500);
            }
        }

        // --- Minigame Logic ---

        function checkMinigamePlayability() {
            if (gameState.energy >= gameState.minigame.cost) {
                btnPlayMinigame.disabled = false;
                overlayText.innerText = "Klaar om te spelen!";
                overlayText.className = "text-green-400 text-2xl font-bold mb-4 text-center px-4 pixel-font";
            } else {
                btnPlayMinigame.disabled = true;
                const needed = gameState.minigame.cost - gameState.energy;
                overlayText.innerText = `Niet genoeg energie.\nVerzamel nog ${needed} ⚡ in de quiz!`;
                overlayText.className = "text-red-400 text-xl mb-4 text-center px-4 font-bold";
            }
        }

        // Game objects
        const player = {
            x: 50, y: 0, width: 30, height: 40,
            velocityY: 0, gravity: 0.8, jumpPower: -12,
            isJumping: false, onGround: false
        };
        
        let platforms = [];
        let collectibles = [];
        let gameSpeed = 5;
        let frameCount = 0;
        let distanceTraveled = 0;

        // Resize canvas to fit container on load
        function resizeCanvas() {
            const container = document.getElementById('game-container');
            const targetWidth = Math.min(container.clientWidth, 600);
            const targetHeight = (targetWidth / 3) * 2; 
            
            canvas.style.width = targetWidth + 'px';
            canvas.style.height = targetHeight + 'px';
            
            canvas.width = 600;
            canvas.height = 400;
        }

        function startMinigame() {
            if (gameState.energy < gameState.minigame.cost) return;
            
            gameState.energy -= gameState.minigame.cost;
            updateEnergyDisplay();

            gameState.minigame.isPlaying = true;
            gameState.minigame.score = 0;
            gameScoreDisplay.innerText = 0;
            
            // Initial Parkour Setup
            gameSpeed = 5;
            frameCount = 0;
            distanceTraveled = 0;
            platforms = [
                { x: 0, y: 300, w: 800, h: 100 } // Start platform
            ];
            collectibles = [];
            
            player.x = 50;
            player.y = 200;
            player.velocityY = 0;
            player.isJumping = false;
            player.onGround = false;

            gameOverlay.style.display = 'none';
            gameUi.style.display = 'block';
            gameInstructions.style.display = 'block';
            canvas.style.display = 'block';
            btnPlayMinigame.style.display = 'none';

            initTone().then(() => {
                Tone.Transport.bpm.value = 150;
                const beat = new Tone.Loop(time => {
                    kick.triggerAttackRelease("C1", "8n", time);
                    if(frameCount % 120 < 60) {
                        hihat.triggerAttackRelease("16n", time + 0.1);
                    }
                }, "4n");
                currentLoops = [beat];
                beat.start(0);
                Tone.Transport.start();
            });

            gameLoop();
        }

        function stopMinigame(crashed = false) {
            gameState.minigame.isPlaying = false;
            cancelAnimationFrame(gameState.minigame.animationId);
            
            Tone.Transport.stop();
            currentLoops.forEach(l => l.dispose());
            currentLoops = [];

            gameOverlay.style.display = 'flex';
            canvas.style.display = 'none';
            btnPlayMinigame.style.display = 'block';
            gameUi.style.display = 'none';
            gameInstructions.style.display = 'none';

            if (crashed) {
                overlayText.innerHTML = `Gevallen!<br><span class="text-white text-lg">Score: ${gameState.minigame.score}</span><br>Probeer het nog eens!`;
                overlayText.className = "text-red-500 text-3xl font-bold mb-4 text-center px-4 pixel-font";
                bassSynth.triggerAttackRelease("C1", "2n"); 
            }
            
            checkMinigamePlayability();
        }

        function gameLoop() {
            if (!gameState.minigame.isPlaying) return;
            updateGame();
            drawGame();
            gameState.minigame.animationId = requestAnimationFrame(gameLoop);
        }

        function updateGame() {
            frameCount++;
            distanceTraveled += gameSpeed;
            
            // Apply Gravity
            player.velocityY += player.gravity;
            player.y += player.velocityY;
            
            // Platform Collision Logic
            player.onGround = false;

            for (let p of platforms) {
                // Solid from top for parkour feel
                if (player.x + player.width > p.x + 10 && 
                    player.x < p.x + p.w - 10 && 
                    player.y + player.height >= p.y && 
                    player.y + player.height - player.velocityY <= p.y + player.gravity + 15) {
                    
                    player.y = p.y - player.height;
                    player.velocityY = 0;
                    player.onGround = true;
                    player.isJumping = false;
                }
            }

            // Fall into gap (Game Over)
            if (player.y > canvas.height) {
                stopMinigame(true);
                return;
            }

            // Generate new platforms
            let lastPlatform = platforms[platforms.length - 1];
            if (lastPlatform.x + lastPlatform.w < canvas.width + 200) {
                let gap = Math.random() * 80 + 70; // 70 to 150 gap
                let heightChange = (Math.random() * 80) - 40; // -40 to +40 height change
                let newY = Math.max(150, Math.min(350, lastPlatform.y + heightChange));
                let newW = Math.random() * 200 + 100; // 100 to 300 width
                
                platforms.push({
                    x: lastPlatform.x + lastPlatform.w + gap,
                    y: newY,
                    w: newW,
                    h: canvas.height - newY + 50 // extend down
                });

                // Chance to spawn a collectible above the new platform
                if (Math.random() > 0.4) {
                    collectibles.push({
                        x: lastPlatform.x + lastPlatform.w + gap + (newW/2) - 10,
                        y: newY - 60 - Math.random() * 40,
                        width: 20,
                        height: 20,
                        color: '#10b981',
                        collected: false
                    });
                }
            }

            // Move and cleanup platforms
            for (let i = platforms.length - 1; i >= 0; i--) {
                platforms[i].x -= gameSpeed;
                if (platforms[i].x + platforms[i].w < -100) {
                    platforms.splice(i, 1);
                    gameState.minigame.score += 5;
                    gameScoreDisplay.innerText = gameState.minigame.score;
                }
            }

            // Move and check collectibles
            for (let i = collectibles.length - 1; i >= 0; i--) {
                let col = collectibles[i];
                col.x -= gameSpeed;

                if (!col.collected && 
                    player.x < col.x + col.width &&
                    player.x + player.width > col.x &&
                    player.y < col.y + col.height &&
                    player.y + player.height > col.y) {
                    
                    col.collected = true;
                    gameState.minigame.score += 20;
                    gameScoreDisplay.innerText = gameState.minigame.score;
                    polySynth.triggerAttackRelease("C6", "16n");
                }

                if (col.x + col.width < 0) {
                    collectibles.splice(i, 1);
                }
            }

            // Increase difficulty
            if (frameCount % 400 === 0 && gameSpeed < 10) {
                gameSpeed += 0.2;
            }
        }

        function drawGame() {
            // Clear canvas
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // Draw background
            const bgGrad = ctx.createLinearGradient(0, 0, 0, canvas.height);
            bgGrad.addColorStop(0, '#1e1b4b');
            bgGrad.addColorStop(1, '#312e81');
            ctx.fillStyle = bgGrad;
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Draw scrolling background elements (parallax effect)
            ctx.fillStyle = 'rgba(255, 255, 255, 0.05)';
            for(let i = 0; i < 5; i++) {
                let x = ((i * 200) - (distanceTraveled * 0.2)) % (canvas.width + 200);
                if(x < -200) x += canvas.width + 400;
                ctx.beginPath();
                ctx.arc(x, 100 + i*30, 40 + i*10, 0, Math.PI*2);
                ctx.fill();
            }

            // Draw Platforms
            platforms.forEach(p => {
                ctx.fillStyle = '#4338ca'; // Indigo
                ctx.fillRect(p.x, p.y, p.w, p.h);
                
                ctx.fillStyle = '#6366f1'; // Lighter edge
                ctx.fillRect(p.x, p.y, p.w, 10);
                
                ctx.fillStyle = '#312e81'; // Details
                ctx.fillRect(p.x + 10, p.y + 20, p.w - 20, 5);
            });

            // Draw Player (Poppetje / Stickman)
            ctx.strokeStyle = '#fbbf24'; // Yellow
            ctx.lineWidth = 4;
            ctx.lineCap = 'round';
            ctx.lineJoin = 'round';
            
            const px = player.x + player.width / 2;
            const py = player.y;

            ctx.beginPath();
            // Head
            ctx.arc(px, py + 8, 6, 0, Math.PI * 2);
            // Body
            ctx.moveTo(px, py + 14);
            ctx.lineTo(px, py + 26);
            
            // Run animation logic
            let legOffset = 0;
            let armOffset = 0;
            if (player.onGround) {
                legOffset = Math.sin(frameCount * 0.3) * 10;
                armOffset = Math.cos(frameCount * 0.3) * 10;
            } else {
                // Jumping pose
                legOffset = 5;
                armOffset = -10;
            }

            // Arms
            ctx.moveTo(px, py + 16);
            ctx.lineTo(px - 10 + armOffset, py + 24); // Left arm
            ctx.moveTo(px, py + 16);
            ctx.lineTo(px + 10 - armOffset, py + 24); // Right arm
            
            // Legs
            ctx.moveTo(px, py + 26);
            ctx.lineTo(px - 6 + legOffset, py + 38); // Left leg
            ctx.moveTo(px, py + 26);
            ctx.lineTo(px + 6 - legOffset, py + 38); // Right leg
            
            ctx.stroke();

            // Draw Collectibles (Notes)
            collectibles.forEach(col => {
                if (!col.collected) {
                    ctx.fillStyle = col.color;
                    ctx.beginPath();
                    ctx.arc(col.x + 10, col.y + 15, 6, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.fillRect(col.x + 14, col.y, 2, 15);
                    
                    ctx.shadowBlur = 10;
                    ctx.shadowColor = col.color;
                    ctx.fill();
                    ctx.shadowBlur = 0;
                }
            });
        }

        function jump() {
            if (gameState.currentScreen === 'minigame' && gameState.minigame.isPlaying) {
                if (player.onGround) {
                    player.velocityY = player.jumpPower;
                    player.isJumping = true;
                    player.onGround = false;
                    snare.triggerAttackRelease("16n");
                }
            }
        }

        // --- Event Listeners ---
        btnStart.addEventListener('click', () => {
            generateQuiz();
            switchScreen('quiz');
        });

        playSoundBtn.addEventListener('click', toggleSound);
        
        btnPlayMinigame.addEventListener('click', startMinigame);

        // Minigame controls (Keyboard & Touch/Mouse)
        window.addEventListener('keydown', (e) => {
            if (e.code === 'Space' || e.code === 'ArrowUp') {
                if(gameState.currentScreen === 'minigame' && !gameState.minigame.isPlaying && !btnPlayMinigame.disabled && gameOverlay.style.display !== 'none') {
                    // Prevent immediate jump on start
                    setTimeout(startMinigame, 100); 
                } else {
                    jump();
                }
                e.preventDefault();
            }
        });

        canvas.addEventListener('mousedown', jump);
        canvas.addEventListener('touchstart', (e) => {
            e.preventDefault(); // Prevent scrolling
            jump();
        }, {passive: false});

        window.addEventListener('resize', resizeCanvas);

        // --- Initialize ---
        resizeCanvas();
        checkMinigamePlayability();

    </script>
</body>
</html>
