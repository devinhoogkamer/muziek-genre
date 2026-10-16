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
