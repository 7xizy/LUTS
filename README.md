<!DOCTYPE html>
<html lang="zh-TW">
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
        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
            overflow-y: auto; /* 允許整個網頁上下滑動 */
        }
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #0f1115;
        }
        ::-webkit-scrollbar-thumb {
            background: #272a35;
            border-radius: 3px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #3f4456;
        }
        .no-select {
            user-select: none;
            -webkit-user-select: none;
        }
    </style>
</head>
<body class="bg-[#08090c] text-gray-100 min-h-screen w-full flex flex-col">

    <header class="bg-[#0d0f14] border-b border-[#1b1e25] px-6 py-2.5 flex items-center justify-between shrink-0 h-14 z-20 sticky top-0">
        <div class="flex items-center gap-3">
            <div class="w-7 h-7 rounded-lg bg-gradient-to-tr from-cyan-500 to-indigo-500 flex items-center justify-center shadow-lg shadow-cyan-500/10">
                <i class="fa-solid fa-play text-xs text-white pl-0.5"></i>
            </div>
            <div>
                <h1 class="font-bold text-sm tracking-wide bg-gradient-to-r from-white to-gray-400 bg-clip-text text-transparent">LUT STUDIO ULTRA</h1>
                <p class="text-[9px] text-gray-500 font-mono tracking-tighter">S-LOG3 REAL-TIME GRADING MONITOR</p>
            </div>
        </div>
        
        <div class="flex items-center gap-2">
            <span class="text-[10px] font-mono text-gray-500 mr-1">PRESETS:</span>
            <div class="flex items-center gap-1 bg-[#13161c] border border-[#222733] rounded-md p-0.5">
                <button onclick="applyPreset('teal_orange')" class="px-2.5 py-1 text-[11px] font-semibold rounded hover:bg-gray-800 transition-colors">Teal & Orange</button>
                <button onclick="applyPreset('bleach_bypass')" class="px-2.5 py-1 text-[11px] font-semibold rounded hover:bg-gray-800 transition-colors">電影旁路</button>
                <button onclick="applyPreset('noir')" class="px-2.5 py-1 text-[11px] font-semibold rounded hover:bg-gray-800 transition-colors">黑白膠卷</button>
                <button onclick="applyPreset('cyberpunk')" class="px-2.5 py-1 text-[11px] font-semibold rounded hover:bg-gray-800 transition-colors">賽博朋克</button>
            </div>
        </div>
    </header>

    <main class="flex-grow flex flex-col md:flex-row w-full">
        
        <aside class="w-full md:w-72 bg-[#0d0f14] border-r border-[#1b1e25] flex flex-col p-4 shrink-0 gap-5 z-10">
            <div class="flex flex-col gap-2">
                <div class="flex items-center gap-2 text-cyan-400 font-bold text-xs uppercase tracking-wider">
                    <i class="fa-solid fa-camera"></i>
                    <span>1. 智能畫面生成</span>
                </div>
                <div class="p-3 bg-[#13161c] border border-[#222733] rounded-xl flex flex-col gap-2.5">
                    <input id="promptInput" type="text" value="sunset wave" placeholder="輸入關鍵字: neon, forest, space..." class="w-full bg-[#090a0d] border border-[#222733] px-3 py-1.5 text-xs rounded-lg text-white focus:outline-none focus:border-cyan-500 font-medium">
                    <button onclick="generateFromPrompt()" class="w-full py-2 bg-gradient-to-r from-cyan-500 to-cyan-600 hover:from-cyan-400 hover:to-cyan-500 text-slate-950 font-bold text-xs rounded-lg transition-all flex items-center justify-center gap-1.5 shadow-lg shadow-cyan-500/10">
                        <i class="fa-solid fa-wand-magic-sparkles"></i>
                        <span>生成寫實模擬畫面</span>
                    </button>
                </div>
            </div>

            <div class="flex flex-col gap-2">
                <div class="flex items-center gap-2 text-indigo-400 font-bold text-xs uppercase tracking-wider">
                    <i class="fa-solid fa-palette"></i>
                    <span>2. 環境底色</span>
                </div>
                <div class="p-3 bg-[#13161c] border border-[#222733] rounded-xl flex flex-col gap-2">
                    <div class="grid grid-cols-4 gap-1.5">
                        <button onclick="setWorkspaceBg('#08090c')" class="h-6 rounded bg-[#08090c] border border-gray-800 hover:border-gray-500 transition-all" title="極夜黑"></button>
                        <button onclick="setWorkspaceBg('#1b1e25')" class="h-6 rounded bg-[#1b1e25] border border-gray-700 hover:border-gray-400 transition-all" title="工業灰"></button>
                        <button onclick="setWorkspaceBg('#141d26')" class="h-6 rounded bg-[#141d26] border border-gray-700 hover:border-gray-400 transition-all" title="調色藍"></button>
                        <button onclick="setWorkspaceBg('#dfdad2')" class="h-6 rounded bg-[#dfdad2] border border-gray-400 hover:border-gray-100 transition-all" title="日光沙"></button>
                    </div>
                    <div class="flex items-center justify-between mt-1 pt-1 border-t border-[#222733]">
                        <input type="color" id="bgCustomColor" oninput="setWorkspaceBg(this.value)" class="w-5 h-5 border border-gray-700 rounded cursor-pointer bg-transparent">
                        <span id="bgHexLabel" class="text-[10px] font-mono text-gray-500">#08090C</span>
                    </div>
                </div>
            </div>

            <div class="p-3 bg-[#13161c]/50 border border-[#222733]/50 rounded-xl text-[11px] text-gray-500 leading-relaxed flex flex-col gap-1.5 md:mt-auto">
                <p><strong class="text-amber-500">Sony S-Log3 Workflow</strong></p>
                <p>左側畫面完美複製了原生 10-bit S-Log3/SGamut3.Cine 的低反差低飽和狀態，右側則為經由真實 3D 矩陣解調後的最終影調。</p>
            </div>
        </aside>

        <section id="workspaceArea" class="flex-grow flex flex-col items-center justify-center p-8 transition-colors duration-300 relative no-select min-h-[600px] bg-[#1b1e25]">
            
            <div class="mb-6 flex bg-[#0d0f14]/90 backdrop-blur-md p-1 rounded-xl border border-[#222733]/80 shadow-2xl z-20">
                <button onclick="setCompareMode('split')" id="modeSplit" class="px-4 py-1.5 text-[11px] font-bold rounded-lg transition-all flex items-center gap-1.5 text-cyan-400 bg-cyan-500/10 border border-cyan-500/20">
                    <i class="fa-solid fa-columns"></i><span>左右即時對比</span>
                </button>
                <button onclick="setCompareMode('graded')" id="modeGraded" class="px-4 py-1.5 text-[11px] font-bold rounded-lg transition-all flex items-center gap-1.5 text-gray-400 hover:text-gray-200">
                    <i class="fa-solid fa-film"></i><span>完全套用 LUT</span>
                </button>
                <button onclick="setCompareMode('original')" id="modeOriginal" class="px-4 py-1.5 text-[11px] font-bold rounded-lg transition-all flex items-center gap-1.5 text-gray-400 hover:text-gray-200">
                    <i class="fa-solid fa-video-slash"></i><span>原始 S-Log3</span>
                </button>
            </div>

            <div class="relative w-full max-w-5xl aspect-video flex items-center justify-center">
                <div class="relative w-full h-full rounded-xl overflow-hidden shadow-[0_25px_60px_-15px_rgba(0,0,0,0.9)] border border-black/40 bg-black flex items-center justify-center" id="canvasContainer">
                    <canvas id="previewCanvas" class="w-full h-full object-contain pointer-events-none"></canvas>
                    
                    <div id="splitBar" class="absolute top-0 bottom-0 w-[2px] bg-white/80 shadow-[0_0_10px_rgba(255,255,255,0.5)] cursor-ew-resize flex items-center justify-center z-10" style="left: 50%;">
                        <div class="w-7 h-7 rounded-full bg-white text-slate-950 flex items-center justify-center shadow-2xl border border-gray-300 transform scale-90 opacity-80 group-hover:opacity-100 transition-opacity">
                            <i class="fa-solid fa-arrows-left-right text-[10px]"></i>
                        </div>
                    </div>
                    
                    <div class="absolute bottom-4 left-4 bg-black/60 backdrop-blur-md px-2.5 py-1 rounded text-[10px] font-mono text-gray-400 border border-white/5 pointer-events-none tracking-widest">
                        INPUT: S-LOG3 / SGAMUT3.CINE
                    </div>
                    <div id="activePromptLabel" class="absolute bottom-4 right-4 bg-black/60 backdrop-blur-md px-2.5 py-1 rounded text-[10px] font-mono text-cyan-400 border border-white/5 pointer-events-none uppercase tracking-wider">
                        PROMPT: SUNSET WAVE
                    </div>
                </div>
            </div>

        </section>

        <aside class="w-full md:w-80 bg-[#0d0f14] border-l border-[#1b1e25] flex flex-col shrink-0">
            <div class="p-4 border-b border-[#1b1e25] flex items-center justify-between bg-[#0a0b0e] shrink-0">
                <div class="flex items-center gap-2 text-cyan-400 font-bold text-xs tracking-wider uppercase">
                    <i class="fa-solid fa-sliders"></i>
                    <span>調色大師控制台</span>
                </div>
                <button onclick="resetSliders()" class="text-[11px] text-gray-500 hover:text-white transition-colors flex items-center gap-1 font-medium">
                    <i class="fa-solid fa-rotate-left"></i><span>重置參數</span>
                </button>
            </div>

            <div class="p-4 flex flex-col gap-5 bg-[#0d0f14]">
                
                <div class="flex flex-col gap-3.5">
                    <h4 class="text-[10px] font-bold text-gray-500 uppercase tracking-widest font-mono">1. Tone & Exposure</h4>
                    
                    <div class="flex flex-col gap-1">
                        <div class="flex justify-between text-xs px-0.5">
                            <span class="text-gray-400">曝光度 (Exposure)</span>
                            <span id="label-exposure" class="font-mono text-cyan-400 font-bold text-[11px]">0.00</span>
                        </div>
                        <input type="range" id="slider-exposure" min="-100" max="100" value="0" class="w-full accent-cyan-500 h-1 rounded bg-[#1f232d]">
                    </div>

                    <div class="flex flex-col gap-1">
                        <div class="flex justify-between text-xs px-0.5">
                            <span class="text-gray-400">對比度 (Contrast)</span>
                            <span id="label-contrast" class="font-mono text-cyan-400 font-bold text-[11px]">1.00</span>
                        </div>
                        <input type="range" id="slider-contrast" min="50" max="200" value="100" class="w-full accent-cyan-500 h-1 rounded bg-[#1f232d]">
                    </div>

                    <div class="flex flex-col gap-1">
                        <div class="flex justify-between text-xs px-0.5">
                            <span class="text-gray-400">飽和度 (Saturation)</span>
                            <span id="label-saturation" class="font-mono text-cyan-400 font-bold text-[11px]">1.00</span>
                        </div>
                        <input type="range" id="slider-saturation" min="0" max="200" value="100" class="w-full accent-cyan-500 h-1 rounded bg-[#1f232d]">
                    </div>

                    <div class="flex flex-col gap-1">
                        <div class="flex justify-between text-xs px-0.5">
                            <span class="text-gray-400">電影色溫 (Kelvin)</span>
                            <span id="label-temp" class="font-mono text-cyan-400 font-bold text-[11px]">0</span>
                        </div>
                        <input type="range" id="slider-temp" min="-50" max="50" value="0" class="w-full accent-cyan-500 h-1 rounded bg-[#1f232d]">
                    </div>
                </div>

                <div class="flex flex-col gap-3.5 pt-4 border-t border-[#1b1e25]">
                    <h4 class="text-[10px] font-bold text-gray-500 uppercase tracking-widest font-mono">2. 3-Way Color Grading</h4>
                    
                    <div class="p-2.5 bg-[#13161c] rounded-xl flex flex-col gap-2 border border-[#1e222d]">
                        <span class="text-[11px] font-bold text-gray-400 flex items-center gap-1.5">● 暗部色彩 (Shadows)</span>
                        <div class="flex flex-col gap-1">
                            <input type="range" id="slider-sh-r" min="-40" max="40" value="0" class="w-full accent-red-500 h-[3px] bg-[#1d212c]" title="紅">
                            <input type="range" id="slider-sh-g" min="-40" max="40" value="0" class="w-full accent-emerald-500 h-[3px] bg-[#1d212c]" title="綠">
                            <input type="range" id="slider-sh-b" min="-40" max="40" value="0" class="w-full accent-blue-500 h-[3px] bg-[#1d212c]" title="藍">
                        </div>
                    </div>

                    <div class="p-2.5 bg-[#13161c] rounded-xl flex flex-col gap-2 border border-[#1e222d]">
                        <span class="text-[11px] font-bold text-cyan-400 flex items-center gap-1.5">● 中間調 (Midtones)</span>
                        <div class="flex flex-col gap-1">
                            <input type="range" id="slider-md-r" min="-40" max="40" value="0" class="w-full accent-red-500 h-[3px] bg-[#1d212c]">
                            <input type="range" id="slider-md-g" min="-40" max="40" value="0" class="w-full accent-emerald-500 h-[3px] bg-[#1d212c]">
                            <input type="range" id="slider-md-b" min="-40" max="40" value="0" class="w-full accent-blue-500 h-[3px] bg-[#1d212c]">
                        </div>
                    </div>

                    <div class="p-2.5 bg-[#13161c] rounded-xl flex flex-col gap-2 border border-[#1e222d]">
                        <span class="text-[11px] font-bold text-amber-400 flex items-center gap-1.5">● 高光色彩 (Highlights)</span>
                        <div class="flex flex-col gap-1">
                            <input type="range" id="slider-hi-r" min="-40" max="40" value="0" class="w-full accent-red-500 h-[3px] bg-[#1d212c]">
                            <input type="range" id="slider-hi-g" min="-40" max="40" value="0" class="w-full accent-emerald-500 h-[3px] bg-[#1d212c]">
                            <input type="range" id="slider-hi-b" min="-40" max="40" value="0" class="w-full accent-blue-500 h-[3px] bg-[#1d212c]">
                        </div>
                    </div>
                </div>
            </div>

            <div class="p-4 border-t border-[#1b1e25] bg-[#0a0b0e] flex flex-col gap-2 shrink-0">
                <button onclick="downloadCube()" class="w-full py-2.5 bg-gradient-to-r from-cyan-500 to-indigo-600 hover:from-cyan-400 hover:to-indigo-500 text-slate-950 font-bold text-xs tracking-wider rounded-xl transition-all shadow-md flex items-center justify-center gap-1.5 uppercase">
                    <i class="fa-solid fa-file-export"></i><span>匯出標準 .CUBE 3D LUT</span>
                </button>
                <button onclick="downloadImage()" class="w-full py-2 bg-slate-800 hover:bg-slate-700 text-gray-300 font-bold text-xs rounded-xl transition-colors flex items-center justify-center gap-1.5">
                    <i class="fa-solid fa-download"></i><span>下載 4K 渲染截圖</span>
                </button>
            </div>
        </aside>
    </main>

    <script>
        const canvas = document.getElementById('previewCanvas');
        const ctx = canvas.getContext('2d');
        const splitBar = document.getElementById('splitBar');
        const canvasContainer = document.getElementById('canvasContainer');
        const activePromptLabel = document.getElementById('activePromptLabel');

        canvas.width = 2560;
        canvas.height = 1440;

        let baseOriginalImage = null; 
        let sLog3BackupImage = null;  
        let splitRatio = 0.5; 
        let compareMode = 'split'; 
        let isDraggingSplit = false;

        const config = {
            exposure: 0, contrast: 1.0, saturation: 1.0, temp: 0,
            shadows: { r: 0, g: 0, b: 0 },
            midtones: { r: 0, g: 0, b: 0 },
            highlights: { r: 0, g: 0, b: 0 }
        };

        window.addEventListener('load', () => {
            generateFromPrompt();
            setupEventListeners();
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
                    render();
                });
            };

            bindSlider('exposure', 'exposure', x => parseFloat(x) / 50.0);
            bindSlider('contrast', 'contrast', x => parseFloat(x) / 100.0);
            bindSlider('saturation', 'saturation', x => parseFloat(x) / 100.0);
            bindSlider('temp', 'temp', x => parseInt(x));

            const bind3Way = (id, part, color) => {
                document.getElementById(`slider-${id}`).addEventListener('input', (e) => {
                    config[part][color] = parseFloat(e.target.value);
                    render();
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
                render();
            };

            splitBar.addEventListener('mousedown', () => { isDraggingSplit = true; });
            window.addEventListener('mouseup', () => { isDraggingSplit = false; });
            window.addEventListener('mousemove', (e) => { if (isDraggingSplit && compareMode === 'split') handleDrag(e.clientX); });
            
            // 增加移動端觸控支援
            splitBar.addEventListener('touchstart', () => { isDraggingSplit = true; });
            window.addEventListener('touchend', () => { isDraggingSplit = false; });
            window.addEventListener('touchmove', (e) => { 
                if (isDraggingSplit && compareMode === 'split') {
                    handleDrag(e.touches[0].clientX);
                }
            });
        }

        function generateFromPrompt() {
            let prompt = document.getElementById('promptInput').value.trim().toLowerCase();
            if (!prompt) prompt = "sunset wave";
            activePromptLabel.innerText = `PROMPT: ${prompt.toUpperCase()}`;

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            if (prompt.includes('neon') || prompt.includes('cyberpunk')) {
                drawRealisticCyberpunk();
            } else if (prompt.includes('forest') || prompt.includes('nature')) {
                drawRealisticForest();
            } else if (prompt.includes('space') || prompt.includes('star')) {
                drawRealisticSpace();
            } else {
                drawRealisticSunset();
            }

            let rawImg = ctx.getImageData(0, 0, canvas.width, canvas.height);
            let rawData = rawImg.data;
            for (let i = 0; i < rawData.length; i += 4) {
                let noise = (Math.random() - 0.5) * 7;
                rawData[i] = Math.min(255, Math.max(0, rawData[i] + noise));
                rawData[i+1] = Math.min(255, Math.max(0, rawData[i+1] + noise));
                rawData[i+2] = Math.min(255, Math.max(0, rawData[i+2] + noise));
            }
            ctx.putImageData(rawImg, 0, 0);
            baseOriginalImage = rawImg;

            let sLogData = ctx.createImageData(baseOriginalImage);
            sLogData.data.set(baseOriginalImage.data);
            let d = sLogData.data;

            for (let i = 0; i < d.length; i += 4) {
                let r_lin = Math.pow(d[i]/255, 2.2);
                let g_lin = Math.pow(d[i+1]/255, 2.2);
                let b_lin = Math.pow(d[i+2]/255, 2.2);

                d[i]     = Math.min(255, Math.max(0, encodeSLog3(r_lin) * 255));
                d[i + 1] = Math.min(255, Math.max(0, encodeSLog3(g_lin) * 255));
                d[i + 2] = Math.min(255, Math.max(0, encodeSLog3(b_lin) * 255));
                d[i + 3] = 255;
            }
            sLog3BackupImage = sLogData;
            render();
        }

        function drawRealisticSunset() {
            let g = ctx.createLinearGradient(0, 0, 0, canvas.height);
            g.addColorStop(0, '#1a091f'); g.addColorStop(0.3, '#ea580c'); g.addColorStop(0.5, '#fef08a'); g.addColorStop(0.7, '#23120b'); g.addColorStop(1, '#090d16');
            ctx.fillStyle = g; ctx.fillRect(0, 0, canvas.width, canvas.height);

            let sun = ctx.createRadialGradient(1600, 500, 0, 1600, 500, 400);
            sun.addColorStop(0, '#ffffff'); sun.addColorStop(0.1, '#fffbeb'); sun.addColorStop(0.4, 'rgba(249,115,22,0.3)'); sun.addColorStop(1, 'rgba(0,0,0,0)');
            ctx.fillStyle = sun; ctx.beginPath(); ctx.arc(1600, 500, 400, 0, Math.PI*2); ctx.fill();

            ctx.fillStyle = 'rgba(12, 18, 33, 0.93)';
            ctx.beginPath(); ctx.moveTo(0, 900);
            ctx.bezierCurveTo(600, 750, 1400, 1100, 2560, 850);
            ctx.lineTo(2560, 1440); ctx.lineTo(0, 1440); ctx.closePath(); ctx.fill();

            let ref = ctx.createLinearGradient(1400, 0, 1800, 0);
            ref.addColorStop(0, 'rgba(254,240,138,0)'); ref.addColorStop(0.5, 'rgba(254,240,138,0.4)'); ref.addColorStop(1, 'rgba(254,240,138,0)');
            ctx.fillStyle = ref; ctx.fillRect(0, 880, 2560, 560);
        }

        function drawRealisticCyberpunk() {
            let g = ctx.createRadialGradient(1280, 720, 100, 1280, 720, 1200);
            g.addColorStop(0, '#131124'); g.addColorStop(1, '#030206');
            ctx.fillStyle = g; ctx.fillRect(0, 0, canvas.width, canvas.height);

            ctx.fillStyle = 'rgba(10, 8, 16, 0.85)';
            for(let i=0; i<15; i++) {
                ctx.fillRect(i*200 - 50, 400 + Math.random()*300, 160, 1100);
            }
            ctx.fillStyle = '#050508';
            ctx.fillRect(400, 300, 350, 1140);
            ctx.fillRect(1600, 200, 400, 1240);

            drawBokeh(575, 450, 300, 'rgba(236, 72, 153, 0.2)');
            drawBokeh(1800, 500, 400, 'rgba(6, 182, 212, 0.25)');
        }

        function drawRealisticForest() {
            let g = ctx.createLinearGradient(0, 0, 0, canvas.height);
            g.addColorStop(0, '#475569'); g.addColorStop(0.5, '#1e293b'); g.addColorStop(1, '#0f172a');
            ctx.fillStyle = g; ctx.fillRect(0, 0, canvas.width, canvas.height);

            let ray = ctx.createLinearGradient(400, 0, 2000, 1440);
            ray.addColorStop(0, 'rgba(255,255,255,0.18)'); ray.addColorStop(0.6, 'rgba(255,255,255,0.02)'); ray.addColorStop(1, 'rgba(0,0,0,0)');
            ctx.fillStyle = ray; ctx.beginPath(); ctx.moveTo(400,0); ctx.lineTo(800,0); ctx.lineTo(2400,1200); ctx.lineTo(1800,1440); ctx.closePath(); ctx.fill();

            ctx.fillStyle = '#090d14';
            for(let i = 0; i < 6; i++) {
                let w = 40 + Math.random()*50;
                ctx.fillRect(200 + i*450 + (Math.random()-0.5)*100, 0, w, 1440);
            }
        }

        function drawRealisticSpace() {
            ctx.fillStyle = '#020205'; ctx.fillRect(0, 0, canvas.width, canvas.height);
            drawBokeh(1280, 720, 800, 'rgba(99, 102, 241, 0.15)');
            drawBokeh(800, 500, 600, 'rgba(219, 39, 119, 0.12)');
            for(let i=0; i<300; i++) {
                ctx.fillStyle = `rgba(255,255,255, ${Math.random()})`;
                ctx.fillRect(Math.random()*canvas.width, Math.random()*canvas.height, 2, 2);
            }
        }

        function drawBokeh(x, y, r, color) {
            let g = ctx.createRadialGradient(x, y, 0, x, y, r);
            g.addColorStop(0, color); g.addColorStop(0.8, color.replace('0.', '0.02')); g.addColorStop(1, 'rgba(0,0,0,0)');
            ctx.fillStyle = g; ctx.beginPath(); ctx.arc(x, y, r, 0, Math.PI*2); ctx.fill();
        }

        function render() {
            if (!sLog3BackupImage) return;

            let gradedImage = ctx.createImageData(sLog3BackupImage);
            let rawData = sLog3BackupImage.data;
            let targetData = gradedImage.data;

            let exp = config.exposure;
            let con = config.contrast;
            let sat = config.saturation;
            let temp = config.temp;

            for (let i = 0; i < rawData.length; i += 4) {
                let r_lin = decodeSLog3(rawData[i] / 255);
                let g_lin = decodeSLog3(rawData[i+1] / 255);
                let b_lin = decodeSLog3(rawData[i+2] / 255);

                let f = Math.pow(2.0, exp);
                let r = r_lin * f, g = g_lin * f, b = b_lin * f;

                let luma = 0.2126 * r + 0.7152 * g + 0.0722 * b;
                let shW = Math.pow(Math.max(0, 1.0 - luma), 2);
                let hiW = Math.pow(Math.max(0, luma), 2);
                let mdW = Math.max(0, 1.0 - shW - hiW);

                r += (config.shadows.r/200)*shW + (config.midtones.r/200)*mdW + (config.highlights.r/200)*hiW;
                g += (config.shadows.g/200)*shW + (config.midtones.g/200)*mdW + (config.highlights.g/200)*hiW;
                b += (config.shadows.b/200)*shW + (config.midtones.b/200)*mdW + (config.highlights.b/200)*hiW;

                r = Math.max(0, (r - 0.18) * con + 0.18);
                g = Math.max(0, (g - 0.18) * con + 0.18);
                b = Math.max(0, (b - 0.18) * con + 0.18);

                r += (temp * -0.001); b += (temp * 0.0015);

                let nLuma = 0.2126 * r + 0.7152 * g + 0.0722 * b;
                r = nLuma + (r - nLuma) * sat; g = nLuma + (g - nLuma) * sat; b = nLuma + (b - nLuma) * sat;

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
            document.getElementById('modeSplit').className = "px-4 py-1.5 text-[11px] font-bold rounded-lg transition-all flex items-center gap-1.5 text-gray-400 hover:text-gray-200";
            document.getElementById('modeGraded').className = "px-4 py-1.5 text-[11px] font-bold rounded-lg transition-all flex items-center gap-1.5 text-gray-400 hover:text-gray-200";
            document.getElementById('modeOriginal').className = "px-4 py-1.5 text-[11px] font-bold rounded-lg transition-all flex items-center gap-1.5 text-gray-400 hover:text-gray-200";
            
            if(mode === 'split') {
                document.getElementById('modeSplit').className = "px-4 py-1.5 text-[11px] font-bold rounded-lg transition-all flex items-center gap-1.5 text-cyan-400 bg-cyan-500/10 border border-cyan-500/20";
                splitBar.style.display = 'flex';
            } else if(mode === 'graded') {
                document.getElementById('modeGraded').className = "px-4 py-1.5 text-[11px] font-bold rounded-lg transition-all flex items-center gap-1.5 text-cyan-400 bg-cyan-500/10 border border-cyan-500/20";
                splitBar.style.display = 'none';
            } else if(mode === 'original') {
                document.getElementById('modeOriginal').className = "px-4 py-1.5 text-[11px] font-bold rounded-lg transition-all flex items-center gap-1.5 text-cyan-400 bg-cyan-500/10 border border-cyan-500/20";
                splitBar.style.display = 'none';
            }
            render();
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
            syncSlidersToConfig(); if (needRender) render();
        }

        function syncSlidersToConfig() {
            document.getElementById('slider-exposure').value = config.exposure * 50; document.getElementById('label-exposure').innerText = (config.exposure).toFixed(2);
            document.getElementById('slider-contrast').value = config.contrast * 100; document.getElementById('label-contrast').innerText = (config.contrast).toFixed(2);
            document.getElementById('slider-saturation').value = config.saturation * 100; document.getElementById('label-saturation').innerText = (config.saturation).toFixed(2);
            document.getElementById('slider-temp').value = config.temp; document.getElementById('label-temp').innerText = config.temp;
            document.getElementById('slider-sh-r').value = config.shadows.r; document.getElementById('slider-sh-g').value = config.shadows.g; document.getElementById('slider-sh-b').value = config.shadows.b;
            document.getElementById('slider-md-r').value = config.midtones.r; document.getElementById('slider-md-g').value = config.midtones.g; document.getElementById('slider-md-b').value = config.midtones.b;
            document.getElementById('slider-hi-r').value = config.highlights.r; document.getElementById('slider-hi-g').value = config.highlights.g; document.getElementById('slider-hi-b').value = config.highlights.b;
            render();
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
            let a = document.createElement('a'); a.href = canvas.toDataURL('image/png'); a.download = `graded_ultra_capture.png`; a.click();
            setCompareMode(m);
        }
    </script>
</body>
</html>
