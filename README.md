<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LUT Studio - 線上預覽與創作系統</title>
    <style>
        :root {
            --bg-color: #121212;
            --panel-color: #1e1e1e;
            --accent-color: #00adb5;
            --text-color: #eeeeee;
        }

        body {
            margin: 0;
            padding: 0;
            background-color: var(--bg-color);
            color: var(--text-color);
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            height: 100vh;
            overflow: hidden;
        }

        /* 左側工作區 */
        .workspace {
            flex: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 20px;
            box-sizing: border-box;
        }

        .canvas-container {
            position: relative;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            border-radius: 8px;
            overflow: hidden;
            background: #000;
            max-width: 100%;
            max-height: 70vh;
        }

        canvas {
            display: block;
            max-width: 100%;
            height: auto;
        }

        .toolbar {
            margin-top: 20px;
            display: flex;
            gap: 10px;
        }

        button {
            background-color: #333;
            color: white;
            border: 1px solid #444;
            padding: 10px 20px;
            border-radius: 4px;
            cursor: pointer;
            transition: all 0.2s;
        }

        button:hover {
            background-color: var(--accent-color);
            border-color: var(--accent-color);
        }

        button.active {
            background-color: var(--accent-color);
            border-color: var(--accent-color);
        }

        /* 右側控制面板 */
        .control-panel {
            width: 380px;
            background-color: var(--panel-color);
            border-left: 1px solid #2d2d2d;
            padding: 20px;
            box-sizing: border-box;
            overflow-y: auto;
        }

        h2, h3 {
            margin-top: 0;
            color: var(--accent-color);
        }

        .section {
            margin-bottom: 25px;
            border-bottom: 1px solid #2d2d2d;
            padding-bottom: 15px;
        }

        .control-group {
            margin-bottom: 15px;
        }

        .control-group label {
            display: flex;
            justify-content: space-between;
            font-size: 14px;
            margin-bottom: 5px;
            color: #bbb;
        }

        input[type="range"] {
            width: 100%;
            background: #333;
            accent-color: var(--accent-color);
        }

        select {
            width: 100%;
            padding: 8px;
            background: #333;
            color: white;
            border: 1px solid #444;
            border-radius: 4px;
        }
    </style>
