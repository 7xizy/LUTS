<!DOCTYPE html>
<html lang="zh-TW" style="overflow-y: scroll; overflow-x: hidden; height: auto; min-height: 100%;">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cinematic LUT Studio Ultra</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;700&family=Plus+Jakarta+Sans:wght@300;400;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Plus Jakarta Sans', 'sans-serif'],
                        mono: ['JetBrains Mono', 'monospace'],
                    }
                }
            }
        }
    </script>
    <style>
        html, body {
            margin: 0;
            padding: 0;
            width: 100%;
            height: auto !important;
            min-height: 100vh;
            overflow-x: hidden;
            overflow-y: auto !important;
        }
        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
        }
        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #0f1115; }
        ::-webkit-scrollbar-thumb { background: #3f4456; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #5b627a; }
        .no-select { user-select: none; -webkit-user-select: none; }
        
        /* 進度條發光動畫 */
        @keyframes pulse-glow {
            0%, 100% { box-shadow: 0 0 15px rgba(6, 182, 212, 0.4); }
            50% { box-shadow: 0 0 25px rgba(6, 182, 212, 0.8); }
        }
        .progress-glow { animation: pulse-glow 2s infinite; }
    </style>
</head>
<body class="bg-[#08090c] text-gray-100 flex flex-col">

    <header class="bg-[#0d0f14]/95 backdrop-blur-md border-b border-[#1b1e25] px-6 py-2.5 flex items-center justify-between shrink-0 h-14 sticky top-0 z-50 shadow-md">
        <div class="flex items-center gap-3">
            <div class="w-7 h-7 rounded-lg bg-gradient-to-tr from-cyan-500 to-indigo-500 flex items-center justify-center shadow-lg shadow-cyan-500/10">
                <i class="fa-solid fa-play text-xs text-white pl-0.5"></i>
            </div>
            <div>
                <h1 class="font-bold text-sm tracking-wide bg-gradient-to-r from-white to-gray-400 bg-clip-text text-transparent">LUT STUDIO ULTRA</h1>
                <p class="text-[9px] text-gray-500 font-mono tracking-tighter">S-LOG3 REAL-TIME GRADING MONITOR</p>
            </div>
        </div>
        
        <div class="flex items-center gap-2 hidden sm:flex">
            <span class="text-[10px] font-mono text-gray-500 mr-1">PRESETS:</span>
            <div class="flex items-center gap-1 bg-[#13161c] border border-[#222733] rounded-md p-0.5">
                <button onclick="applyPreset('teal_orange')" class="px-2.5 py-1 text-[11px] font-semibold rounded hover:bg-gray-800 transition-colors">Teal & Orange</button>
                <button onclick="applyPreset('bleach_bypass')" class="px-2.5 py-1 text-[11px] font-semibold rounded hover:bg-gray-800 transition-colors">電影旁路</button>
                <button onclick="applyPreset('noir')" class="px-2.5 py-1 text-[11px] font-semibold rounded hover:bg-gray-800 transition-colors">黑白膠卷</button>
                <button onclick="applyPreset('cyberpunk')" class="px-2.5 py-1 text-[11px] font-semibold rounded hover:bg-gray-800 transition-colors">賽博朋克</button>
            </div>
        </div>
    </header>

    <main class="w-full flex flex-col lg:flex-row flex-grow">
        
        <aside class="w-full lg:w-72 bg-[#0d0f14] border-b lg:border-b-0 lg:border-r border-[#1b1e25] flex flex-col p-5 shrink-0 gap-6 z-10 h-auto lg:min-h-[calc(100vh-56px)]">
            <div class="flex flex-col gap-3">
                <div class="flex items-center gap-2 text-cyan-400 font-bold text-xs uppercase tracking-wider">
                    <i class="fa-solid fa-camera"></i>
                    <span>1. 智能畫面生成</span>
                </div>
                <div class="p-4 bg-[#13161c] border border-[#222733] rounded-xl flex flex-col gap-3 relative">
                    <input id="promptInput" type="text" value="sunset wave" placeholder="輸入關鍵字: neon, forest, space..." class="w-full bg-[#090a0d] border border-[#222733] px-3 py-2 text-xs rounded-lg text-white focus:outline-none focus:border-cyan-500 font-medium transition-colors">
                    <button id="btnGenerate" onclick="generateFromPrompt()" class="w-full py-2.5 bg-gradient-to-r from-cyan-500 to-cyan-600 hover:from-cyan-400 hover:to-cyan-500 text-slate-950 font-bold text-xs rounded-lg transition-all flex items-center justify-center gap-1.5 shadow-lg shadow-cyan-500/10">
                        <i class="fa-solid fa-wand-magic-sparkles"></i>
                        <span id="btnGenerateText">生成寫實模擬畫面</span>
                    </button>
                </div>
            </div>

            <div class="flex flex-col gap-3">
                <div class="flex items-center gap-2 text-indigo-400 font-bold text-xs uppercase tracking-wider">
                    <i class="fa-solid fa-palette"></i>
                    <span>2. 環境底色</span>
                </div>
                <div class="p-4 bg-[#13161c] border border-[#222733] rounded-xl flex flex-col gap-3">
                    <div class="grid grid-cols-4 gap-2">
                        <button onclick="setWorkspaceBg('#08090c')" class="h-8 rounded bg-[#08090c] border border-gray-800 hover:border-gray-500 transition-all"></button>
                        <button onclick="setWorkspaceBg('#1b1e25')" class="h-8 rounded bg-[#1b1e25] border border-gray-700 hover:border-gray-400 transition-all"></button>
                        <button onclick="setWorkspaceBg('#141d26')" class="h-8 rounded bg-[#141d26] border border-gray-700 hover:border-gray-400 transition-all"></button>
                        <button onclick="setWorkspaceBg('#dfdad2')" class="h-8 rounded bg-[#dfdad2] border border-gray-400 hover:border-gray-100 transition-all"></button>
                    </div>
                    <div class="flex items-center justify-between mt-1 pt-2 border-t border-[#222733]">
                        <input type="color" id="bgCustomColor" oninput="setWorkspaceBg(this.value)" class="w-6 h-6 border border-gray-700 rounded cursor-pointer bg-transparent">
                        <span id="bgHexLabel" class="text-xs font-mono text-gray-500">#08090C</span>
                    </div>
                </div>
            </div>

            <div class="p-4 bg-[#13161c]/50 border border-[#222733]/50 rounded-xl text-xs text-gray-500 leading-relaxed flex flex-col gap-2 lg:mt-auto">
                <p><strong class="text-amber-500">Sony S-Log3 Workflow</strong></p>
                <p>完美複製原生 10-bit S-Log3 低反差低飽和狀態，透過非同步引擎實現即時 3D 矩陣解調。</p>
            </div>
        </aside>

        <section id="workspaceArea" class="flex-grow flex flex-col items-center justify-center p-4 sm:p-8 transition-colors duration-500 relative no-select min-h-[60vh] lg:min-h-[850px] bg-[#1b1e25]">
            
            <div class="mb-6 flex bg-[#0d0f14]/90 backdrop-blur-md p-1.5 rounded-xl border border-[#222733]/80 shadow-2xl z-20">
                <button onclick="setCompareMode('split')" id="modeSplit" class="px-5 py-2 text-xs font-bold rounded-lg transition-all flex items-center gap-2 text-cyan-400 bg-cyan-500/10 border border-cyan-500/20">
                    <i class="fa-solid fa-columns"></i><span>左右對比</span>
                </button>
                <button onclick="setCompareMode('graded')" id="modeGraded" class="px-5 py-2 text-xs font-bold rounded-lg transition-all flex items-center gap-2 text-gray-400 hover:text-gray-200 border border-transparent">
                    <i class="fa-solid fa-film"></i><span>套用 LUT</span>
                </button>
                <button onclick="setCompareMode('original')" id="modeOriginal" class="px-5 py-2 text-xs font-bold rounded-lg transition-all flex items-center gap-2 text-gray-400 hover:text-gray-200 border border-transparent">
                    <i class="fa-solid fa-video-slash"></i><span>原始 S-Log3</span>
                </button>
            </div>

            <div class="relative w-full max-w-[1600px] aspect-video flex items-center justify-center shadow-[0_35px_80px_-15px_rgba(0,0,0,0.95)]">
                <div class="relative w-full h-full rounded-2xl overflow-hidden border border-black/60 bg-black flex items-center justify-center" id="canvasContainer">
                    
                    <canvas id="previewCanvas" class="w-full h-full object-contain pointer-events-none transition-opacity duration-700 opacity-100"></canvas>
                    
                    <div id="splitBar" class="absolute top-0 bottom-0 w-[3px] bg-white shadow-[0_0_15px_rgba(255,255,255,0.7)] cursor-ew-resize flex items-center justify-center z-10 transition-transform duration-75" style="left: 50%;">
                        <div class="w-9 h-9 rounded-full bg-white text-slate-950 flex items-center justify-center shadow-2xl border-2 border-gray-200 transform scale-90 opacity-90 hover:opacity-100 transition-opacity cursor-ew-resize">
                            <i class="fa-solid fa-arrows-left-right text-xs"></i>
                        </div>
                    </div>
                    
                    <div class="absolute bottom-6 left-6 bg-black/70 backdrop-blur-md px-3 py-1.5 rounded-md text-xs font-mono text-gray-300 border border-white/10 pointer-events-none tracking-widest hidden sm:block">
                        INPUT: S-LOG3 / SGAMUT3.CINE
                    </div>
                    <div id="activePromptLabel" class="absolute bottom-6 right-6 bg-black/70 backdrop-blur-md px-3 py-1.5 rounded-md text-xs font-mono text-cyan-400 border border-white/10 pointer-events-none uppercase tracking-wider hidden sm:block">
                        PROMPT: SUNSET WAVE
                    </div>

                    <div id="loadingOverlay" class="absolute inset-0 bg-[#08090c]/90 backdrop-blur-sm z-30 flex flex-col items-center justify-center opacity-0 pointer-events-none transition-opacity duration-500">
                        <div class="flex flex-col items-center w-full max-w-md px-8">
                            <i class="fa-solid fa-circle-notch fa-spin text-cyan-400 text-3xl mb-5"></i>
                            <div class="w-full h-1.5 bg-gray-800 rounded-full overflow-hidden mb-3 border border-gray-700 shadow-inner">
                                <div id="progressBar" class="h-full bg-gradient-to-r from-cyan-400 to-indigo-500 w-0 transition-all duration-300 progress-glow rounded-full"></div>
                            </div>
                            <div class="flex justify-between w-full text-xs font-mono tracking-widest">
                                <span id="progressText" class="text-gray-400">PROCESSING ENGINE...</span>
                                <span id="progressPercent" class="text-cyan-400 font-bold">0%</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <aside class="w-full lg:w-80 bg-[#0d0f14] border-t lg:border-t-0 lg:border-l border-[#1b1e25] flex flex-col shrink-0 h-auto lg:min-h-[calc(1
