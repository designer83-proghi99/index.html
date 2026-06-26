<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cosmic Bubble Shooter</title>
    <style>
        :root {
            --primary-glow: rgba(0, 240, 255, 0.4);
            --danger-glow: rgba(255, 46, 147, 0.4);
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            user-select: none;
            -webkit-user-select: none;
        }

        body {
            background: radial-gradient(circle at top, #1b0f2a, #0f0c1b, #0a0714);
            color: #ffffff;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            overflow: hidden;
        }

        #game-container {
            width: 100%;
            max-width: 450px;
            height: 92vh;
            display: flex;
            flex-direction: column;
            background: rgba(27, 15, 42, 0.3);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 24px;
            padding: 12px;
            box-shadow: 0 20px 50px rgba(0, 0, 0, 0.6);
            position: relative;
            backdrop-filter: blur(10px);
        }

        /* Top Status Bar Styling */
        .status-card {
            background: rgba(34, 18, 53, 0.6);
            border: 1px solid rgba(255, 255, 255, 0.15);
            border-radius: 18px;
            padding: 12px 16px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
        }

        .status-left h3 {
            color: #00f0ff;
            font-size: 11px;
            font-family: monospace;
            letter-spacing: 1px;
            text-transform: uppercase;
        }

        .status-left .score-container {
            display: flex;
            align-items: center;
            gap: 6px;
            margin-top: 2px;
        }

        .status-left .score-container svg {
            fill: #ffff33;
            width: 18px;
            height: 18px;
        }

        .status-left .score-val {
            font-size: 22px;
            font-weight: 800;
        }

        .status-center {
            text-align: center;
        }

        .status-center span {
            color: rgba(255, 255, 255, 0.5);
            font-size: 10px;
            font-weight: bold;
            display: block;
        }

        .status-center .best-val {
            color: #ff9900;
            font-size: 18px;
            font-weight: 800;
            font-family: monospace;
        }

        .status-actions {
            display: flex;
            gap: 8px;
        }

        .action-btn {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            border: none;
            background: rgba(255, 255, 255, 0.12);
            color: #ffffff;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: background 0.2s, transform 0.1s;
        }

        .action-btn:hover {
            background: rgba(255, 255, 255, 0.2);
        }

        .action-btn:active {
            transform: scale(0.9);
        }

        .action-btn svg {
            width: 18px;
            height: 18px;
            fill: currentColor;
        }

        /* Main Game Screen Canvas */
        .canvas-container {
            flex: 1;
            position: relative;
            border-radius: 24px;
            background: rgba(0, 0, 0, 0.2);
            border: 2px solid rgba(128, 247, 255, 0.1);
            overflow: hidden;
            margin-bottom: 10px;
            cursor: crosshair;
        }

        #game-canvas {
            display: block;
            width: 100%;
            height: 100%;
        }

        /* Bottom Controls Area */
        .controls-card {
            background: rgba(0, 0, 0, 0.15);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 20px;
            padding: 12px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
        }

        .controls-left h4 {
            color: rgba(255, 255, 255, 0.4);
            font-size: 9px;
            font-weight: bold;
            letter-spacing: 0.5px;
        }

        .controls-left p {
            color: #c0b3e0;
            font-size: 11px;
            line-height: 15px;
            margin-top: 2px;
        }

        .controls-right {
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .queue-label h4 {
            color: rgba(255, 255, 255, 0.4);
            font-size: 9px;
            font-weight: bold;
            text-align: right;
        }

        .queue-label span {
            font-size: 12px;
            font-weight: bold;
            display: block;
            text-align: right;
        }

        .swap-bubble-container {
            width: 56px;
            height: 56px;
            border-radius: 50%;
            background: rgba(34, 18, 53, 0.6);
            border: 1px solid rgba(255, 255, 255, 0.2);
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: transform 0.2s, border-color 0.2s;
        }

        .swap-bubble-container:hover {
            border-color: rgba(255, 255, 255, 0.4);
            transform: scale(1.05);
        }

        .swap-bubble-inner {
            width: 36px;
            height: 36px;
            border-radius: 50%;
            position: relative;
            box-shadow: inset -4px -4px 8px rgba(0,0,0,0.4), 
                        inset 4px 4px 8px rgba(255,255,255,0.4);
        }

        /* Ad Banner Placeholder (Empty space left at the bottom) */
        .ad-space {
            width: 100%;
            height: 60px;
            background: transparent;
            border-radius: 12px;
        }

        /* Modal Overlays (GameOver & Win Screens) */
        .modal-overlay {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(15, 12, 27, 0.93);
            border-radius: 24px;
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 10;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.3s ease;
        }

        .modal-overlay.active {
            opacity: 1;
            pointer-events: auto;
        }

        .modal-card {
            width: 85%;
            background: #1b0f2a;
            border-radius: 32px;
            padding: 24px;
            text-align: center;
            box-shadow: 0 24px 48px rgba(0,0,0,0.8);
            transform: scale(0.85);
            transition: transform 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }

        .modal-overlay.active .modal-card {
            transform: scale(1);
        }

        .modal-title {
            font-size: 32px;
            font-weight: 850;
            letter-spacing: 1px;
            margin-bottom: 8px;
        }

        .modal-title.over { color: #ff2e93; text-shadow: 0 0 15px var(--danger-glow); }
        .modal-title.win { color: #39ff14; text-shadow: 0 0 15px rgba(57, 255, 20, 0.4); }

        .modal-desc {
            color: #c0b3e0;
            font-size: 14px;
            margin-bottom: 24px;
        }

        .modal-stats {
            display: flex;
            justify-content: space-around;
            margin-bottom: 32px;
        }

        .stat-item span {
            color: rgba(255, 255, 255, 0.6);
            font-size: 12px;
        }

        .stat-item h2 {
            font-size: 24px;
            font-weight: bold;
            margin-top: 4px;
        }

        .modal-btn {
            width: 100%;
            height: 56px;
            border-radius: 16px;
            border: none;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            color: #ffffff;
            transition: filter 0.2s, transform 0.1s;
        }

        .modal-btn:active {
            transform: scale(0.98);
        }

        .modal-btn.over-btn { background: #ff2e93; }
        .modal-btn.win-btn { background: #00f0ff; color: #0a0714; }

        .modal-btn svg {
            width: 20px;
            height: 20px;
            fill: currentColor;
        }
    </style>
</head>
<body>

    <div id="game-container">
        <!-- TOP STATUS BAR -->
        <div class="status-card">
            <div class="status-left">
                <h3 id="level-display">Level 1</h3>
                <div class="score-container">
                    <svg viewBox="0 0 24 24"><path d="M12 17.27L18.18 21l-1.64-7.03L22 9.24l-7.19-.61L12 2 9.19 8.63 2 9.24l5.46 4.73L5.82 21z"/></svg>
                    <span class="score-val" id="score-display">0</span>
                </div>
            </div>
            <div class="status-center">
                <span>BEST SCORE</span>
                <span class="best-val" id="high-score-display">0</span>
            </div>
            <div class="status-actions">
                <button class="action-btn" id="mute-btn">
                    <svg id="volume-icon" viewBox="0 0 24 24">
                        <path d="M3 9v6h4l5 5V4L7 9H3zm13.5 3c0-1.77-1.02-3.29-2.5-4.03v8.05c1.48-.73 2.5-2.25 2.5-4.02zM14 3.23v2.06c2.89.86 5 3.54 5 6.71s-2.11 5.85-5 6.71v2.06c4.01-.91 7-4.49 7-8.77s-2.99-7.86-7-8.77z"/>
                    </svg>
                </button>
                <button class="action-btn" id="restart-btn">
                    <svg viewBox="0 0 24 24"><path d="M17.65 6.35C16.2 4.9 14.21 4 12 4c-4.42 0-7.99 3.58-7.99 8s3.57 8 7.99 8c3.73 0 6.84-2.55 7.73-6h-2.08c-.82 2.33-3.04 4-5.65 4-3.31 0-6-2.69-6-6s2.69-6 6-6c1.66 0 3.14.69 4.22 1.78L13 11h7V4l-2.35 2.35z"/></svg>
                </button>
            </div>
        </div>

        <!-- MAIN CANVAS SCREEN -->
        <div class="canvas-container" id="canvas-wrapper">
            <canvas id="game-canvas"></canvas>
        </div>

        <!-- BOTTOM CONTROLS PANEL -->
        <div class="controls-card">
            <div class="controls-left">
                <h4>HOW TO PLAY:</h4>
                <p>Drag to aim laser pointer,<br>release to shoot bubbles!</p>
            </div>
            <div class="controls-right">
                <div class="queue-label">
                    <h4>NEXT BUBBLE:</h4>
                    <span id="next-bubble-label">Blue</span>
                </div>
                <div class="swap-bubble-container" id="swap-btn">
                    <div class="swap-bubble-inner" id="swap-bubble-preview"></div>
                </div>
            </div>
        </div>

        <!-- AD ADAPTER CONTAINER (Empty space left empty) -->
        <div class="ad-space"></div>

        <!-- GAME OVER OVERLAY -->
        <div class="modal-overlay" id="game-over-overlay">
            <div class="modal-card">
                <h1 class="modal-title over">GAME OVER</h1>
                <p class="modal-desc">Oh no! The bubbles reached the bottom!</p>
                <div class="modal-stats">
                    <div class="stat-item">
                        <span>Your Score</span>
                        <h2 id="over-score">0</h2>
                    </div>
                    <div class="stat-item">
                        <span>High Score</span>
                        <h2 id="over-best" style="color: #ff9900;">0</h2>
                    </div>
                </div>
                <button class="modal-btn over-btn" id="retry-btn">
                    <svg viewBox="0 0 24 24"><path d="M17.65 6.35C16.2 4.9 14.21 4 12 4c-4.42 0-7.99 3.58-7.99 8s3.57 8 7.99 8c3.73 0 6.84-2.55 7.73-6h-2.08c-.82 2.33-3.04 4-5.65 4-3.31 0-6-2.69-6-6s2.69-6 6-6c1.66 0 3.14.69 4.22 1.78L13 11h7V4l-2.35 2.35z"/></svg>
                    PLAY AGAIN
                </button>
            </div>
        </div>

        <!-- GAME WIN OVERLAY -->
        <div class="modal-overlay" id="game-win-overlay">
            <div class="modal-card">
                <h1 class="modal-title win">CONGRATS!</h1>
                <p class="modal-desc">You cleared all the bubbles!</p>
                <div class="modal-stats" style="justify-content: center;">
                    <div class="stat-item">
                        <span style="color: #00f0ff; font-weight: bold; letter-spacing: 0.5px;">UPCOMING LEVEL</span>
                        <h2 id="next-level-title">LEVEL 2</h2>
                    </div>
                </div>
                <button class="modal-btn win-btn" id="next-level-btn">
                    <svg viewBox="0 0 24 24"><path d="M8 5v14l11-7z"/></svg>
                    START NEXT LEVEL
                </button>
            </div>
        </div>
    </div>

    <!-- SOUND GENERATOR ENGINE + GAME SYSTEM LOGIC -->
    <script>
        // ============================================================================
        // Web Audio Synth Engine (Retro Synthesizer)
        // ============================================================================
        const Synth = {
            ctx: null,
            isMuted: false,
            musicInterval: null,
            melody: [261.63, 293.66, 329.63, 392.00, 440.00, 392.00, 329.63, 293.66],
            melodyIndex: 0,

            init() {
                // AudioContext initialized safely on first gesture/action
                if (!this.ctx) {
                    this.ctx = new (window.AudioContext || window.webkitAudioContext)();
                }
                if (this.ctx.state === 'suspended') {
                    this.ctx.resume();
                }
            },

            playTone(freqStart, freqEnd, duration, type = 'sine', volume = 0.3) {
                if (this.isMuted) return;
                this.init();
                try {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();

                    osc.type = type;
                    osc.frequency.setValueAtTime(freqStart, this.ctx.currentTime);
                    if (freqEnd !== freqStart) {
                        osc.frequency.exponentialRampToValueAtTime(freqEnd, this.ctx.currentTime + duration);
                    }

                    gain.gain.setValueAtTime(volume, this.ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, this.ctx.currentTime + duration);

                    osc.connect(gain);
                    gain.connect(this.ctx.destination);

                    osc.start();
                    osc.stop(this.ctx.currentTime + duration);
                } catch (e) {
                    console.error(e);
                }
            },

            playShoot() {
                this.playTone(550, 250, 0.12, 'sawtooth', 0.15);
            },

            playPop() {
                this.playTone(700, 1400, 0.05, 'triangle', 0.25);
            },

            playBounce() {
                this.playTone(1100, 1100, 0.02, 'sine', 0.1);
            },

            playSwap() {
                this.playTone(380, 600, 0.08, 'sine', 0.2);
            },

            playWin() {
                const notes = [523.25, 659.25, 783.99, 1046.50];
                notes.forEach((freq, i) => {
                    setTimeout(() => {
                        this.playTone(freq, freq, 0.15, 'triangle', 0.2);
                    }, i * 120);
                });
            },

            playGameOver() {
                this.playTone(250, 80, 0.45, 'sawtooth', 0.2);
            },

            startMusic() {
                if (this.isMuted) return;
                this.stopMusic();
                this.musicInterval = setInterval(() => {
                    if (this.isMuted) return;
                    const freq = this.melody[this.melodyIndex];
                    this.melodyIndex = (this.melodyIndex + 1) % this.melody.length;
                    
                    // Very soft ambient chimes
                    this.playTone(freq, freq, 0.35, 'sine', 0.018);
                }, 1200);
            },

            stopMusic() {
                if (this.musicInterval) {
                    clearInterval(this.musicInterval);
                    this.musicInterval = null;
                }
            }
        };

        // ============================================================================
        // Config & Enums
        // ============================================================================
        const BubbleColors = {
            RED:     { name: "Red",     main: "#ff2e93", glow: "#ff85c2" },
            BLUE:    { name: "Blue",    main: "#00f0ff", glow: "#80f7ff" },
            GREEN:   { name: "Green",   main: "#39ff14", glow: "#b5ff9c" },
            YELLOW:  { name: "Yellow",  main: "#ffff33", glow: "#ffffb3" },
            MAGENTA: { name: "Magenta", main: "#e000ff", glow: "#f3b3ff" },
            ORANGE:  { name: "Orange",  main: "#ff6600", glow: "#ffb380" }
        };
        const COLOR_KEYS = Object.keys(BubbleColors);

        // ============================================================================
        // Core Game Logic State
        // ============================================================================
        const Game = {
            canvas: null,
            ctx: null,
            width: 1080,
            height: 1920,
            bubbleRadius: 50,
            maxRows: 12,
            maxCols: 8,

            board: {}, // "row,col" -> colorKey
            score: 0,
            highScore: 0,
            level: 1,
            gameOver: false,
            gameWon: false,

            currentBubbleColor: 'RED',
            nextBubbleColor: 'BLUE',

            // Shooting state
            isShooting: false,
            shotX: 0, shotY: 0,
            shotVx: 0, shotVy: 0,

            // Aiming state
            isAiming: false,
            aimAngle: -Math.PI / 2,

            foulMeter: 0,
            particles: [],

            init() {
                this.canvas = document.getElementById('game-canvas');
                this.ctx = this.canvas.getContext('2d');
                this.canvas.width = this.width;
                this.canvas.height = this.height;

                this.bubbleRadius = this.width / (this.maxCols + 0.5) / 2;

                // Load High Score
                this.highScore = parseInt(localStorage.getItem('bubble_high_score') || '0');
                document.getElementById('high-score-display').innerText = this.highScore;

                this.reset();
                this.setupInput();
                this.loop();
            },

            reset() {
                this.board = {};
                this.particles = [];
                this.score = 0;
                this.level = 1;
                this.gameOver = false;
                this.gameWon = false;
                this.foulMeter = 0;
                this.isShooting = false;
                this.isAiming = false;

                document.getElementById('score-display').innerText = '0';
                document.getElementById('level-display').innerText = `Level ${this.level}`;
                
                // Populating initial grid
                for (let r = 0; r < 4; r++) {
                    for (let c = 0; c < this.maxCols; c++) {
                        if (r % 2 === 1 && c === this.maxCols - 1) continue;
                        const ran
