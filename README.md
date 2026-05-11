<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>中文跟读小程序 - 修复版</title>
    <link href="https://fonts.googleapis.com/css2?family=Ma+Shan+Zheng&family=Noto+Sans+SC:wght@400;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --sky: #f0f7ff;
            --primary: #4a90e2;
            --text: #2c3e50;
        }
        body { background: var(--sky); font-family: 'Noto Sans SC', sans-serif; display: flex; flex-direction: column; align-items: center; padding: 20px; }
        .container { max-width: 500px; width: 100%; }
        
        /* 引导点击层 */
        #overlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(74, 144, 226, 0.95);
            display: flex; flex-direction: column; justify-content: center; align-items: center;
            z-index: 100; color: white; cursor: pointer; text-align: center;
        }

        .sentence-card {
            background: white; border-radius: 15px; padding: 20px; margin-bottom: 15px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.05); cursor: pointer;
            border: 2px solid transparent; transition: all 0.2s;
        }
        .sentence-card:active { transform: scale(0.98); }
        .reading { border-color: var(--primary); background: #eef6ff; }
        .role { font-size: 0.8rem; font-weight: bold; margin-bottom: 5px; color: var(--primary); }
        .text { font-family: 'Ma Shan Zheng', cursive; font-size: 1.6rem; color: var(--text); }
        .status-dot { display: inline-block; width: 8px; height: 8px; background: #ddd; border-radius: 50%; margin-left: 10px; }
        .reading .status-dot { background: #4a90e2; animation: blink 0.8s infinite; }
        @keyframes blink { 50% { opacity: 0; } }
    </style>
</head>
<body>

    <!-- 解决浏览器不让自动发声的遮罩层 -->
    <div id="overlay" onclick="startApp()">
        <h1 style="font-size: 3rem;">👋</h1>
        <p style="font-size: 1.2rem;">点击屏幕开启学习之旅</p>
        <span style="font-size: 0.8rem; margin-top: 10px; opacity: 0.8;">(这是为了激活浏览器的语音功能)</span>
    </div>

    <div class="container">
        <header style="text-align: center; margin-bottom: 20px;">
            <h2 style="color: var(--primary); font-family: 'Ma Shan Zheng';">点击句子跟读</h2>
        </header>
        <div id="list"></div>
    </div>

    <script>
        const data = [
            { r: "问", t: "你饿了吗？" },
            { r: "答", t: "我饿了，我想吃苹果。" },
            { r: "问", t: "你想喝什么？" },
            { r: "答", t: "我想喝牛奶。" },
            { r: "问", t: "你叫什么名字？" },
            { r: "答", t: "我叫小明。" }
        ];

        let synth = window.speechSynthesis;
        let voicesReady = false;

        // 核心修复：预加载语音库
        function loadVoices() {
            synth.getVoices();
            voicesReady = true;
        }
        if (speechSynthesis.onvoiceschanged !== undefined) {
            speechSynthesis.onvoiceschanged = loadVoices;
        }

        function startApp() {
            // 用户点击后，隐藏遮罩并尝试播放一个空声音来“解锁”语音权限
            document.getElementById('overlay').style.display = 'none';
            const silence = new SpeechSynthesisUtterance("");
            synth.speak(silence);
            renderList();
        }

        function renderList() {
            const list = document.getElementById('list');
            data.forEach(item => {
                const card = document.createElement('div');
                card.className = 'sentence-card';
                card.innerHTML = `
                    <div class="role">${item.r}</div>
                    <div class="text">${item.t}<span class="status-dot"></span></div>
                `;
                card.onclick = () => speak(item.t, card);
                list.appendChild(card);
            });
        }

        function speak(text, el) {
            // 停止当前所有声音
            synth.cancel();

            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'zh-CN';
            utterance.rate = 0.8; 

            // 针对部分浏览器（如电脑版 Chrome）寻找最佳中文男/女声
            const voices = synth.getVoices();
            const preferredVoice = voices.find(v => v.name.includes("Xiaoxiao") || v.name.includes("Huihui") || v.lang === "zh-CN");
            if (preferredVoice) utterance.voice = preferredVoice;

            utterance.onstart = () => el.classList.add('reading');
            utterance.onend = () => el.classList.remove('reading');
            utterance.onerror = () => el.classList.remove('reading');

            synth.speak(utterance);
        }

        // 初始化加载一次
        loadVoices();
    </script>
</body>
</html>
