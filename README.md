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

        <aside class="w-full lg:w-80 bg-[#0d0f14] border-t lg:border-t-0 lg:border-l border-[#1b1e25] flex flex-col shrink-0 h-auto lg:min-h-[calc(100vh-56px)]">
            <div class="p-5 border-b border-[#1b1e25] flex items-center justify-between bg-[#0a0b0e] shrink-0">
                <div class="flex items-center gap-2 text-cyan-400 font-bold text-xs tracking-wider uppercase">
                    <i class="fa-solid fa-sliders"></i>
                    <span>調色大師控制台</span>
                </div>
                <button onclick="resetSliders()" class="text-xs text-gray-500 hover:text-white transition-colors flex items-center gap-1 font-medium">
                    <i class="fa-solid fa-rotate-left"></i><span>重置參數</span>
                </button>
            </div>

            <div class="p-5 flex flex-col gap-6 bg-[#0d0f14] flex-grow" id="controlsContainer">
                
                <div class="flex flex-col gap-4">
                    <h4 class="text-[11px] font-bold text-gray-500 uppercase tracking-widest font-mono">1. Tone & Exposure</h4>
                    
                    <div class="flex flex-col gap-1.5">
                        <div class="flex justify-between text-xs px-0.5">
                            <span class="text-gray-400">曝光度 (Exposure)</span>
                            <span id="label-exposure" class="font-mono text-cyan-400 font-bold text-xs">0.00</span>
                        </div>
                        <input type="range" id="slider-exposure" min="-100" max="100" value="0" class="w-full accent-cyan-500 h-1.5 rounded-full bg-[#1f232d] outline-none cursor-pointer">
                    </div>

                    <div class="flex flex-col gap-1.5">
                        <div class="flex justify-between text-xs px-0.5">
                            <span class="text-gray-400">對比度 (Contrast)</span>
                            <span id="label-contrast" class="font-mono text-cyan-400 font-bold text-xs">1.00</span>
                        </div>
                        <input type="range" id="slider-contrast" min="50" max="200" value="100" class="w-full accent-cyan-500 h-1.5 rounded-full bg-[#1f232d] outline-none cursor-pointer">
                    </div>

                    <div class="flex flex-col gap-1.5">
                        <div class="flex justify-between text-xs px-0.5">
                            <span class="text-gray-400">飽和度 (Saturation)</span>
                            <span id="label-saturation" class="font-mono text-cyan-400 font-bold text-xs">1.00</span>
                        </div>
                        <input type="range" id="slider-saturation" min="0" max="200" value="100" class="w-full accent-cyan-500 h-1.5 rounded-full bg-[#1f232d] outline-none cursor-pointer">
                    </div>

                    <div class="flex flex-col gap-1.5">
                        <div class="flex justify-between text-xs px-0.5">
                            <span class="text-gray-400">電影色溫 (Kelvin)</span>
                            <span id="label-temp" class="font-mono text-cyan-400 font-bold text-xs">0</span>
                        </div>
                        <input type="range" id="slider-temp" min="-50" max="50" value="0" class="w-full accent-cyan-500 h-1.5 rounded-full bg-[#1f232d] outline-none cursor-pointer">
                    </div>
                </div>

                <div class="flex flex-col gap-4 pt-5 border-t border-[#1b1e25]">
                    <h4 class="text-[11px] font-bold text-gray-500 uppercase tracking-widest font-mono">2. 3-Way Color Grading</h4>
                    
                    <div class="p-3.5 bg-[#13161c] rounded-xl flex flex-col gap-3 border border-[#1e222d]">
                        <span class="text-[11px] font-bold text-gray-400 flex items-center gap-1.5">● 暗部色彩 (Shadows)</span>
                        <div class="flex flex-col gap-2">
                            <input type="range" id="slider-sh-r" min="-40" max="40" value="0" class="w-full accent-red-500 h-[3px] bg-[#1d212c] cursor-pointer" title="紅">
                            <input type="range" id="slider-sh-g" min="-40" max="40" value="0" class="w-full accent-emerald-500 h-[3px] bg-[#1d212c] cursor-pointer" title="綠">
                            <input type="range" id="slider-sh-b" min="-40" max="40" value="0" class="w-full accent-blue-500 h-[3px] bg-[#1d212c] cursor-pointer" title="藍">
                        </div>
                    </div>

                    <div class="p-3.5 bg-[#13161c] rounded-xl flex flex-col gap-3 border border-[#1e222d]">
                        <span class="text-[11px] font-bold text-cyan-400 flex items-center gap-1.5">● 中間調 (Midtones)</span>
                        <div class="flex flex-col gap-2">
                            <input type="range" id="slider-md-r" min="-40" max="40" value="0" class="w-full accent-red-500 h-[3px] bg-[#1d212c] cursor-pointer">
                            <input type="range" id="slider-md-g" min="-40" max="40" value="0" class="w-full accent-emerald-500 h-[3px] bg-[#1d212c] cursor-pointer">
                            <input type="range" id="slider-md-b" min="-40" max="40" value="0" class="w-full accent-blue-500 h-[3px] bg-[#1d212c] cursor-pointer">
                        </div>
                    </div>

                    <div class="p-3.5 bg-[#13161c] rounded-xl flex flex-col gap-3 border border-[#1e222d]">
                        <span class="text-[11px] font-bold text-amber-400 flex items-center gap-1.5">● 高光色彩 (Highlights)</span>
                        <div class="flex flex-col gap-2">
                            <input type="range" id="slider-hi-r" min="-40" max="40" value="0" class="w-full accent-red-500 h-[3px] bg-[#1d212c] cursor-pointer">
                            <input type="range" id="slider-hi-g" min="-40" max="40" value="0" class="w-full accent-emerald-500 h-[3px] bg-[#1d212c] cursor-pointer">
                            <input type="range" id="slider-hi-b" min="-40" max="40" value="0" class="w-full accent-blue-500 h-[3px] bg-[#1d212c] cursor-pointer">
                        </div>
                    </div>
                </div>
            </div>

            <div class="p-5 border-t border-[#1b1e25] bg-[#0a0b0e] flex flex-col gap-3 shrink-0">
                <button onclick="downloadCube()" class="w-full py-3 bg-gradient-to-r from-cyan-500 to-indigo-600 hover:from-cyan-400 hover:to-indigo-500 text-slate-950 font-bold text-xs tracking-wider rounded-xl transition-all shadow-[0_0_15px_rgba(6,182,212,0.3)] flex items-center justify-center gap-2 uppercase">
                    <i class="fa-solid fa-file-export text-sm"></i><span>匯出 .CUBE 3D LUT</span>
                </button>
                <button onclick="downloadImage()" class="w-full py-2.5 bg-slate-800 hover:bg-slate-700 text-gray-300 font-bold text-xs rounded-xl transition-colors flex items-center justify-center gap-2">
                    <i class="fa-solid fa-download"></i><span>下載渲染截圖</span>
                </button>
            </div>
        </aside>
    </main>

    <script>
        const canvas = document.getElementById('previewCanvas');
        const ctx = canvas.getContext('2d', { willReadFrequently: true });
        const splitBar = document.getElementById('splitBar');
        const canvasContainer = document.getElementById('canvasContainer');
        const activePromptLabel = document.getElementById('activePromptLabel');
        const loadingOverlay = document.getElementById('loadingOverlay');
        const progressBar = document.getElementById('progressBar');
        const progressText = document.getElementById('progressText');
        const progressPercent = document.getElementById('progressPercent');
        const btnGenerate = document.getElementById('btnGenerate');
        const btnGenerateText = document.getElementById('btnGenerateText');
        const controlsContainer = document.getElementById('controlsContainer');

        // 將內部解析度設定為 1080p (大幅降低渲染壓力，實現即時順滑調色)
        // CSS 會自動將其縮放至填滿大螢幕，保持高畫質與高效能的完美平衡
        canvas.width = 1920;
        canvas.height = 1080;

        let sLog3BackupImage = null;  
        let splitRatio = 0.5; 
        let compareMode = 'split'; 
        let isDraggingSplit = false;
        let isProcessing = false;
        let renderPending = false; // 用於控制 requestAnimationFrame 渲染

        const config = {
            exposure: 0, contrast: 1.0, saturation: 1.0, temp: 0,
            shadows: { r: 0, g: 0, b: 0 },
            midtones: { r: 0, g: 0, b: 0 },
            highlights: { r: 0, g: 0, b: 0 }
        };

        window.addEventListener('load', () => {
            setupEventListeners();
            generateFromPrompt(); // 初始載入自動生成
        });

        function setWorkspaceBg(color) {
            document.getElementById('workspaceArea').style.backgroundColor = color;
            document.getElementById('bgHexLabel').innerText = color.toUpperCase();
            document.getElementById('bgCustomColor').value = color;
        }

        function encodeSLog3(x) {
            return x >= 0.01125 ? (Math.log10((x + 0.01) / 1.01) * 0.4294415) + 0.615 : (x * 171.21029 + 95) / 1023;
        }
        function decodeSLog3(y) {
            return y >= 171.21029 / 1023 ? Math.pow(10, (y - 0.615) / 0.4294415) * 1.01 - 0.01 : (y * 1023 - 95) / 171.21029;
        }

        function setupEventListeners() {
            const bindSlider = (id, prop, transform = x => parseFloat(x)) => {
                const slider = document.getElementById(`slider-${id}`);
                const label = document.getElementById(`label-${id}`);
                slider.addEventListener('input', (e) => {
                    const val = e.target.value;
                    if(prop.includes('.')) {
                        const [p1, p2] = prop.split('.');
                        config[p1][p2] = transform(val);
                    } else {
                        config[prop] = transform(val);
                    }
                    if(label) label.innerText = transform(val).toFixed(2);
                    requestRender(); // 使用幀同步渲染代替直接渲染
                });
            };

            bindSlider('exposure', 'exposure', x => parseFloat(x) / 50.0);
            bindSlider('contrast', 'contrast', x => parseFloat(x) / 100.0);
            bindSlider('saturation', 'saturation', x => parseFloat(x) / 100.0);
            bindSlider('temp', 'temp', x => parseInt(x));

            const bind3Way = (id, part, color) => {
                document.getElementById(`slider-${id}`).addEventListener('input', (e) => {
                    config[part][color] = parseFloat(e.target.value);
                    requestRender();
                });
            };
            bind3Way('sh-r', 'shadows', 'r'); bind3Way('sh-g', 'shadows', 'g'); bind3Way('sh-b', 'shadows', 'b');
            bind3Way('md-r', 'midtones', 'r'); bind3Way('md-g', 'midtones', 'g'); bind3Way('md-b', 'midtones', 'b');
            bind3Way('hi-r', 'highlights', 'r'); bind3Way('hi-g', 'highlights', 'g'); bind3Way('hi-b', 'highlights', 'b');

            const handleDrag = (clientX) => {
                const rect = canvasContainer.getBoundingClientRect();
                const x = clientX - rect.left;
                splitRatio = Math.max(0, Math.min(1, x / rect.width));
                splitBar.style.left = `${splitRatio * 100}%`;
                requestRender();
            };

            splitBar.addEventListener('mousedown', (e) => { isDraggingSplit = true; e.preventDefault(); });
            window.addEventListener('mouseup', () => { isDraggingSplit = false; });
            window.addEventListener('mousemove', (e) => { if (isDraggingSplit && compareMode === 'split') handleDrag(e.clientX); });
            
            splitBar.addEventListener('touchstart', (e) => { isDraggingSplit = true; e.preventDefault(); }, {passive: false});
            window.addEventListener('touchend', () => { isDraggingSplit = false; });
            window.addEventListener('touchmove', (e) => { 
                if (isDraggingSplit && compareMode === 'split') {
                    handleDrag(e.touches[0].clientX);
                    e.preventDefault();
                }
            }, {passive: false});
        }

        // --- 非同步且分塊的畫面生成核心 ---
        function updateProgressUI(percent, text) {
            progressBar.style.width = `${percent}%`;
            progressPercent.innerText = `${Math.floor(percent)}%`;
            progressText.innerText = text;
        }

        async function generateFromPrompt() {
            if (isProcessing) return;
            isProcessing = true;
            
            // 鎖定 UI 並顯示加載動畫
            btnGenerate.disabled = true;
            btnGenerate.classList.add('opacity-50', 'cursor-not-allowed');
            btnGenerateText.innerText = '生成中...';
            controlsContainer.classList.add('opacity-50', 'pointer-events-none');
            
            canvas.classList.add('opacity-0');
            loadingOverlay.classList.remove('opacity-0', 'pointer-events-none');
            updateProgressUI(0, "ANALYZING PROMPT...");

            let prompt = document.getElementById('promptInput').value.trim().toLowerCase();
            if (!prompt) prompt = "sunset wave";
            activePromptLabel.innerText = `PROMPT: ${prompt.toUpperCase()}`;

            // 讓瀏覽器有時間渲染 UI (進度條 0%)
            await new Promise(r => setTimeout(r, 50)); 

            // 步驟 1: 繪製基礎圖形
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            if (prompt.includes('neon') || prompt.includes('cyberpunk')) drawRealisticCyberpunk();
            else if (prompt.includes('forest') || prompt.includes('nature')) drawRealisticForest();
            else if (prompt.includes('space') || prompt.includes('star')) drawRealisticSpace();
            else drawRealisticSunset();

            updateProgressUI(15, "RENDERING GEOMETRY...");
            await new Promise(r => setTimeout(r, 50)); // UI 更新

            // 步驟 2: 分塊處理像素 (加入雜訊 & 轉換 S-Log3)
            let rawImg = ctx.getImageData(0, 0, canvas.width, canvas.height);
            let d = rawImg.data;
            let sLogData = ctx.createImageData(canvas.width, canvas.height);
            let targetD = sLogData.data;

            const totalRows = canvas.height;
            const rowWidth = canvas.width * 4;
            let currentRow = 0;
            const rowsPerChunk = 40; // 每次處理 40 行，平衡速度與 UI 順滑度

            function processNextChunk() {
                let endRow = Math.min(currentRow + rowsPerChunk, totalRows);
                for (let y = currentRow; y < endRow; y++) {
                    let offset = y * rowWidth;
                    for (let x = 0; x < canvas.width; x++) {
                        let i = offset + x * 4;
                        
                        // 雜訊演算
                        let noise = (Math.random() - 0.5) * 8;
                        let r = Math.min(255, Math.max(0, d[i] + noise));
                        let g = Math.min(255, Math.max(0, d[i+1] + noise));
                        let b = Math.min(255, Math.max(0, d[i+2] + noise));

                        // 線性至 S-Log3 曲線轉換
                        let r_lin = Math.pow(r/255, 2.2);
                        let g_lin = Math.pow(g/255, 2.2);
                        let b_lin = Math.pow(b/255, 2.2);

                        targetD[i]     = Math.min(255, Math.max(0, encodeSLog3(r_lin) * 255));
                        targetD[i + 1] = Math.min(255, Math.max(0, encodeSLog3(g_lin) * 255));
                        targetD[i + 2] = Math.min(255, Math.max(0, encodeSLog3(b_lin) * 255));
                        targetD[i + 3] = 255;
                    }
                }
                currentRow = endRow;

                // 更新進度條 (15% ~ 100%)
                let progress = 15 + (currentRow / totalRows) * 85;
                updateProgressUI(progress, `ENCODING S-LOG3 DATA...`);

                if (currentRow < totalRows) {
                    // 將下一塊排入下一幀，避免卡死主執行緒
                    requestAnimationFrame(processNextChunk);
                } else {
                    finishGeneration(sLogData);
                }
            }

            // 啟動分塊處理
            requestAnimationFrame(processNextChunk);
        }

        function finishGeneration(sLogData) {
            sLog3BackupImage = sLogData;
            
            // 強制渲染一次結果
            render();

            // 恢復 UI 狀態
            loadingOverlay.classList.add('opacity-0', 'pointer-events-none');
            canvas.classList.remove('opacity-0');
            
            btnGenerate.disabled = false;
            btnGenerate.classList.remove('opacity-50', 'cursor-not-allowed');
            btnGenerateText.innerText = '生成寫實模擬畫面';
            controlsContainer.classList.remove('opacity-50', 'pointer-events-none');
            
            isProcessing = false;
        }

        // --- 繪圖函數群 ---
        function drawRealisticSunset() {
            let g = ctx.createLinearGradient(0, 0, 0, canvas.height);
            g.addColorStop(0, '#1a091f'); g.addColorStop(0.3, '#ea580c'); g.addColorStop(0.5, '#fef08a'); g.addColorStop(0.7, '#23120b'); g.addColorStop(1, '#090d16');
            ctx.fillStyle = g; ctx.fillRect(0, 0, canvas.width, canvas.height);

            let sun = ctx.createRadialGradient(1200, 375, 0, 1200, 375, 300);
            sun.addColorStop(0, '#ffffff'); sun.addColorStop(0.1, '#fffbeb'); sun.addColorStop(0.4, 'rgba(249,115,22,0.3)'); sun.addColorStop(1, 'rgba(0,0,0,0)');
            ctx.fillStyle = sun; ctx.beginPath(); ctx.arc(1200, 375, 300, 0, Math.PI*2); ctx.fill();

            ctx.fillStyle = 'rgba(12, 18, 33, 0.93)';
            ctx.beginPath(); ctx.moveTo(0, 675);
            ctx.bezierCurveTo(450, 560, 1050, 825, 1920, 630);
            ctx.lineTo(1920, 1080); ctx.lineTo(0, 1080); ctx.closePath(); ctx.fill();

            let ref = ctx.createLinearGradient(1050, 0, 1350, 0);
            ref.addColorStop(0, 'rgba(254,240,138,0)'); ref.addColorStop(0.5, 'rgba(254,240,138,0.4)'); ref.addColorStop(1, 'rgba(254,240,138,0)');
            ctx.fillStyle = ref; ctx.fillRect(0, 660, 1920, 420);
        }

        function drawRealisticCyberpunk() {
            let g = ctx.createRadialGradient(960, 540, 75, 960, 540, 900);
            g.addColorStop(0, '#131124'); g.addColorStop(1, '#030206');
            ctx.fillStyle = g; ctx.fillRect(0, 0, canvas.width, canvas.height);

            ctx.fillStyle = 'rgba(10, 8, 16, 0.85)';
            for(let i=0; i<25; i++) ctx.fillRect(i*150 - 35, 300 + Math.random()*225, 120, 825);
            ctx.fillStyle = '#050508';
            ctx.fillRect(300, 225, 260, 855);
            ctx.fillRect(1200, 150, 300, 930);

            drawBokeh(430, 330, 225, 'rgba(236, 72, 153, 0.2)');
            drawBokeh(1350, 375, 300, 'rgba(6, 182, 212, 0.25)');
        }

        function drawRealisticForest() {
            let g = ctx.createLinearGradient(0, 0, 0, canvas.height);
            g.addColorStop(0, '#475569'); g.addColorStop(0.5, '#1e293b'); g.addColorStop(1, '#0f172a');
            ctx.fillStyle = g; ctx.fillRect(0, 0, canvas.width, canvas.height);

            let ray = ctx.createLinearGradient(300, 0, 1500, 1080);
            ray.addColorStop(0, 'rgba(255,255,255,0.18)'); ray.addColorStop(0.6, 'rgba(255,255,255,0.02)'); ray.addColorStop(1, 'rgba(0,0,0,0)');
            ctx.fillStyle = ray; ctx.beginPath(); ctx.moveTo(300,0); ctx.lineTo(600,0); ctx.lineTo(1800,900); ctx.lineTo(1350,1080); ctx.closePath(); ctx.fill();

            ctx.fillStyle = '#090d14';
            for(let i = 0; i < 9; i++) {
                let w = 30 + Math.random()*35;
                ctx.fillRect(150 + i*335 + (Math.random()-0.5)*75, 0, w, 1080);
            }
        }

        function drawRealisticSpace() {
            ctx.fillStyle = '#020205'; ctx.fillRect(0, 0, canvas.width, canvas.height);
            drawBokeh(960, 540, 600, 'rgba(99, 102, 241, 0.15)');
            drawBokeh(600, 375, 450, 'rgba(219, 39, 119, 0.12)');
            for(let i=0; i<400; i++) {
                ctx.fillStyle = `rgba(255,255,255, ${Math.random()})`;
                ctx.fillRect(Math.random()*canvas.width, Math.random()*canvas.height, 2, 2);
            }
        }

        function drawBokeh(x, y, r, color) {
            let g = ctx.createRadialGradient(x, y, 0, x, y, r);
            g.addColorStop(0, color); g.addColorStop(0.8, color.replace('0.', '0.02')); g.addColorStop(1, 'rgba(0,0,0,0)');
            ctx.fillStyle = g; ctx.beginPath(); ctx.arc(x, y, r, 0, Math.PI*2); ctx.fill();
        }

        // --- 核心渲染機制 (加入 RequestAnimationFrame 控制幀率) ---
        function requestRender() {
            if (!renderPending) {
                renderPending = true;
                requestAnimationFrame(() => {
                    render();
                    renderPending = false;
                });
            }
        }

        function render() {
            if (!sLog3BackupImage) return;

            let gradedImage = ctx.createImageData(canvas.width, canvas.height);
            let rawData = sLog3BackupImage.data;
            let targetData = gradedImage.data;

            // 預先計算常數，避免迴圈內重複計算
            let exp = config.exposure;
            let con = config.contrast;
            let sat = config.saturation;
            let temp = config.temp;
            
            let shR = config.shadows.r / 200, shG = config.shadows.g / 200, shB = config.shadows.b / 200;
            let mdR = config.midtones.r / 200, mdG = config.midtones.g / 200, mdB = config.midtones.b / 200;
            let hiR = config.highlights.r / 200, hiG = config.highlights.g / 200, hiB = config.highlights.b / 200;
            
            let tempR = temp * -0.001;
            let tempB = temp * 0.0015;
            let factor = Math.pow(2.0, exp);

            for (let i = 0; i < rawData.length; i += 4) {
                let r_lin = decodeSLog3(rawData[i] / 255);
                let g_lin = decodeSLog3(rawData[i+1] / 255);
                let b_lin = decodeSLog3(rawData[i+2] / 255);

                let r = r_lin * factor, g = g_lin * factor, b = b_lin * factor;

                let luma = 0.2126 * r + 0.7152 * g + 0.0722 * b;
                let shW = Math.pow(Math.max(0, 1.0 - luma), 2);
                let hiW = Math.pow(Math.max(0, luma), 2);
                let mdW = Math.max(0, 1.0 - shW - hiW);

                r += shR*shW + mdR*mdW + hiR*hiW;
                g += shG*shW + mdG*mdW + hiG*hiW;
                b += shB*shW + mdB*mdW + hiB*hiW;

                r = Math.max(0, (r - 0.18) * con + 0.18) + tempR;
                g = Math.max(0, (g - 0.18) * con + 0.18);
                b = Math.max(0, (b - 0.18) * con + 0.18) + tempB;

                let nLuma = 0.2126 * r + 0.7152 * g + 0.0722 * b;
                r = nLuma + (r - nLuma) * sat; 
                g = nLuma + (g - nLuma) * sat; 
                b = nLuma + (b - nLuma) * sat;

                targetData[i]   = Math.min(255, Math.max(0, Math.pow(r, 1 / 2.2) * 255));
                targetData[i+1] = Math.min(255, Math.max(0, Math.pow(g, 1 / 2.2) * 255));
                targetData[i+2] = Math.min(255, Math.max(0, Math.pow(b, 1 / 2.2) * 255));
                targetData[i+3] = 255;
            }

            ctx.clearRect(0, 0, canvas.width, canvas.height);
            if (compareMode === 'split') {
                let splitX = canvas.width * splitRatio;
                ctx.putImageData(sLog3BackupImage, 0, 0);
                ctx.save(); ctx.beginPath(); ctx.rect(splitX, 0, canvas.width - splitX, canvas.height); ctx.clip();
                ctx.putImageData(gradedImage, 0, 0, splitX, 0, canvas.width - splitX, canvas.height); ctx.restore();
            } else if (compareMode === 'graded') {
                ctx.putImageData(gradedImage, 0, 0);
            } else if (compareMode === 'original') {
                ctx.putImageData(sLog3BackupImage, 0, 0);
            }
        }

        function setCompareMode(mode) {
            compareMode = mode;
            let activeClass = "px-5 py-2 text-xs font-bold rounded-lg transition-all flex items-center gap-2 text-cyan-400 bg-cyan-500/10 border border-cyan-500/20";
            let inactiveClass = "px-5 py-2 text-xs font-bold rounded-lg transition-all flex items-center gap-2 text-gray-400 hover:text-gray-200 border border-transparent";
            
            document.getElementById('modeSplit').className = inactiveClass;
            document.getElementById('modeGraded').className = inactiveClass;
            document.getElementById('modeOriginal').className = inactiveClass;
            
            if(mode === 'split') {
                document.getElementById('modeSplit').className = activeClass;
                splitBar.style.display = 'flex';
            } else if(mode === 'graded') {
                document.getElementById('modeGraded').className = activeClass;
                splitBar.style.display = 'none';
            } else if(mode === 'original') {
                document.getElementById('modeOriginal').className = activeClass;
                splitBar.style.display = 'none';
            }
            requestRender();
        }

        function applyPreset(name) {
            resetSliders(false);
            if (name === 'teal_orange') {
                config.contrast = 1.35; config.saturation = 1.20; config.shadows = { r: -15, g: 3, b: 18 }; config.highlights = { r: 16, g: 4, b: -12 };
            } else if (name === 'bleach_bypass') {
                config.contrast = 1.65; config.saturation = 0.50; config.shadows = { r: -8, g: -8, b: -4 }; config.highlights = { r: 8, g: 8, b: 12 };
            } else if (name === 'noir') {
                config.contrast = 1.55; config.saturation = 0.0;
            } else if (name === 'cyberpunk') {
                config.contrast = 1.40; config.saturation = 1.40; config.shadows = { r: 12, g: -20, b: 30 }; config.highlights = { r: -12, g: 12, b: 20 };
            }
            syncSlidersToConfig(); setCompareMode('split');
        }

        function resetSliders(needRender = true) {
            config.exposure = 0; config.contrast = 1.0; config.saturation = 1.0; config.temp = 0;
            config.shadows = { r: 0, g: 0, b: 0 }; config.midtones = { r: 0, g: 0, b: 0 }; config.highlights = { r: 0, g: 0, b: 0 };
            syncSlidersToConfig(); if (needRender) requestRender();
        }

        function syncSlidersToConfig() {
            document.getElementById('slider-exposure').value = config.exposure * 50; document.getElementById('label-exposure').innerText = (config.exposure).toFixed(2);
            document.getElementById('slider-contrast').value = config.contrast * 100; document.getElementById('label-contrast').innerText = (config.contrast).toFixed(2);
            document.getElementById('slider-saturation').value = config.saturation * 100; document.getElementById('label-saturation').innerText = (config.saturation).toFixed(2);
            document.getElementById('slider-temp').value = config.temp; document.getElementById('label-temp').innerText = config.temp;
            document.getElementById('slider-sh-r').value = config.shadows.r; document.getElementById('slider-sh-g').value = config.shadows.g; document.getElementById('slider-sh-b').value = config.shadows.b;
            document.getElementById('slider-md-r').value = config.midtones.r; document.getElementById('slider-md-g').value = config.midtones.g; document.getElementById('slider-md-b').value = config.midtones.b;
            document.getElementById('slider-hi-r').value = config.highlights.r; document.getElementById('slider-hi-g').value = config.highlights.g; document.getElementById('slider-hi-b').value = config.highlights.b;
            requestRender();
        }

        function downloadCube() {
            const lutSize = 17;
            let cube = `# Created with LUT Studio Ultra\nTITLE "Studio_SLog3_Ultra"\nLUT_3D_SIZE ${lutSize}\n\n`;
            for (let b = 0; b < lutSize; b++) {
                for (let g = 0; g < lutSize; g++) {
                    for (let r = 0; r < lutSize; r++) {
                        let color = applyGradingOnPixel(decodeSLog3(r/(lutSize-1)), decodeSLog3(g/(lutSize-1)), decodeSLog3(b/(lutSize-1)), config.exposure, config.contrast, config.saturation, config.temp);
                        cube += `${Math.min(1, Math.max(0, Math.pow(color.r, 1/2.2))).toFixed(6)} ${Math.min(1, Math.max(0, Math.pow(color.g, 1/2.2))).toFixed(6)} ${Math.min(1, Math.max(0, Math.pow(color.b, 1/2.2))).toFixed(6)}\n`;
                    }
                }
            }
            const blob = new Blob([cube], { type: 'text/plain' });
            const a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = `Ultra_LUT_SLog3.cube`; a.click();
        }

        function applyGradingOnPixel(r, g, b, exp, con, sat, temp) {
            let f = Math.pow(2.0, exp); r *= f; g *= f; b *= f;
            let luma = 0.2126 * r + 0.7152 * g + 0.0722 * b;
            let shW = Math.pow(Math.max(0, 1.0 - luma), 2), hiW = Math.pow(Math.max(0, luma), 2), mdW = Math.max(0, 1.0 - shW - hiW);
            r += (config.shadows.r/200)*shW + (config.midtones.r/200)*mdW + (config.highlights.r/200)*hiW;
            g += (config.shadows.g/200)*shW + (config.midtones.g/200)*mdW + (config.highlights.g/200)*hiW;
            b += (config.shadows.b/200)*shW + (config.midtones.b/200)*mdW + (config.highlights.b/200)*hiW;
            r = Math.max(0, (r - 0.18) * con + 0.18); g = Math.max(0, (g - 0.18) * con + 0.18); b = Math.max(0, (b - 0.18) * con + 0.18);
            r += (temp * -0.001); b += (temp * 0.0015);
            let nL = 0.2126 * r + 0.7152 * g + 0.0722 * b;
            return { r: Math.max(0, nL + (r - nL) * sat), g: Math.max(0, nL + (g - nL) * sat), b: Math.max(0, nL + (b - nL) * sat) };
        }

        function downloadImage() {
            let m = compareMode; setCompareMode('graded');
            // 稍等一幀確保渲染完成
            requestAnimationFrame(() => {
                let a = document.createElement('a'); a.href = canvas.toDataURL('image/png'); a.download = `graded_ultra_capture.png`; a.click();
                setCompareMode(m);
            });
        }
    </script>
</body>
</html>