</head>
<body>

    <div class="workspace">
        <div class="canvas-container">
            <canvas id="previewCanvas"></canvas>
        </div>
        <div class="toolbar">
            <button id="viewOriginal" onclick="setViewMode('log')">顯示 S-Log3 原片</button>
            <button id="viewLut" class="active" onclick="setViewMode('lut')">套用自訂 LUT</button>
            <button id="view709" onclick="setViewMode('rec709')">快速還原 Rec.709</button>
        </div>
    </div>

    <div class="control-panel">
        <h2>LUT 創作工坊</h2>
        
        <div class="section">
            <h3>1. 選擇模擬場景</h3>
            <div class="control-group">
                <select id="sceneSelect" onchange="generateScene()">
                    <option value="cyberpunk">霓虹都市 (高反差/夜景)</option>
                    <option value="sunset">金色夕陽 (高動態範圍/逆光)</option>
                    <option value="forest">迷霧森林 (低飽和/冷調)</option>
                </select>
            </div>
        </div>

        <div class="section">
            <h3>2. 基本基礎校正 (Base)</h3>
            <div class="control-group">
                <label>曝光度 (Exposure) <span id="val-exposure">0</span></label>
                <input type="range" id="exposure" min="-100" max="100" value="0" oninput="updateParam('exposure', this.value)">
            </div>
            <div class="control-group">
                <label>對比度 (Contrast) <span id="val-contrast">0</span></label>
                <input type="range" id="contrast" min="-100" max="100" value="0" oninput="updateParam('contrast', this.value)">
            </div>
            <div class="control-group">
                <label>飽和度 (Saturation) <span id="val-saturation">0</span></label>
                <input type="range" id="saturation" min="-100" max="100" value="0" oninput="updateParam('saturation', this.value)">
            </div>
        </div>

        <div class="section">
            <h3>3. 色彩風格化 (Creative LUT)</h3>
            <div class="control-group">
                <label style="color:#ff5e5e;">紅色通道 (Red) <span id="val-red">0</span></label>
                <input type="range" id="red" min="-50" max="50" value="0" oninput="updateParam('red', this.value)">
            </div>
            <div class="control-group">
                <label style="color:#5eff5e;">綠色通道 (Green) <span id="val-green">0</span></label>
                <input type="range" id="green" min="-50" max="50" value="0" oninput="updateParam('green', this.value)">
            </div>
            <div class="control-group">
                <label style="color:#5e8cff;">藍色通道 (Blue) <span id="val-blue">0</span></label>
                <input type="range" id="blue" min="-50" max="50" value="0" oninput="updateParam('blue', this.value)">
            </div>
            <div class="control-group">
                <label>色溫 (Tint - 藍/黃) <span id="val-temperature">0</span></label>
                <input type="range" id="temperature" min="-50" max="50" value="0" oninput="updateParam('temperature', this.value)">
            </div>
        </div>

        <div class="section">
            <h3>4. 輸出</h3>
            <button style="width:100%; margin-bottom:10px;" onclick="resetParams()">重設所有參數</button>
            <button style="width:100%; background-color: var(--accent-color);" onclick="exportLUT()">匯出目前調色參數</button>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('previewCanvas');
        const ctx = canvas.getContext('2d');
        
        // 設置畫布解析度 (16:9)
        canvas.width = 800;
        canvas.height = 450;

        // 儲存原始產生的 S-Log3 像素資料
        let originalLogData = null;
        let viewMode = 'lut'; // log, lut, rec709

        // 調色參數狀態
        let params = {
            exposure: 0,
            contrast: 0,
            saturation: 0,
            red: 0,
            green: 0,
            blue: 0,
            temperature: 0
        };

        // 初始化
        window.onload = function() {
            generateScene();
        };

        // 1. 模擬畫面產生器 (產生符合 S-Log3 特性的灰暗、高動態圖像)
        function generateScene() {
            const scene = document.getElementById('sceneSelect').value;
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            if (scene === 'cyberpunk') {
                // 背景暗部
                let gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
                gradient.addColorStop(0, '#2a2030');
                gradient.addColorStop(1, '#101015');
                ctx.fillStyle = gradient;
                ctx.fillRect(0, 0, canvas.width, canvas.height);

                // 模擬霓虹燈大樓 (高光點)
                ctx.fillStyle = '#ff2a85';
                ctx.fillRect(150, 100, 80, 350);
                ctx.fillStyle = '#00f0ff';
                ctx.fillRect(300, 180, 120, 270);
                
                // 霓虹燈發光倒影 (光源)
                ctx.fillStyle = 'rgba(255, 42, 133, 0.3)';
                ctx.beginPath(); ctx.arc(190, 100, 80, 0, Math.PI*2); ctx.fill();
            } 
            else if (scene === 'sunset') {
                // 夕陽漸層
                let gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
                gradient.addColorStop(0, '#f0a060');
                gradient.addColorStop(0.4, '#e07050');
                gradient.addColorStop(1, '#402030');
                ctx.fillStyle = gradient;
                ctx.fillRect(0, 0, canvas.width, canvas.height);

                // 太陽高光
                ctx.fillStyle = '#ffffff';
                ctx.beginPath(); ctx.arc(400, 200, 45, 0, Math.PI*2); ctx.fill();

                // 山脈剪影 (暗部)
                ctx.fillStyle = '#201018';
                ctx.beginPath();
                ctx.moveTo(0, 450); ctx.lineTo(200, 300); ctx.lineTo(450, 380); ctx.lineTo(650, 280); ctx.lineTo(800, 450);
                ctx.fill();
            }
            else if (scene === 'forest') {
                // 森林清晨
                let gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
                gradient.addColorStop(0, '#708580');
                gradient.addColorStop(1, '#20302b');
                ctx.fillStyle = gradient;
                ctx.fillRect(0, 0, canvas.width, canvas.height);

                // 樹木線條
                ctx.fillStyle = '#15221e';
                ctx.fillRect(100, 0, 40, 450);
                ctx.fillRect(250, 0, 25, 450);
                ctx.fillRect(550, 0, 50, 450);

                // 晨霧效果
                ctx.fillStyle = 'rgba(255,255,255,0.15)';
                ctx.fillRect(0, 200, 800, 250);
            }

            // 【關鍵步驟】將剛才繪製的標準畫面，轉化為 S-Log3 曲線特徵（低對比、高動態範圍、整體偏灰白）
            let imgData = ctx.getImageData(0, 0, canvas.width, canvas.height);
            let d = imgData.data;
            for (let i = 0; i < d.length; i += 4) {
                // 模擬 S-Log3 曲線：將 0-255 壓縮並提高黑點 (Black Level)
                d[i]     = d[i] * 0.5 + 40;     // R
                d[i + 1] = d[i + 1] * 0.5 + 40; // G
                d[i + 2] = d[i + 2] * 0.5 + 40; // B
            }
            ctx.putImageData(imgData, 0, 0);
            
            // 備份這張「標準的 S-Log3 模擬圖」
            originalLogData = ctx.getImageData(0, 0, canvas.width, canvas.height);
            
            // 重新渲染畫面
            applyLUT();
        }

        // 2. 更新滑桿參數
        function updateParam(id, val) {
            params[id] = parseFloat(val);
            document.getElementById('val-' + id).innerText = val > 0 ? '+' + val : val;
            if (viewMode === 'lut') applyLUT();
        }

        // 3. 切換預覽模式
        function setViewMode(mode) {
            viewMode = mode;
            document.getElementById('viewOriginal').classList.remove('active');
            document.getElementById('viewLut').classList.remove('active');
            document.getElementById('view709').classList.remove('active');

            if (mode === 'log') {
                document.getElementById('viewOriginal').classList.add('active');
                ctx.putImageData(originalLogData, 0, 0);
            } else if (mode === 'lut') {
                document.getElementById('viewLut').classList.add('active');
                applyLUT();
            } else if (mode === 'rec709') {
                document.getElementById('view709').classList.add('active');
                applyRec709Standard();
            }
        }

        // 4. 核心：色彩科學與 LUT 運算引擎
        function applyLUT() {
            if (!originalLogData) return;

            // 複製一份全新的 S-Log3 基礎像素進行計算
            let baseImg = ctx.createImageData(originalLogData);
            baseImg.data.set(originalLogData.data);
            let d = baseImg.data;

            // 讀取目前的控制參數
            let exp = params.exposure / 100 * 50;  // 曝光範圍
            let con = (params.contrast + 100) / 100; // 對比係數
            let sat = (params.saturation + 100) / 100; // 飽和度係數

            for (let i = 0; i < d.length; i += 4) {
                let r = d[i];
                let g = d[i+1];
                let b = d[i+2];

                // 步驟 A: 還原 S-Log3 到線性或基本 Rec.709 曲線 (粗估反轉)
                r = (r - 40) / 0.5;
                g = (g - 40) / 0.5;
                b = (b - 40) / 0.5;

                // 步驟 B: 套用自訂 LUT 控制參數
                // 1. 曝光度 (Exposure)
                r += exp; g += exp; b += exp;

                // 2. 對比度 (Contrast) - 以中性灰 128 為中心點放大縮小
                r = (r - 128) * con + 128;
                g = (g - 128) * con + 128;
                b = (b - 128) * con + 128;

                // 3. RGB 通道與色溫微調
                r += params.red + (params.temperature * -0.4);
                g += params.green;
                b += params.blue + (params.temperature * 0.6);

                // 4. 飽和度 (Saturation) - 根據亮度權重計算
                let gray = 0.2126 * r + 0.7152 * g + 0.0722 * b;
                r = gray + (r - gray) * sat;
                g = gray + (g - gray) * sat;
                b = gray + (b - gray) * sat;

                // 安全邊界限制 (0 - 255)
                d[i]     = Math.min(255, Math.max(0, r));
                d[i + 1] = Math.min(255, Math.max(0, g));
                d[i + 2] = Math.min(255, Math.max(0, b));
            }

            ctx.putImageData(baseImg, 0, 0);
        }

        // 5. 快速還原標準 Rec.709 效果 (官方還原 LUT 模擬)
        function applyRec709Standard() {
            let baseImg = ctx.createImageData(originalLogData);
            baseImg.data.set(originalLogData.data);
            let d = baseImg.data;

            for (let i = 0; i < d.length; i += 4) {
                // 官方標準 S-Log3 轉 Rec.709 大致上是將對比度拉高 1.6 倍，並修正黑白位準
                d[i]     = Math.min(255, Math.max(0, (d[i] - 44) * 1.6));
                d[i + 1] = Math.min(255, Math.max(0, (d[i + 1] - 44) * 1.6));
                d[i + 2] = Math.min(255, Math.max(0, (d[i + 2] - 44) * 1.6));
            }
            ctx.putImageData(baseImg, 0, 0);
        }

        // 重設
        function resetParams() {
            for (let key in params) {
                params[key] = 0;
                let slider = document.getElementById(key);
                if(slider) slider.value = 0;
                let valLabel = document.getElementById('val-' + key);
                if(valLabel) valLabel.innerText = '0';
            }
            setViewMode('lut');
        }

        // 匯出調色數據
        function exportLUT() {
            alert("已成功生成您的專屬創意 LUT 數據！\n\n當前矩陣設定：\n" + JSON.stringify(params, null, 2) + "\n\n(提示：此參數在進階版本中可直接轉寫為標準 .cube 格式供 DaVinci / Premiere 使用)");
        }
    </script>
</body>
</html>
