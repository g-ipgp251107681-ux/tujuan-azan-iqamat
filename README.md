<html lang="ms">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Misi Azan & Iqamat - Tahun 4</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;500;600;700&family=Nunito:wght@600;700;800&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Nunito', sans-serif;
            background: linear-gradient(180deg, #bae6fd 0%, #e0f2fe 50%, #dcfce7 100%);
            min-height: 100vh;
            overflow-x: hidden;
            user-select: none;
            -webkit-user-select: none;
        }
        h1, h2, h3, .font-display {
            font-family: 'Fredoka', sans-serif;
        }
        
        /* Custom Button 3D Bubble Effect */
        .btn-game {
            transition: all 0.15s cubic-bezier(0.4, 0, 0.2, 1);
            box-shadow: 0 6px 0 rgba(0,0,0,0.2);
        }
        .btn-game:active {
            transform: translateY(4px);
            box-shadow: 0 2px 0 rgba(0,0,0,0.2);
        }
        
        /* Floating Clouds & Mosque Animation */
        @keyframes floatSlow {
            0%, 100% { transform: translateY(0px); }
            50% { transform: translateY(-8px); }
        }
        .animate-float {
            animation: floatSlow 3.5s ease-in-out infinite;
        }

        @keyframes pulseScale {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }
        .animate-pulse-slow {
            animation: pulseScale 2.5s ease-in-out infinite;
        }

        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            20%, 60% { transform: translateX(-8px); }
            40%, 80% { transform: translateX(8px); }
        }
        .animate-shake {
            animation: shake 0.4s ease-in-out;
        }

        @keyframes popIn {
            0% { transform: scale(0.7); opacity: 0; }
            80% { transform: scale(1.05); opacity: 1; }
            100% { transform: scale(1); opacity: 1; }
        }
        .animate-pop {
            animation: popIn 0.35s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }

        /* Canvas container */
        #arcade-canvas {
            touch-action: none;
            display: block;
            width: 100%;
            height: 100%;
        }

        /* Custom Scrollbar for Notes */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb {
            background: #94a3b8;
            border-radius: 10px;
        }
    </style>
</head>
<body class="text-slate-800 flex flex-col items-center justify-between p-2 sm:p-4">

    <!-- Decorative Mosque Skyline Background SVG -->
    <div class="fixed inset-0 pointer-events-none z-0 overflow-hidden opacity-35">
        <svg viewBox="0 0 1440 320" class="absolute bottom-0 w-full h-auto" preserveAspectRatio="none">
            <path fill="#4ade80" fill-opacity="0.6" d="M0,224L60,213.3C120,203,240,181,360,186.7C480,192,600,224,720,218.7C840,213,960,171,1080,165.3C1200,160,1320,192,1380,208L1440,224L1440,320L1380,320C1320,320,1200,320,1080,320C960,320,840,320,720,320C600,320,480,320,360,320C240,320,120,320,60,320L0,320Z"></path>
            <!-- Silhouette Mosque Left -->
            <path fill="#16a34a" fill-opacity="0.4" d="M120,240 L120,140 L130,110 L140,140 L140,240 Z M150,240 Q190,150 230,240 Z M188,195 L192,195 L192,175 L188,175 Z"></path>
            <!-- Silhouette Mosque Right -->
            <path fill="#15803d" fill-opacity="0.4" d="M1240,240 L1240,120 L1255,90 L1270,120 L1270,240 Z M1150,240 Q1195,140 1240,240 Z"></path>
        </svg>
    </div>

    <!-- Top HUD Header (Visible during gameplay) -->
    <header id="game-hud" class="hidden relative z-20 w-full max-w-4xl bg-white/95 backdrop-blur-md rounded-2xl shadow-lg border-4 border-sky-300 px-3 py-2 sm:px-5 sm:py-3 mb-2 flex flex-wrap items-center justify-between gap-2">
        <div class="flex items-center gap-2 sm:gap-3">
            <button onclick="returnToMenu()" class="bg-amber-100 hover:bg-amber-200 text-amber-800 font-bold px-3 py-1.5 rounded-xl border-2 border-amber-300 text-xs sm:text-sm flex items-center gap-1 transition">
                <span>🏠</span> <span class="hidden sm:inline">Menu</span>
            </button>
            <div class="bg-indigo-100 border-2 border-indigo-300 px-3 py-1 rounded-xl">
                <span class="text-xs font-bold text-indigo-600 uppercase block leading-none">Peringkat</span>
                <span id="hud-level" class="font-display font-bold text-sm sm:text-base text-indigo-900">1 / 3</span>
            </div>
        </div>

        <!-- Lives & Combo -->
        <div class="flex items-center gap-2 sm:gap-4">
            <div id="hud-combo" class="hidden bg-amber-400 text-amber-950 font-display font-bold px-2.5 py-1 rounded-full text-xs sm:text-sm animate-bounce border-2 border-amber-600">
                🔥 Kombo x2!
            </div>
            <div class="flex items-center gap-1 bg-rose-50 px-2.5 py-1 rounded-xl border-2 border-rose-200" id="hud-lives">
                <span class="text-lg sm:text-xl">❤️</span>
                <span class="text-lg sm:text-xl">❤️</span>
                <span class="text-lg sm:text-xl">❤️</span>
            </div>
            <div class="bg-emerald-100 border-2 border-emerald-400 px-3 py-1 rounded-xl text-right">
                <span class="text-xs font-bold text-emerald-700 uppercase block leading-none">Markah</span>
                <span id="hud-score" class="font-display font-bold text-base sm:text-xl text-emerald-900">0</span>
            </div>
            <button onclick="togglePause()" id="pause-btn-hud" class="bg-amber-100 hover:bg-amber-200 text-amber-900 font-bold px-3 py-1.5 rounded-xl border-2 border-amber-400 text-xs sm:text-sm flex items-center gap-1 transition" title="Jeda / Rehat (Esc / P)">
                <span>⏸️</span> <span class="hidden sm:inline">Pause</span>
            </button>
            <button onclick="toggleSound()" id="sound-btn-hud" class="bg-sky-100 hover:bg-sky-200 p-2 rounded-xl border-2 border-sky-300 text-lg" title="Bunyi">
                🔊
            </button>
        </div>
    </header>

    <!-- ==================== MODAL: PAUSE OVERLAY (JEDA PERMAINAN) ==================== -->
    <section id="modal-pause" class="hidden fixed inset-0 z-50 bg-slate-900/70 backdrop-blur-sm flex items-center justify-center p-4">
        <div class="bg-white rounded-3xl border-4 border-amber-400 max-w-md w-full p-6 sm:p-8 text-center shadow-2xl animate-pop">
            <div class="text-6xl mb-3 animate-bounce">⏸️</div>
            <h2 class="font-display font-extrabold text-2xl sm:text-3xl text-slate-800 mb-2">
                Permainan Dijeda
            </h2>
            <p class="text-slate-600 font-semibold text-sm sm:text-base mb-6">
                Ambil masa untuk berehat atau baca semula nota sebelum menyambung misi!
            </p>
            <div class="flex flex-col gap-3">
                <button onclick="togglePause()" class="btn-game w-full bg-emerald-500 hover:bg-emerald-400 text-white font-display font-bold text-lg py-3.5 rounded-2xl border-2 border-emerald-700 flex items-center justify-center gap-2">
                    <span>▶️</span> Sambung Main
                </button>
                <button onclick="openNotes()" class="btn-game w-full bg-sky-100 hover:bg-sky-200 text-sky-900 font-display font-bold text-base py-3 rounded-2xl border-2 border-sky-400 flex items-center justify-center gap-2">
                    <span>📖</span> Lihat Nota Ringkas
                </button>
                <button onclick="restartFromPause()" class="btn-game w-full bg-amber-400 hover:bg-amber-300 text-amber-950 font-display font-bold text-base py-3 rounded-2xl border-2 border-amber-600 flex items-center justify-center gap-2">
                    <span>🔄</span> Mula Semula
                </button>
                <button onclick="menuFromPause()" class="btn-game w-full bg-rose-100 hover:bg-rose-200 text-rose-800 font-display font-bold text-base py-3 rounded-2xl border-2 border-rose-300 flex items-center justify-center gap-2">
                    <span>🏠</span> Menu Utama
                </button>
            </div>
        </div>
    </section>

    <!-- MAIN CONTAINER -->
    <main class="relative z-10 w-full max-w-4xl flex-1 flex flex-col items-center justify-center my-auto">

        <!-- ==================== SCREEN 1: MAIN MENU ==================== -->
        <section id="screen-menu" class="w-full max-w-2xl bg-white/95 backdrop-blur-md rounded-3xl shadow-2xl border-4 border-sky-400 p-5 sm:p-8 text-center animate-pop">
            <!-- Top Badge -->
            <div class="flex justify-between items-center mb-2">
                <span class="bg-amber-100 text-amber-800 border-2 border-amber-300 font-bold text-xs sm:text-sm px-3 py-1 rounded-full">
                    🕌 Pendidikan Islam • Tahun 4
                </span>
                <button onclick="toggleSound()" id="sound-btn-menu" class="bg-sky-100 hover:bg-sky-200 text-sky-800 font-bold px-3 py-1 rounded-full border-2 border-sky-300 text-sm flex items-center gap-1">
                    <span id="sound-icon-menu">🔊</span> <span id="sound-text-menu">Bunyi: Hidup</span>
                </button>
            </div>

            <!-- Cartoon Mosque & Bilal Illustration SVG -->
            <div class="my-3 flex justify-center animate-float">
                <svg class="w-36 h-28 sm:w-44 sm:h-32" viewBox="0 0 220 150">
                    <!-- Sun -->
                    <circle cx="185" cy="30" r="18" fill="#fde047" />
                    <!-- Clouds -->
                    <path d="M25,40 Q35,25 50,35 Q65,25 75,40 Z" fill="#e0f2fe" />
                    <!-- Minaret -->
                    <rect x="35" y="45" width="18" height="85" rx="3" fill="#fef08a" stroke="#ca8a04" stroke-width="2"/>
                    <path d="M33,45 L44,25 L55,45 Z" fill="#16a34a" stroke="#14532d" stroke-width="2"/>
                    <circle cx="44" cy="20" r="4" fill="#eab308"/>
                    <!-- Main Dome -->
                    <path d="M65,95 Q110,25 155,95 Z" fill="#22c55e" stroke="#15803d" stroke-width="3"/>
                    <path d="M110,38 L110,22" stroke="#eab308" stroke-width="3" stroke-linecap="round"/>
                    <circle cx="110" cy="20" r="4" fill="#eab308"/>
                    <!-- Mosque Base -->
                    <rect x="60" y="95" width="100" height="40" rx="6" fill="#ffffff" stroke="#0284c7" stroke-width="3"/>
                    <!-- Mosque Arch Door -->
                    <path d="M98,135 L98,112 Q110,100 122,112 L122,135 Z" fill="#0ea5e9"/>
                    <!-- Windows -->
                    <circle cx="80" cy="115" r="7" fill="#bae6fd" stroke="#0284c7" stroke-width="2"/>
                    <circle cx="140" cy="115" r="7" fill="#bae6fd" stroke="#0284c7" stroke-width="2"/>
                    <!-- Ground -->
                    <rect x="15" y="133" width="190" height="10" rx="5" fill="#4ade80"/>
                </svg>
            </div>

            <!-- Title -->
            <div class="bg-sky-50 border-4 border-sky-200 rounded-3xl py-3 px-4 mb-4 shadow-inner">
                <p class="text-sm sm:text-base font-bold text-sky-700 tracking-wide uppercase">Permainan Interaktif</p>
                <h1 class="text-3xl sm:text-5xl font-extrabold tracking-tight mt-1">
                    <span class="text-slate-800">Misi </span>
                    <span class="text-emerald-600">Azan</span>
                    <span class="text-slate-800"> & </span>
                    <span class="text-pink-600">Iqamat</span>
                </h1>
                <p class="text-xs sm:text-sm text-slate-600 font-semibold mt-1 italic">
                    "Bersedia untuk Solat, Bersedia untuk Kejayaan" 💖
                </p>
            </div>

            <!-- Level Preview Pills -->
            <div class="grid grid-cols-3 gap-2 mb-6 text-xs sm:text-sm">
                <div class="bg-emerald-50 border-2 border-emerald-300 rounded-2xl p-2">
                    <div class="text-xl sm:text-2xl mb-1">🎯</div>
                    <div class="font-bold text-emerald-800">Peringkat 1</div>
                    <div class="text-emerald-600 text-[11px] sm:text-xs">Tangkap Seruan</div>
                </div>
                <div class="bg-pink-50 border-2 border-pink-300 rounded-2xl p-2">
                    <div class="text-xl sm:text-2xl mb-1">💡</div>
                    <div class="font-bold text-pink-800">Peringkat 2</div>
                    <div class="text-pink-600 text-[11px] sm:text-xs">Cabaran Situasi</div>
                </div>
                <div class="bg-amber-50 border-2 border-amber-300 rounded-2xl p-2">
                    <div class="text-xl sm:text-2xl mb-1">🏆</div>
                    <div class="font-bold text-amber-800">Peringkat 3</div>
                    <div class="text-amber-600 text-[11px] sm:text-xs">Urutan Solat</div>
                </div>
            </div>

            <!-- Action Buttons -->
            <div class="flex flex-col sm:flex-row gap-3 justify-center">
                <button onclick="openNotes()" class="btn-game bg-amber-400 hover:bg-amber-300 text-amber-950 font-display font-bold text-lg sm:text-xl px-6 py-4 rounded-2xl border-2 border-amber-600 flex items-center justify-center gap-2">
                    <span>📖</span> Nota Ringkas
                </button>
                <button onclick="startGame()" class="btn-game animate-pulse-slow bg-emerald-500 hover:bg-emerald-400 text-white font-display font-bold text-lg sm:text-xl px-8 py-4 rounded-2xl border-2 border-emerald-700 flex items-center justify-center gap-2">
                    <span>🚀</span> Mula Bermain!
                </button>
            </div>
        </section>

        <!-- ==================== MODAL: NOTA RINGKAS (POSTER CONTENT) ==================== -->
        <section id="modal-notes" class="hidden fixed inset-0 z-50 bg-slate-900/60 backdrop-blur-sm flex items-center justify-center p-3 sm:p-5">
            <div class="bg-white rounded-3xl border-4 border-amber-400 max-w-3xl w-full max-h-[90vh] flex flex-col shadow-2xl animate-pop overflow-hidden">
                <!-- Header -->
                <div class="bg-amber-400 px-5 py-3 flex items-center justify-between border-b-4 border-amber-500">
                    <h2 class="font-display font-bold text-xl sm:text-2xl text-amber-950 flex items-center gap-2">
                        <span>📖</span> Nota Ringkas: Tujuan Azan & Iqamat
                    </h2>
                    <button onclick="closeNotes()" class="bg-white hover:bg-rose-100 text-rose-600 font-bold w-9 h-9 rounded-full border-2 border-amber-600 flex items-center justify-center text-lg">
                        ✖
                    </button>
                </div>

                <!-- Scrollable Poster Content -->
                <div class="p-4 sm:p-6 overflow-y-auto space-y-4 text-sm sm:text-base">
                    <!-- Grid Tujuan Azan & Iqamat -->
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <!-- Tujuan Azan -->
                        <div class="bg-emerald-50 border-3 border-emerald-500 rounded-2xl p-4 shadow-sm">
                            <div class="inline-block bg-emerald-600 text-white font-display font-bold px-4 py-1 rounded-full text-base mb-2">
                                🕌 Tujuan Azan
                            </div>
                            <p class="text-slate-700 font-semibold mb-2">
                                <strong class="text-emerald-700">Azan</strong> ialah seruan untuk memberitahu umat Islam bahawa <strong>waktu solat telah masuk</strong>.
                            </p>
                            <div class="bg-white border-2 border-emerald-200 rounded-xl p-2.5 text-xs sm:text-sm mb-2">
                                <span class="font-bold text-emerald-700">🗣️ Lafaz Permulaan:</span><br>
                                <em>"Allahu Akbar, Allahu Akbar..."</em>
                            </div>
                            <div class="bg-emerald-100/80 rounded-xl p-2.5 text-xs sm:text-sm text-emerald-950">
                                <span class="font-bold">📌 Contoh:</span> Apabila azan Zuhur berkumandang, kita tahu bahawa waktu solat Zuhur sudah tiba.
                            </div>
                        </div>

                        <!-- Tujuan Iqamat -->
                        <div class="bg-pink-50 border-3 border-pink-500 rounded-2xl p-4 shadow-sm">
                            <div class="inline-block bg-pink-600 text-white font-display font-bold px-4 py-1 rounded-full text-base mb-2">
                                🧎 Tujuan Iqamat
                            </div>
                            <p class="text-slate-700 font-semibold mb-2">
                                <strong class="text-pink-700">Iqamat</strong> pula ialah seruan yang menandakan <strong>solat berjemaah akan dimulakan tidak lama lagi</strong>.
                            </p>
                            <div class="bg-white border-2 border-pink-200 rounded-xl p-2.5 text-xs sm:text-sm mb-2">
                                <span class="font-bold text-pink-700">🗣️ Lafaz Khusus:</span><br>
                                <em>"Qad Qamat as-Solah, Qad Qamat as-Solah..."</em>
                            </div>
                            <div class="bg-pink-100/80 rounded-xl p-2.5 text-xs sm:text-sm text-pink-950">
                                <span class="font-bold">📌 Contoh:</span> Selepas jemaah selesai bersedia di saf, iqamat dilaungkan sebelum imam memulakan solat.
                            </div>
                        </div>
                    </div>

                    <!-- Persamaan -->
                    <div class="bg-amber-50 border-2 border-amber-400 rounded-2xl p-4">
                        <div class="flex items-center gap-2 mb-1">
                            <span class="bg-indigo-800 text-white font-display font-bold px-3 py-0.5 rounded-full text-sm">🤝 Persamaan</span>
                            <span class="text-xs font-bold text-amber-700">Sama-sama untuk solat!</span>
                        </div>
                        <p class="text-slate-700 font-semibold">
                            <strong class="text-emerald-700">Azan</strong> dan <strong class="text-pink-700">iqamat</strong> ialah seruan yang berkaitan dengan solat. Kedua-duanya membantu umat Islam bersedia untuk menunaikan solat.
                        </p>
                    </div>

                    <!-- Perbezaan Penggunaan -->
                    <div class="bg-sky-50 border-2 border-sky-300 rounded-2xl p-4">
                        <div class="inline-block bg-indigo-800 text-white font-display font-bold px-3 py-0.5 rounded-full text-sm mb-3">
                            ⚖️ Perbezaan Penggunaan
                        </div>
                        <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                            <div class="bg-white border-2 border-emerald-300 rounded-xl p-3 flex items-start gap-2.5">
                                <span class="bg-emerald-600 text-white font-bold w-7 h-7 rounded-full flex items-center justify-center shrink-0">1</span>
                                <div>
                                    <p class="font-bold text-emerald-800 text-sm">Azan (📢 Waktu Solat Tiba)</p>
                                    <p class="text-xs text-slate-600 mt-0.5">Dilaungkan apabila <strong>masuk waktu solat</strong> untuk memaklumkan orang ramai.</p>
                                </div>
                            </div>
                            <div class="bg-white border-2 border-pink-300 rounded-xl p-3 flex items-start gap-2.5">
                                <span class="bg-pink-600 text-white font-bold w-7 h-7 rounded-full flex items-center justify-center shrink-0">2</span>
                                <div>
                                    <p class="font-bold text-pink-800 text-sm">Iqamat (🕌 Solat Akan Bermula)</p>
                                    <p class="text-xs text-slate-600 mt-0.5">Dilaungkan <strong>sejurus sebelum solat berjemaah dimulakan</strong>.</p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Footer -->
                <div class="bg-slate-50 px-5 py-3 border-t-2 border-slate-200 flex justify-end">
                    <button onclick="closeNotes()" class="btn-game bg-emerald-500 hover:bg-emerald-400 text-white font-display font-bold px-6 py-2.5 rounded-xl border-2 border-emerald-700">
                        Faham! Jom Main 🚀
                    </button>
                </div>
            </div>
        </section>

        <!-- ==================== LEVEL 1: TANGKAP SERUAN (ARCADE CATCHER) ==================== -->
        <section id="screen-level1" class="hidden w-full max-w-4xl flex-1 flex flex-col bg-white/95 rounded-3xl shadow-2xl border-4 border-emerald-400 overflow-hidden animate-pop">
            <!-- Level 1 Instruction Header -->
            <div class="bg-emerald-500 text-white px-4 py-2.5 flex flex-wrap items-center justify-between gap-2">
                <div>
                    <h2 class="font-display font-bold text-lg sm:text-xl flex items-center gap-2">
                        <span>🎯 Peringkat 1: Tangkap Seruan!</span>
                    </h2>
                    <p class="text-xs sm:text-sm text-emerald-50 font-semibold">
                        Tukar zon Bilal Cilik (<span class="bg-emerald-700 px-1.5 py-0.5 rounded">AZAN</span> atau <span class="bg-pink-600 px-1.5 py-0.5 rounded">IQAMAT</span>) dan tangkap kad yang tepat!
                    </p>
                </div>
                <div class="flex items-center gap-2">
                    <div class="bg-white/20 px-3 py-1 rounded-xl font-display font-bold text-sm">
                        Sasaran: <span id="l1-progress">0</span> / 8
                    </div>
                    <button onclick="togglePause()" class="btn-game bg-amber-400 hover:bg-amber-300 text-amber-950 font-display font-bold px-3 py-1.5 rounded-xl border-2 border-amber-600 text-xs sm:text-sm flex items-center gap-1">
                        <span>⏸️</span> Pause
                    </button>
                </div>
            </div>

            <!-- Interactive Canvas Area -->
            <div id="canvas-wrapper" class="relative w-full h-[360px] sm:h-[410px] bg-gradient-to-b from-sky-100 via-sky-50 to-emerald-50 overflow-hidden">
                <canvas id="arcade-canvas"></canvas>

                <!-- Floating Feedback Toast inside canvas -->
                <div id="l1-toast" class="hidden pointer-events-none absolute top-4 left-1/2 -translate-x-1/2 px-4 py-2 rounded-2xl font-display font-bold text-sm sm:text-base shadow-lg border-2 z-20 whitespace-nowrap">
                </div>
            </div>

            <!-- Bottom Touch/Click Controls for Mobile & Desktop Accessibility -->
            <div class="bg-slate-100 p-3 border-t-2 border-slate-200 flex flex-col sm:flex-row items-center justify-between gap-2">
                <div class="flex gap-2 w-full sm:w-auto justify-center">
                    <button onpointerdown="movePlayer(-1)" class="btn-game bg-white hover:bg-sky-50 text-sky-800 font-bold px-5 py-2.5 rounded-xl border-2 border-sky-300 text-lg flex-1 sm:flex-none">
                        ⬅️ Kiri
                    </button>
                    <button onpointerdown="movePlayer(1)" class="btn-game bg-white hover:bg-sky-50 text-sky-800 font-bold px-5 py-2.5 rounded-xl border-2 border-sky-300 text-lg flex-1 sm:flex-none">
                        Kanan ➡️
                    </button>
                </div>

                <!-- Mode Switcher (Azan vs Iqamat Bucket) -->
                <div class="flex gap-2 w-full sm:w-auto justify-center">
                    <button id="btn-mode-azan" onclick="setCatcherMode('AZAN')" class="btn-game flex-1 sm:flex-none bg-emerald-600 text-white font-display font-bold px-4 py-2.5 rounded-xl border-2 border-emerald-800 text-sm sm:text-base ring-4 ring-emerald-300">
                        🟢 Mod: AZAN
                    </button>
                    <button id="btn-mode-iqamat" onclick="setCatcherMode('IQAMAT')" class="btn-game flex-1 sm:flex-none bg-pink-100 text-pink-800 font-display font-bold px-4 py-2.5 rounded-xl border-2 border-pink-400 text-sm sm:text-base">
                        🌸 Mod: IQAMAT
                    </button>
                </div>
            </div>
        </section>

        <!-- ==================== LEVEL 2: CABARAN SITUASI & PERSAMAAN ==================== -->
        <section id="screen-level2" class="hidden w-full max-w-3xl bg-white/95 rounded-3xl shadow-2xl border-4 border-pink-400 overflow-hidden animate-pop">
            <div class="bg-pink-500 text-white px-5 py-3 flex items-center justify-between">
                <div>
                    <h2 class="font-display font-bold text-lg sm:text-2xl">💡 Peringkat 2: Cabaran Situasi & Persamaan</h2>
                    <p class="text-xs sm:text-sm text-pink-100 font-semibold">Baca situasi dengan teliti dan pilih jawapan yang paling tepat!</p>
                </div>
                <div class="bg-white/20 px-3 py-1 rounded-xl font-display font-bold text-sm">
                    Soalan <span id="l2-qnum">1</span> / 6
                </div>
            </div>

            <div class="p-5 sm:p-7">
                <!-- Visual Illustration Card -->
                <div class="bg-sky-50 border-3 border-sky-200 rounded-2xl p-4 sm:p-5 mb-5 flex flex-col sm:flex-row items-center gap-4">
                    <div id="l2-icon" class="text-5xl sm:text-6xl bg-white w-20 h-20 sm:w-24 sm:h-24 rounded-2xl border-2 border-sky-300 flex items-center justify-center shrink-0 shadow-sm">
                        🕌
                    </div>
                    <div class="text-center sm:text-left">
                        <span id="l2-tag" class="inline-block bg-indigo-100 text-indigo-800 font-bold text-xs px-2.5 py-0.5 rounded-full mb-1">
                            Situasi Masjid
                        </span>
                        <h3 id="l2-question" class="font-display font-bold text-lg sm:text-xl text-slate-800 leading-snug">
                            Soalan akan dipaparkan di sini...
                        </h3>
                    </div>
                </div>

                <!-- Options Grid -->
                <div id="l2-options" class="grid grid-cols-1 sm:grid-cols-2 gap-3 mb-4">
                    <!-- Injected via JS -->
                </div>

                <!-- Educational Instant Feedback Box -->
                <div id="l2-feedback" class="hidden rounded-2xl p-4 border-2 mb-4 animate-pop">
                    <div class="flex items-start gap-3">
                        <span id="l2-fb-icon" class="text-2xl">🌟</span>
                        <div>
                            <h4 id="l2-fb-title" class="font-display font-bold text-base sm:text-lg">Tahniah!</h4>
                            <p id="l2-fb-desc" class="text-xs sm:text-sm font-semibold mt-0.5"></p>
                        </div>
                    </div>
                    <div class="mt-3 flex justify-end">
                        <button onclick="nextLevel2Question()" class="btn-game bg-indigo-600 hover:bg-indigo-500 text-white font-display font-bold px-5 py-2 rounded-xl border-2 border-indigo-800 text-sm sm:text-base">
                            Teruskan ➡️
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- ==================== LEVEL 3: SUSUN URUTAN MENUJU SOLAT (BOSS LEVEL) ==================== -->
        <section id="screen-level3" class="hidden w-full max-w-3xl bg-white/95 rounded-3xl shadow-2xl border-4 border-amber-400 overflow-hidden animate-pop">
            <div class="bg-amber-500 text-amber-950 px-5 py-3 flex items-center justify-between">
                <div>
                    <h2 class="font-display font-bold text-lg sm:text-2xl">🏆 Peringkat 3: Susun Urutan Menuju Solat</h2>
                    <p class="text-xs sm:text-sm text-amber-900 font-bold">Klik langkah-langkah di bawah mengikut urutan 1 hingga 5 yang betul!</p>
                </div>
                <button onclick="resetLevel3()" class="bg-white/80 hover:bg-white text-amber-950 font-bold px-3 py-1.5 rounded-xl border-2 border-amber-700 text-xs sm:text-sm">
                    🔄 Susun Semula
                </button>
            </div>

            <div class="p-4 sm:p-6">
                <!-- Ordered Slots (1 to 5) -->
                <div class="mb-5">
                    <p class="text-xs font-extrabold uppercase tracking-wider text-slate-500 mb-2">Urutan Langkah Anda (Dari Awal Hingga Solat Bermula):</p>
                    <div id="l3-slots" class="space-y-2">
                        <!-- 5 target slots generated by JS -->
                    </div>
                </div>

                <!-- Shuffled Choice Cards -->
                <div class="bg-sky-50 border-2 border-sky-200 rounded-2xl p-3 sm:p-4">
                    <p class="text-xs font-extrabold uppercase tracking-wider text-sky-700 mb-2">Pilih Langkah Seterusnya:</p>
                    <div id="l3-pool" class="grid grid-cols-1 gap-2">
                        <!-- Shuffled buttons generated by JS -->
                    </div>
                </div>

                <!-- Feedback & Check Button -->
                <div id="l3-feedback" class="hidden mt-4 rounded-2xl p-4 border-2 animate-pop"></div>

                <div class="mt-4 flex justify-end">
                    <button id="l3-check-btn" onclick="checkLevel3Order()" disabled class="btn-game disabled:opacity-40 disabled:cursor-not-allowed bg-emerald-500 hover:bg-emerald-400 text-white font-display font-bold text-base sm:text-lg px-6 py-3 rounded-2xl border-2 border-emerald-700">
                        ✅ Semak Urutan Saya!
                    </button>
                </div>
            </div>
        </section>

        <!-- ==================== SCREEN: TRANSITION BETWEEN LEVELS ==================== -->
        <section id="screen-transition" class="hidden w-full max-w-lg bg-white/95 rounded-3xl shadow-2xl border-4 border-emerald-400 p-6 sm:p-8 text-center animate-pop">
            <div id="trans-emoji" class="text-6xl mb-3 animate-bounce">🎉</div>
            <h2 id="trans-title" class="font-display font-bold text-2xl sm:text-3xl text-emerald-700 mb-2">Peringkat 1 Selesai!</h2>
            <p id="trans-desc" class="text-slate-600 font-semibold text-sm sm:text-base mb-5">
                Hebat! Kamu sudah pandai membezakan antara Azan dan Iqamat.
            </p>
            <div class="bg-amber-50 border-2 border-amber-300 rounded-2xl p-3 mb-6">
                <span class="text-xs font-bold text-amber-700 uppercase">Markah Terkumpul</span>
                <div id="trans-score" class="font-display font-bold text-3xl text-amber-900">0</div>
            </div>
            <button id="trans-next-btn" class="btn-game w-full bg-emerald-500 hover:bg-emerald-400 text-white font-display font-bold text-lg sm:text-xl py-3.5 rounded-2xl border-2 border-emerald-700">
                Teruskan ke Peringkat Seterusnya 🚀
            </button>
        </section>

        <!-- ==================== SCREEN: VICTORY / GAME OVER ==================== -->
        <section id="screen-end" class="hidden w-full max-w-xl bg-white/95 rounded-3xl shadow-2xl border-4 border-amber-400 p-6 sm:p-8 text-center animate-pop relative overflow-hidden">
            <div id="end-badge-icon" class="text-6xl sm:text-7xl mb-2 animate-bounce">🏅</div>
            <div id="end-badge-title" class="inline-block bg-amber-400 text-amber-950 font-display font-bold px-4 py-1 rounded-full text-sm sm:text-base border-2 border-amber-600 mb-2">
                Lencana: Bilal Muda Cemerlang
            </div>
            <h2 id="end-heading" class="font-display font-extrabold text-2xl sm:text-4xl text-slate-800 mb-2">
                Tahniah! Misi Berjaya!
            </h2>
            <p id="end-message" class="text-slate-600 font-semibold text-sm sm:text-base mb-5">
                Kamu telah menguasai tujuan, persamaan, dan perbezaan antara Azan dan Iqamat dengan cemerlang!
            </p>

            <!-- Final Stats -->
            <div class="grid grid-cols-2 gap-3 mb-5">
                <div class="bg-emerald-50 border-2 border-emerald-300 rounded-2xl p-3">
                    <span class="text-xs font-bold text-emerald-700 uppercase">Jumlah Markah</span>
                    <div id="end-score" class="font-display font-extrabold text-2xl sm:text-3xl text-emerald-900">0</div>
                </div>
                <div class="bg-sky-50 border-2 border-sky-300 rounded-2xl p-3">
                    <span class="text-xs font-bold text-sky-700 uppercase">Baki Nyawa</span>
                    <div id="end-lives" class="font-display font-extrabold text-2xl sm:text-3xl text-sky-900">3 ❤️</div>
                </div>
            </div>

            <!-- Rumusan Poster Quote -->
            <div class="bg-pink-50 border-2 border-pink-300 rounded-2xl p-3 mb-6 text-xs sm:text-sm text-pink-900 font-bold">
                💖 "Solat Jambatan Kita kepada Allah — Bersedia untuk Solat, Bersedia untuk Kejayaan!"
            </div>

            <div class="flex flex-col sm:flex-row gap-3 justify-center">
                <button onclick="openNotes()" class="btn-game bg-amber-400 hover:bg-amber-300 text-amber-950 font-display font-bold px-5 py-3.5 rounded-2xl border-2 border-amber-600">
                    📖 Semak Nota
                </button>
                <button onclick="startGame()" class="btn-game bg-emerald-500 hover:bg-emerald-400 text-white font-display font-bold px-7 py-3.5 rounded-2xl border-2 border-emerald-700">
                    🔄 Main Semula
                </button>
            </div>
        </section>

    </main>

    <!-- Confetti Canvas Overlay -->
    <canvas id="confetti-canvas" class="fixed inset-0 pointer-events-none z-50 hidden"></canvas>

    <!-- Footer Credit -->
    <footer class="relative z-10 text-center text-xs text-slate-600 font-bold mt-2">
        Ilmu • Amal • Akhlak Mulia | Latihan Pengukuhan Pendidikan Islam Tahun 4
    </footer>

    <script>
        /* =========================================================================
           1. AUDIO ENGINE (WEB AUDIO API - SYNTHETIC CHEERFUL SOUNDS)
           ========================================================================= */
        let soundEnabled = true;
        let audioCtx = null;

        function initAudio() {
            if (!audioCtx) {
                const AudioContext = window.AudioContext || window.webkitAudioContext;
                audioCtx = new AudioContext();
            }
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
        }

        function toggleSound() {
            soundEnabled = !soundEnabled;
            const icon = soundEnabled ? '🔊' : '🔇';
            const text = soundEnabled ? 'Bunyi: Hidup' : 'Bunyi: Senyap';
            document.getElementById('sound-icon-menu').textContent = icon;
            document.getElementById('sound-text-menu').textContent = text;
            document.getElementById('sound-btn-hud').textContent = icon;
            if (soundEnabled) playSound('click');
        }

        function playSound(type) {
            if (!soundEnabled) return;
            try {
                initAudio();
                const now = audioCtx.currentTime;
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain);
                gain.connect(audioCtx.destination);

                if (type === 'click') {
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(520, now);
                    osc.frequency.exponentialRampToValueAtTime(780, now + 0.08);
                    gain.gain.setValueAtTime(0.15, now);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.08);
                    osc.start(now);
                    osc.stop(now + 0.08);
                } else if (type === 'correct') {
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(523.25, now); // C5
                    osc.frequency.setValueAtTime(659.25, now + 0.09); // E5
                    osc.frequency.setValueAtTime(783.99, now + 0.18); // G5
                    gain.gain.setValueAtTime(0.2, now);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.32);
                    osc.start(now);
                    osc.stop(now + 0.32);
                } else if (type === 'wrong') {
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(220, now);
                    osc.frequency.linearRampToValueAtTime(150, now + 0.25);
                    gain.gain.setValueAtTime(0.15, now);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.25);
                    osc.start(now);
                    osc.stop(now + 0.25);
                } else if (type === 'switch') {
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(400, now);
                    osc.frequency.exponentialRampToValueAtTime(600, now + 0.06);
                    gain.gain.setValueAtTime(0.12, now);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.06);
                    osc.start(now);
                    osc.stop(now + 0.06);
                } else if (type === 'win') {
                    const notes = [523.25, 659.25, 783.99, 1046.50];
                    notes.forEach((freq, i) => {
                        const o = audioCtx.createOscillator();
                        const g = audioCtx.createGain();
                        o.type = 'triangle';
                        o.connect(g);
                        g.connect(audioCtx.destination);
                        o.frequency.setValueAtTime(freq, now + i * 0.12);
                        g.gain.setValueAtTime(0.2, now + i * 0.12);
                        g.gain.exponentialRampToValueAtTime(0.01, now + i * 0.12 + 0.35);
                        o.start(now + i * 0.12);
                        o.stop(now + i * 0.12 + 0.35);
                    });
                }
            } catch (e) {
                console.warn('Audio error:', e);
            }
        }

        /* =========================================================================
           2. GAME STATE & NAVIGATION
           ========================================================================= */
        const gameState = {
            level: 1,
            score: 0,
            lives: 3,
            combo: 0,
            isPaused: false
        };

        const screens = ['screen-menu', 'screen-level1', 'screen-level2', 'screen-level3', 'screen-transition', 'screen-end'];

        function showScreen(id) {
            screens.forEach(s => {
                const el = document.getElementById(s);
                if (el) el.classList.add('hidden');
            });
            const target = document.getElementById(id);
            if (target) target.classList.remove('hidden');

            const hud = document.getElementById('game-hud');
            if (id === 'screen-level1' || id === 'screen-level2' || id === 'screen-level3') {
                hud.classList.remove('hidden');
            } else {
                hud.classList.add('hidden');
            }
        }

        function updateHUD() {
            document.getElementById('hud-level').textContent = `${gameState.level} / 3`;
            document.getElementById('hud-score').textContent = gameState.score;

            const livesContainer = document.getElementById('hud-lives');
            livesContainer.innerHTML = '';
            for (let i = 0; i < 3; i++) {
                const span = document.createElement('span');
                span.className = 'text-lg sm:text-xl transition-transform';
                span.textContent = i < gameState.lives ? '❤️' : '🤍';
                livesContainer.appendChild(span);
            }

            const comboEl = document.getElementById('hud-combo');
            if (gameState.combo >= 2) {
                comboEl.textContent = `🔥 Kombo x${gameState.combo}!`;
                comboEl.classList.remove('hidden');
            } else {
                comboEl.classList.add('hidden');
            }
        }

        function openNotes() {
            playSound('click');
            document.getElementById('modal-notes').classList.remove('hidden');
        }

        function closeNotes() {
            playSound('click');
            document.getElementById('modal-notes').classList.add('hidden');
        }

        function returnToMenu() {
            playSound('click');
            gameState.isPaused = false;
            document.getElementById('modal-pause').classList.add('hidden');
            stopLevel1Loop();
            stopConfetti();
            showScreen('screen-menu');
        }

        function togglePause() {
            // Hanya benarkan pause semasa skrin permainan aktif
            const hudHidden = document.getElementById('game-hud').classList.contains('hidden');
            const transHidden = document.getElementById('screen-transition').classList.contains('hidden');
            if (hudHidden || !transHidden) return;

            playSound('click');
            gameState.isPaused = !gameState.isPaused;
            const pauseModal = document.getElementById('modal-pause');

            if (gameState.isPaused) {
                pauseModal.classList.remove('hidden');
                stopLevel1Loop();
            } else {
                pauseModal.classList.add('hidden');
                if (gameState.level === 1 && !document.getElementById('screen-level1').classList.contains('hidden')) {
                    l1Loop();
                }
            }
        }

        function restartFromPause() {
            gameState.isPaused = false;
            document.getElementById('modal-pause').classList.add('hidden');
            startGame();
        }

        function menuFromPause() {
            gameState.isPaused = false;
            document.getElementById('modal-pause').classList.add('hidden');
            returnToMenu();
        }

        function startGame() {
            playSound('click');
            stopConfetti();
            gameState.level = 1;
            gameState.score = 0;
            gameState.lives = 3;
            gameState.combo = 0;
            gameState.isPaused = false;
            document.getElementById('modal-pause').classList.add('hidden');
            updateHUD();
            startLevel1();
        }

        /* =========================================================================
           3. LEVEL 1: TANGKAP SERUAN (INTERACTIVE CANVAS ARCADE)
           ========================================================================= */
        const l1CardsData = [
            { text: "Masuk waktu solat", type: "AZAN", icon: "⏰" },
            { text: "Allahu Akbar, Allahu Akbar...", type: "AZAN", icon: "📢" },
            { text: "Maklumkan orang ramai", type: "AZAN", icon: "🕌" },
            { text: "Waktu Zuhur telah tiba", type: "AZAN", icon: "☀️" },
            { text: "Qad Qamat as-Solah...", type: "IQAMAT", icon: "🗣️" },
            { text: "Jemaah bersedia di saf", type: "IQAMAT", icon: "🧍‍♂️" },
            { text: "Solat akan dimulakan", type: "IQAMAT", icon: "🧎" },
            { text: "Sebelum imam mula solat", type: "IQAMAT", icon: "✨" }
        ];

        let canvas, ctx;
        let l1AnimId = null;
        let fallingCards = [];
        let cardDeck = [];
        let l1CaughtCount = 0;
        const L1_TARGET = 8;
        let spawnTimer = 0;

        const catcher = {
            x: 0.5, // Normalized 0..1
            width: 115,
            height: 54,
            mode: 'AZAN' // 'AZAN' or 'IQAMAT'
        };

        function startLevel1() {
            gameState.level = 1;
            l1CaughtCount = 0;
            fallingCards = [];
            cardDeck = [...l1CardsData].sort(() => Math.random() - 0.5);
            spawnTimer = 25;
            catcher.x = 0.5;
            setCatcherMode('AZAN', false);

            document.getElementById('l1-progress').textContent = '0';
            document.getElementById('l1-toast').classList.add('hidden');
            updateHUD();
            showScreen('screen-level1');

            canvas = document.getElementById('arcade-canvas');
            ctx = canvas.getContext('2d');
            resizeCanvas();
            setupCanvasControls();

            stopLevel1Loop();
            l1Loop();
        }

        function resizeCanvas() {
            const wrapper = document.getElementById('canvas-wrapper');
            if (!wrapper || !canvas) return;
            canvas.width = wrapper.clientWidth;
            canvas.height = wrapper.clientHeight;
            catcher.width = Math.min(135, Math.max(105, canvas.width * 0.21));
        }

        window.addEventListener('resize', () => {
            if (gameState.level === 1) resizeCanvas();
        });

        function setCatcherMode(mode, withSound = true) {
            if (gameState.isPaused) return;
            catcher.mode = mode;
            if (withSound) playSound('switch');

            const btnAzan = document.getElementById('btn-mode-azan');
            const btnIqamat = document.getElementById('btn-mode-iqamat');

            if (mode === 'AZAN') {
                btnAzan.className = "btn-game flex-1 sm:flex-none bg-emerald-600 text-white font-display font-bold px-4 py-2.5 rounded-xl border-2 border-emerald-800 text-sm sm:text-base ring-4 ring-emerald-300";
                btnIqamat.className = "btn-game flex-1 sm:flex-none bg-pink-100 text-pink-800 font-display font-bold px-4 py-2.5 rounded-xl border-2 border-pink-400 text-sm sm:text-base opacity-85";
            } else {
                btnIqamat.className = "btn-game flex-1 sm:flex-none bg-pink-600 text-white font-display font-bold px-4 py-2.5 rounded-xl border-2 border-pink-800 text-sm sm:text-base ring-4 ring-pink-300";
                btnAzan.className = "btn-game flex-1 sm:flex-none bg-emerald-100 text-emerald-800 font-display font-bold px-4 py-2.5 rounded-xl border-2 border-emerald-400 text-sm sm:text-base opacity-85";
            }
        }

        function movePlayer(dir) {
            if (gameState.isPaused) return;
            playSound('click');
            catcher.x = Math.max(0.12, Math.min(0.88, catcher.x + dir * 0.14));
        }

        let controlsAttached = false;
        function setupCanvasControls() {
            if (controlsAttached || !canvas) return;
            controlsAttached = true;

            const updatePosFromClientX = (clientX) => {
                if (gameState.isPaused) return;
                const rect = canvas.getBoundingClientRect();
                const relX = (clientX - rect.left) / rect.width;
                catcher.x = Math.max(0.1, Math.min(0.9, relX));
            };

            canvas.addEventListener('pointermove', (e) => {
                if (gameState.level === 1 && !gameState.isPaused) updatePosFromClientX(e.clientX);
            });

            canvas.addEventListener('pointerdown', (e) => {
                if (gameState.level === 1 && !gameState.isPaused) {
                    updatePosFromClientX(e.clientX);
                }
            });

            // Keyboard support (Arrow keys + Spacebar to switch mode + P/Escape to pause)
            window.addEventListener('keydown', (e) => {
                if (e.key === 'p' || e.key === 'P' || e.key === 'Escape') {
                    const modalNotesHidden = document.getElementById('modal-notes').classList.contains('hidden');
                    if (modalNotesHidden) {
                        e.preventDefault();
                        togglePause();
                    }
                    return;
                }

                if (gameState.level !== 1 || gameState.isPaused) return;
                if (e.key === 'ArrowLeft' || e.key === 'a' || e.key === 'A') {
                    catcher.x = Math.max(0.1, catcher.x - 0.08);
                } else if (e.key === 'ArrowRight' || e.key === 'd' || e.key === 'D') {
                    catcher.x = Math.min(0.9, catcher.x + 0.08);
                } else if (e.key === ' ' || e.key === 'ArrowUp' || e.key === 'ArrowDown') {
                    e.preventDefault();
                    setCatcherMode(catcher.mode === 'AZAN' ? 'IQAMAT' : 'AZAN');
                }
            });
        }

        function spawnFallingCard() {
            if (cardDeck.length === 0) {
                cardDeck = [...l1CardsData].sort(() => Math.random() - 0.5);
            }
            const item = cardDeck.pop();
            const cardW = Math.min(175, Math.max(135, canvas.width * 0.26));
            const cardH = 44;
            const margin = cardW / 2 + 12;
            const randX = margin + Math.random() * (canvas.width - margin * 2);

            fallingCards.push({
                ...item,
                x: randX,
                y: -40,
                w: cardW,
                h: cardH,
                // Kelajuan diperlahankan supaya murid Tahun 4 sempat membaca dengan tenang
                speed: (canvas.height / 360) * (0.55 + (l1CaughtCount * 0.035))
            });
        }

        function showL1Toast(msg, isSuccess) {
            const toast = document.getElementById('l1-toast');
            toast.textContent = msg;
            toast.className = `pointer-events-none absolute top-4 left-1/2 -translate-x-1/2 px-4 py-2 rounded-2xl font-display font-bold text-sm sm:text-base shadow-lg border-2 z-20 whitespace-nowrap animate-pop ${
                isSuccess ? 'bg-emerald-100 text-emerald-900 border-emerald-500' : 'bg-rose-100 text-rose-900 border-rose-500'
            }`;
            toast.classList.remove('hidden');
            clearTimeout(toast._timer);
            toast._timer = setTimeout(() => {
                toast.classList.add('hidden');
            }, 1300);
        }

        function evaluateCatch(card, index) {
            fallingCards.splice(index, 1);
            if (card.type === catcher.mode) {
                // Correct catch!
                playSound('correct');
                gameState.combo++;
                const bonus = gameState.combo >= 2 ? 15 : 10;
                gameState.score += bonus;
                l1CaughtCount++;
                document.getElementById('l1-progress').textContent = l1CaughtCount;
                updateHUD();
                showL1Toast(`✅ Tepat! ${card.type} (+${bonus})`, true);

                if (l1CaughtCount >= L1_TARGET) {
                    stopLevel1Loop();
                    setTimeout(() => {
                        showTransition(
                            '🎯',
                            'Peringkat 1 Berjaya!',
                            'Syabas! Kamu berjaya memadankan semua seruan dan tujuan Azan serta Iqamat.',
                            () => startLevel2()
                        );
                    }, 600);
                }
            } else {
                // Wrong mode!
                playSound('wrong');
                gameState.combo = 0;
                gameState.lives--;
                updateHUD();
                showL1Toast(`❌ Salah Mod! Ini ialah ${card.type}`, false);
                if (gameState.lives <= 0) {
                    stopLevel1Loop();
                    setTimeout(() => endGame(false), 600);
                }
            }
        }

        function drawRoundRect(ctx, x, y, w, h, r) {
            ctx.beginPath();
            ctx.moveTo(x + r, y);
            ctx.lineTo(x + w - r, y);
            ctx.quadraticCurveTo(x + w, y, x + w, y + r);
            ctx.lineTo(x + w, y + h - r);
            ctx.quadraticCurveTo(x + w, y + h, x + w - r, y + h);
            ctx.lineTo(x + r, y + h);
            ctx.quadraticCurveTo(x, y + h, x, y + h - r);
            ctx.lineTo(x, y + r);
            ctx.quadraticCurveTo(x, y, x + r, y);
            ctx.closePath();
        }

        function l1Loop() {
            if (gameState.level !== 1 || gameState.isPaused) return;

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Decorative subtle background clouds & mosque floor
            ctx.fillStyle = '#bbf7d0';
            ctx.fillRect(0, canvas.height - 22, canvas.width, 22);

            // Spawn logic (jarak masa lebih panjang supaya tidak terlalu sesak)
            spawnTimer--;
            if (spawnTimer <= 0 && fallingCards.length < 2) {
                spawnFallingCard();
                spawnTimer = 190;
            }

            // Player Catcher Position
            const px = catcher.x * canvas.width;
            const py = canvas.height - 52;
            const isAzan = catcher.mode === 'AZAN';

            // Update & Draw Falling Cards
            for (let i = fallingCards.length - 1; i >= 0; i--) {
                const c = fallingCards[i];
                c.y += c.speed;

                // Draw Card Shadow & Body
                ctx.save();
                ctx.fillStyle = 'rgba(0,0,0,0.08)';
                drawRoundRect(ctx, c.x - c.w/2, c.y - c.h/2 + 3, c.w, c.h, 12);
                ctx.fill();

                ctx.fillStyle = '#ffffff';
                ctx.strokeStyle = '#0284c7';
                ctx.lineWidth = 2.5;
                drawRoundRect(ctx, c.x - c.w/2, c.y - c.h/2, c.w, c.h, 12);
                ctx.fill();
                ctx.stroke();

                // Icon & Text
                ctx.font = 'bold 16px Nunito, sans-serif';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                ctx.fillStyle = '#1e293b';
                ctx.fillText(c.icon, c.x, c.y - 9);

                ctx.font = 'bold 11.5px Nunito, sans-serif';
                ctx.fillStyle = '#0f172a';
                ctx.fillText(c.text, c.x, c.y + 10);
                ctx.restore();

                // Collision Detection with Catcher
                const hitX = Math.abs(c.x - px) < (c.w / 2 + catcher.width / 2 - 10);
                const hitY = (c.y + c.h / 2 >= py - 24) && (c.y - c.h / 2 <= py + 24);

                if (hitX && hitY) {
                    evaluateCatch(c, i);
                    continue;
                }

                // Missed card (falls off screen)
                if (c.y - c.h / 2 > canvas.height) {
                    fallingCards.splice(i, 1);
                    // Re-queue missed card gently without harsh penalty so Year 4 kids stay encouraged
                    cardDeck.unshift(c);
                }
            }

            // Draw Bilal Cilik Catcher Platform
            ctx.save();
            const bx = px - catcher.width / 2;
            const by = py - 20;

            // Glow ring
            ctx.fillStyle = isAzan ? '#16a34a' : '#db2777';
            ctx.strokeStyle = isAzan ? '#14532d' : '#831843';
            ctx.lineWidth = 4;
            drawRoundRect(ctx, bx, by, catcher.width, 46, 20);
            ctx.fill();
            ctx.stroke();

            // Cute Bilal Head Badge above catcher
            ctx.fillStyle = '#fde68a';
            ctx.beginPath();
            ctx.arc(px, by - 10, 16, 0, Math.PI * 2);
            ctx.fill();
            ctx.strokeStyle = '#92400e';
            ctx.lineWidth = 2;
            ctx.stroke();

            // White Kopiah
            ctx.fillStyle = '#ffffff';
            ctx.beginPath();
            ctx.arc(px, by - 15, 14, Math.PI, 0);
            ctx.fill();
            ctx.stroke();

            // Hands up near ears (Bilal gesture from poster!)
            ctx.fillStyle = '#fde68a';
            ctx.beginPath();
            ctx.arc(px - 18, by - 9, 5, 0, Math.PI * 2);
            ctx.arc(px + 18, by - 9, 5, 0, Math.PI * 2);
            ctx.fill();

            // Mode Label on Catcher
            ctx.fillStyle = '#ffffff';
            ctx.font = 'bold 16px Fredoka, sans-serif';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.fillText(isAzan ? '📢 AZAN' : '🧎 IQAMAT', px, by + 23);
            ctx.restore();

            l1AnimId = requestAnimationFrame(l1Loop);
        }

        function stopLevel1Loop() {
            if (l1AnimId) {
                cancelAnimationFrame(l1AnimId);
                l1AnimId = null;
            }
        }

        /* =========================================================================
           4. LEVEL 2: CABARAN SITUASI & PERSAMAAN (INTERACTIVE QUIZ)
           ========================================================================= */
        const level2Questions = [
            {
                icon: "☀️",
                tag: "Contoh Situasi",
                question: "Jam menunjukkan masuk waktu Zuhur. Bilal melaungkan 'Allahu Akbar, Allahu Akbar...'. Apakah tujuan seruan ini?",
                options: [
                    "Memberitahu umat Islam bahawa waktu solat telah masuk",
                    "Menandakan imam sudah memberi salam",
                    "Meminta jemaah pulang ke rumah",
                    "Menandakan solat berjemaah telah tamat"
                ],
                correct: 0,
                explanation: "Tepat sekali! Tujuan Azan ialah memberitahu umat Islam bahawa waktu solat telah masuk."
            },
            {
                icon: "🧎",
                tag: "Tujuan Iqamat",
                question: "Selepas jemaah selesai meluruskan saf di surau, bilal melaungkan 'Qad Qamat as-Solah...'. Mengapakah iqamat dilaungkan?",
                options: [
                    "Untuk memberitahu waktu Subuh sudah tamat",
                    "Menandakan solat berjemaah akan dimulakan tidak lama lagi",
                    "Untuk memanggil orang ramai datang dari jauh",
                    "Untuk memulakan bacaan khutbah Jumaat"
                ],
                correct: 1,
                explanation: "Betul! Iqamat dilaungkan sejurus sebelum solat berjemaah dimulakan."
            },
            {
                icon: "🤝",
                tag: "Persamaan Azan & Iqamat",
                question: "Apakah PERSAMAAN antara Azan dan Iqamat?",
                options: [
                    "Kedua-duanya dilaungkan selepas selesai solat",
                    "Kedua-duanya ialah seruan yang berkaitan dengan solat dan membantu bersedia untuk solat",
                    "Kedua-duanya mempunyai lafaz yang sama panjang",
                    "Kedua-duanya hanya dilaungkan pada hari Jumaat"
                ],
                correct: 1,
                explanation: "Cemerlang! Azan dan iqamat sama-sama seruan berkaitan solat yang membantu umat Islam bersedia menunaikan solat."
            },
            {
                icon: "📢",
                tag: "Perbezaan Penggunaan",
                question: "Bagaimanakah cara membezakan masa penggunaan Azan dan Iqamat?",
                options: [
                    "Azan ketika masuk waktu solat; Iqamat sejurus sebelum solat berjemaah bermula",
                    "Azan untuk solat sunat; Iqamat untuk solat fardu",
                    "Azan dilaungkan oleh imam; Iqamat dilaungkan oleh makmum",
                    "Azan di dalam saf; Iqamat di luar masjid"
                ],
                correct: 0,
                explanation: "Syabas! Azan memaklumkan masuk waktu solat kepada orang ramai, manakala iqamat menandakan solat akan bermula."
            },
            {
                icon: "🕌",
                tag: "Amalan Mulia Muslim",
                question: "Ahmad mendengar laungan Iqamat ketika berada di masjid. Apakah tindakan paling sesuai yang perlu Ahmad lakukan?",
                options: [
                    "Berbual-bual dengan kawan di belakang",
                    "Segera berdiri dan meluruskan saf untuk memulakan solat berjemaah",
                    "Keluar dari masjid untuk bermain",
                    "Menunggu sehingga solat selesai"
                ],
                correct: 1,
                explanation: "Bagus! Iqamat menandakan solat akan bermula tidak lama lagi, jadi kita mesti bersedia dalam saf."
            },
            {
                icon: "💖",
                tag: "Rumusan Pembelajaran",
                question: "Mengapakah Azan dan Iqamat dianggap amalan mulia setiap Muslim?",
                options: [
                    "Kerana ia membantu umat Islam bersedia menuju solat yang menjadi jambatan kepada Allah",
                    "Kerana ia menguji kekuatan suara bilal sahaja",
                    "Kerana ia menggantikan solat fardu",
                    "Kerana ia dilakukan sekali setahun sahaja"
                ],
                correct: 0,
                explanation: "Hebat! Solat ialah jambatan kita kepada Allah. Bersedia untuk solat, bersedia untuk kejayaan!"
            }
        ];

        let l2Index = 0;
        let l2Answered = false;

        function startLevel2() {
            gameState.level = 2;
            l2Index = 0;
            updateHUD();
            showScreen('screen-level2');
            renderLevel2Question();
        }

        function renderLevel2Question() {
            l2Answered = false;
            const q = level2Questions[l2Index];
            document.getElementById('l2-qnum').textContent = `${l2Index + 1}`;
            document.getElementById('l2-icon').textContent = q.icon;
            document.getElementById('l2-tag').textContent = q.tag;
            document.getElementById('l2-question').textContent = q.question;
            document.getElementById('l2-feedback').classList.add('hidden');

            const optsContainer = document.getElementById('l2-options');
            optsContainer.innerHTML = '';

            // Shuffle option display while tracking original index
            const indexedOptions = q.options.map((text, idx) => ({ text, idx }));
            indexedOptions.sort(() => Math.random() - 0.5);

            const letters = ['A', 'B', 'C', 'D'];
            indexedOptions.forEach((opt, displayIdx) => {
                const btn = document.createElement('button');
                btn.className = "btn-game text-left bg-white hover:bg-sky-50 border-3 border-sky-300 rounded-2xl p-3.5 sm:p-4 font-bold text-slate-800 text-sm sm:text-base flex items-start gap-3 transition";
                btn.innerHTML = `
                    <span class="bg-sky-500 text-white font-display font-bold w-7 h-7 rounded-lg flex items-center justify-center shrink-0 mt-0.5">${letters[displayIdx]}</span>
                    <span>${opt.text}</span>
                `;
                btn.onclick = () => selectLevel2Option(opt.idx, btn, indexedOptions);
                optsContainer.appendChild(btn);
            });
        }

        function selectLevel2Option(selectedIdx, clickedBtn, indexedOptions) {
            if (l2Answered || gameState.isPaused) return;
            l2Answered = true;

            const q = level2Questions[l2Index];
            const isCorrect = selectedIdx === q.correct;
            const allBtns = document.getElementById('l2-options').querySelectorAll('button');

            allBtns.forEach((b, i) => {
                b.disabled = true;
                const origIdx = indexedOptions[i].idx;
                if (origIdx === q.correct) {
                    b.className = "text-left bg-emerald-100 border-3 border-emerald-600 rounded-2xl p-3.5 sm:p-4 font-bold text-emerald-950 text-sm sm:text-base flex items-start gap-3 ring-4 ring-emerald-200";
                } else if (b === clickedBtn && !isCorrect) {
                    b.className = "animate-shake text-left bg-rose-100 border-3 border-rose-500 rounded-2xl p-3.5 sm:p-4 font-bold text-rose-950 text-sm sm:text-base flex items-start gap-3";
                } else {
                    b.classList.add('opacity-50');
                }
            });

            const fbBox = document.getElementById('l2-feedback');
            const fbIcon = document.getElementById('l2-fb-icon');
            const fbTitle = document.getElementById('l2-fb-title');
            const fbDesc = document.getElementById('l2-fb-desc');

            if (isCorrect) {
                playSound('correct');
                gameState.combo++;
                const pts = gameState.combo >= 2 ? 25 : 20;
                gameState.score += pts;
                fbBox.className = "rounded-2xl p-4 border-2 mb-4 animate-pop bg-emerald-50 border-emerald-400 text-emerald-900";
                fbIcon.textContent = "🌟";
                fbTitle.textContent = `Jawapan Tepat! (+${pts} Markah)`;
                fbDesc.textContent = q.explanation;
            } else {
                playSound('wrong');
                gameState.combo = 0;
                gameState.lives--;
                fbBox.className = "rounded-2xl p-4 border-2 mb-4 animate-pop bg-rose-50 border-rose-400 text-rose-900";
                fbIcon.textContent = "💡";
                fbTitle.textContent = "Cuba Lagi! Mari Semak Jawapan:";
                fbDesc.textContent = q.explanation;
            }

            updateHUD();
            fbBox.classList.remove('hidden');
        }

        function nextLevel2Question() {
            playSound('click');
            if (gameState.lives <= 0) {
                endGame(false);
                return;
            }
            l2Index++;
            if (l2Index < level2Questions.length) {
                renderLevel2Question();
            } else {
                showTransition(
                    '💡',
                    'Peringkat 2 Selesai!',
                    'Luar biasa! Sekarang mari hadapi cabaran terakhir: Susun Urutan Menuju Solat!',
                    () => startLevel3()
                );
            }
        }

        /* =========================================================================
           5. LEVEL 3: SUSUN URUTAN MENUJU SOLAT (BOSS LEVEL)
           ========================================================================= */
        const level3Steps = [
            { id: 1, icon: "⏰", text: "Waktu solat fardu telah masuk." },
            { id: 2, icon: "📢", text: "Bilal melaungkan Azan untuk memaklumkan orang ramai." },
            { id: 3, icon: "🕌", text: "Umat Islam mengambil wuduk dan bersedia di dalam saf." },
            { id: 4, icon: "🗣️", text: "Bilal melaungkan Iqamat sejurus sebelum solat bermula." },
            { id: 5, icon: "🤲", text: "Imam mengangkat takbiratul ihram dan solat berjemaah bermula." }
        ];

        let l3Selected = [];
        let l3Shuffled = [];

        function startLevel3() {
            gameState.level = 3;
            updateHUD();
            showScreen('screen-level3');
            resetLevel3(false);
        }

        function resetLevel3(withSound = true) {
            if (withSound) playSound('click');
            l3Selected = [];
            l3Shuffled = [...level3Steps].sort(() => Math.random() - 0.5);
            document.getElementById('l3-feedback').classList.add('hidden');
            renderLevel3Board();
        }

        function renderLevel3Board() {
            const slotsEl = document.getElementById('l3-slots');
            const poolEl = document.getElementById('l3-pool');
            const checkBtn = document.getElementById('l3-check-btn');

            slotsEl.innerHTML = '';
            for (let i = 0; i < 5; i++) {
                const item = l3Selected[i];
                const row = document.createElement('div');
                if (item) {
                    row.className = "bg-emerald-50 border-2 border-emerald-400 rounded-xl px-3.5 py-2.5 flex items-center justify-between gap-2 animate-pop";
                    row.innerHTML = `
                        <div class="flex items-center gap-2.5 font-bold text-sm sm:text-base text-emerald-950">
                            <span class="bg-emerald-600 text-white font-display w-7 h-7 rounded-full flex items-center justify-center shrink-0">${i + 1}</span>
                            <span>${item.icon} ${item.text}</span>
                        </div>
                        <button onclick="removeLevel3Item(${i})" class="text-xs font-bold bg-white hover:bg-rose-100 text-rose-600 px-2.5 py-1 rounded-lg border border-rose-300">
                            Batal
                        </button>
                    `;
                } else {
                    row.className = "bg-slate-50 border-2 border-dashed border-slate-300 rounded-xl px-3.5 py-2.5 flex items-center gap-2.5 text-slate-400 text-sm font-bold";
                    row.innerHTML = `
                        <span class="bg-slate-200 text-slate-500 font-display w-7 h-7 rounded-full flex items-center justify-center shrink-0">${i + 1}</span>
                        <span>Klik kad langkah di bawah untuk isi tempat ke-${i + 1}...</span>
                    `;
                }
                slotsEl.appendChild(row);
            }

            poolEl.innerHTML = '';
            l3Shuffled.forEach((step) => {
                const isUsed = l3Selected.some(s => s.id === step.id);
                if (!isUsed) {
                    const btn = document.createElement('button');
                    btn.className = "btn-game text-left bg-white hover:bg-amber-50 border-2 border-sky-300 rounded-xl px-4 py-2.5 font-bold text-sm sm:text-base text-slate-800 flex items-center gap-2.5";
                    btn.innerHTML = `<span class="text-xl">${step.icon}</span> <span>${step.text}</span>`;
                    btn.onclick = () => pickLevel3Item(step);
                    poolEl.appendChild(btn);
                }
            });

            checkBtn.disabled = l3Selected.length !== 5;
        }

        function pickLevel3Item(step) {
            if (l3Selected.length >= 5 || gameState.isPaused) return;
            playSound('switch');
            l3Selected.push(step);
            document.getElementById('l3-feedback').classList.add('hidden');
            renderLevel3Board();
        }

        function removeLevel3Item(index) {
            if (gameState.isPaused) return;
            playSound('click');
            l3Selected.splice(index, 1);
            document.getElementById('l3-feedback').classList.add('hidden');
            renderLevel3Board();
        }

        function checkLevel3Order() {
            if (gameState.isPaused) return;
            const isAllCorrect = l3Selected.every((item, idx) => item.id === idx + 1);
            const fb = document.getElementById('l3-feedback');

            if (isAllCorrect) {
                playSound('win');
                gameState.score += 50;
                updateHUD();
                fb.className = "mt-4 rounded-2xl p-4 border-2 bg-emerald-100 border-emerald-500 text-emerald-950 font-bold text-sm sm:text-base animate-pop";
                fb.innerHTML = "🎉 <strong>Sempurna!</strong> Kamu telah menyusun urutan Azan hingga Iqamat dan Solat Berjemaah dengan tepat! (+50 Markah)";
                fb.classList.remove('hidden');
                document.getElementById('l3-check-btn').disabled = true;

                setTimeout(() => {
                    endGame(true);
                }, 1400);
            } else {
                playSound('wrong');
                gameState.lives--;
                gameState.combo = 0;
                updateHUD();
                fb.className = "mt-4 rounded-2xl p-4 border-2 bg-rose-100 border-rose-500 text-rose-950 font-bold text-sm sm:text-base animate-shake";
                fb.innerHTML = "❌ <strong>Urutan belum tepat!</strong> Ingat: Masuk waktu ➔ Azan ➔ Bersedia di saf ➔ Iqamat ➔ Solat bermula. Cuba susun semula!";
                fb.classList.remove('hidden');

                if (gameState.lives <= 0) {
                    setTimeout(() => endGame(false), 1200);
                }
            }
        }

        /* =========================================================================
           6. TRANSITIONS, VICTORY & CONFETTI CELEBRATION
           ========================================================================= */
        function showTransition(emoji, title, desc, onNext) {
            playSound('win');
            document.getElementById('trans-emoji').textContent = emoji;
            document.getElementById('trans-title').textContent = title;
            document.getElementById('trans-desc').textContent = desc;
            document.getElementById('trans-score').textContent = gameState.score;

            const btn = document.getElementById('trans-next-btn');
            btn.onclick = () => {
                playSound('click');
                onNext();
            };

            showScreen('screen-transition');
        }

        function endGame(isVictory) {
            stopLevel1Loop();
            showScreen('screen-end');

            const badgeIcon = document.getElementById('end-badge-icon');
            const badgeTitle = document.getElementById('end-badge-title');
            const heading = document.getElementById('end-heading');
            const message = document.getElementById('end-message');

            document.getElementById('end-score').textContent = gameState.score;
            document.getElementById('end-lives').textContent = `${Math.max(0, gameState.lives)} ❤️`;

            if (isVictory) {
                playSound('win');
                startConfetti();
                badgeIcon.textContent = "🏆";
                badgeTitle.textContent = gameState.score >= 220 ? "Lencana: Juara Bilal Cilik 🌟" : "Lencana: Sahabat Masjid Cemerlang 🕌";
                heading.textContent = "Tahniah! Misi Berjaya!";
                heading.className = "font-display font-extrabold text-2xl sm:text-4xl text-emerald-700 mb-2";
                message.textContent = "Alhamdulillah! Kamu telah menguasai tujuan Azan dan Iqamat, persamaan, serta perbezaan penggunaannya dengan sangat baik!";
            } else {
                playSound('wrong');
                badgeIcon.textContent = "📖";
                badgeTitle.textContent = "Semangat Usaha Lagi!";
                heading.textContent = "Nyawa Telah Habis!";
                heading.className = "font-display font-extrabold text-2xl sm:text-4xl text-rose-600 mb-2";
                message.textContent = "Jangan putus asa! Jom baca Nota Ringkas sebentar dan cuba lagi untuk mendapat markah penuh.";
            }
        }

        // Simple Lightweight Confetti Celebration
        let confettiAnim = null;
        function startConfetti() {
            const cCanvas = document.getElementById('confetti-canvas');
            cCanvas.classList.remove('hidden');
            cCanvas.width = window.innerWidth;
            cCanvas.height = window.innerHeight;
            const cCtx = cCanvas.getContext('2d');

            const colors = ['#22c55e', '#ec4899', '#eab308', '#3b82f6', '#f97316'];
            const pieces = Array.from({ length: 85 }, () => ({
                x: Math.random() * cCanvas.width,
                y: Math.random() * cCanvas.height - cCanvas.height,
                r: Math.random() * 7 + 4,
                color: colors[Math.floor(Math.random() * colors.length)],
                vy: Math.random() * 3 + 2,
                vx: Math.random() * 2 - 1,
                rot: Math.random() * 360
            }));

            function renderConfetti() {
                cCtx.clearRect(0, 0, cCanvas.width, cCanvas.height);
                pieces.forEach(p => {
                    p.y += p.vy;
                    p.x += p.vx;
                    p.rot += 4;
                    if (p.y > cCanvas.height) p.y = -10;

                    cCtx.save();
                    cCtx.translate(p.x, p.y);
                    cCtx.rotate((p.rot * Math.PI) / 180);
                    cCtx.fillStyle = p.color;
                    cCtx.fillRect(-p.r / 2, -p.r / 2, p.r, p.r * 0.6);
                    cCtx.restore();
                });
                confettiAnim = requestAnimationFrame(renderConfetti);
            }
            renderConfetti();
        }

        function stopConfetti() {
            if (confettiAnim) {
                cancelAnimationFrame(confettiAnim);
                confettiAnim = null;
            }
            const cCanvas = document.getElementById('confetti-canvas');
            if (cCanvas) cCanvas.classList.add('hidden');
        }
    </script>
</body>
</html>
