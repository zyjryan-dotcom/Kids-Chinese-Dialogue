<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>亲子中文对话</title>
  <link href="https://fonts.googleapis.com/css2?family=Ma+Shan+Zheng&family=Noto+Sans+SC:wght@400;700&display=swap" rel="stylesheet">
  <style>
    :root {
      --sky: #fef9f0;
      --cloud: #ffffff;
      --peach: #ffb347;
      --coral: #ff7043;
      --mint: #4caf8a;
      --mint-dark: #388e6a;
      --lavender: #9c77d4;
      --lavender-dark: #7b5bbb;
      --text: #3a2e2e;
      --soft: #a09090;
      --card-shadow: 0 8px 32px rgba(60,30,10,0.10);
    }

    * { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      background: var(--sky);
      font-family: 'Noto Sans SC', sans-serif;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 24px 16px;
      background-image:
        radial-gradient(circle at 10% 20%, #ffe0b2 0%, transparent 40%),
        radial-gradient(circle at 85% 80%, #e8f5e9 0%, transparent 40%),
        radial-gradient(circle at 50% 50%, #fce4ec 0%, transparent 60%);
    }

    /* Floating decorations */
    .deco {
      position: fixed;
      font-size: 2.2rem;
      opacity: 0.18;
      pointer-events: none;
      animation: float 6s ease-in-out infinite;
    }
    .deco:nth-child(1) { top: 8%; left: 5%; animation-delay: 0s; }
    .deco:nth-child(2) { top: 15%; right: 6%; animation-delay: 1.5s; }
    .deco:nth-child(3) { bottom: 10%; left: 8%; animation-delay: 3s; }
    .deco:nth-child(4) { bottom: 18%; right: 5%; animation-delay: 4.5s; }
    @keyframes float {
      0%, 100% { transform: translateY(0) rotate(0deg); }
      50% { transform: translateY(-14px) rotate(8deg); }
    }

    .card {
      background: var(--cloud);
      border-radius: 28px;
      box-shadow: var(--card-shadow);
      padding: 36px 32px 32px;
      max-width: 520px;
      width: 100%;
      text-align: center;
      position: relative;
    }

    /* Progress bar */
    .progress-wrap {
      display: flex;
      gap: 8px;
      justify-content: center;
      margin-bottom: 24px;
    }
    .progress-dot {
      width: 10px; height: 10px;
      border-radius: 50%;
      background: #e0d8d0;
      transition: background 0.3s, transform 0.3s;
    }
    .progress-dot.active {
      background: var(--peach);
      transform: scale(1.3);
    }
    .progress-dot.done {
      background: var(--mint);
    }

    /* Question */
    #question-wrap {
      margin-bottom: 28px;
    }
    #question {
      font-family: 'Ma Shan Zheng', cursive;
      font-size: 2.2rem;
      color: var(--text);
      line-height: 1.4;
      display: inline-block;
    }
    .speak-q-btn {
      background: none;
      border: none;
      cursor: pointer;
      font-size: 1.4rem;
      margin-left: 6px;
      vertical-align: middle;
      transition: transform 0.2s;
      padding: 2px 6px;
      border-radius: 8px;
    }
    .speak-q-btn:hover { transform: scale(1.2); background: #fff3e0; }

    /* Answer buttons */
    .answers {
      display: flex;
      flex-direction: column;
      gap: 14px;
      margin-bottom: 24px;
    }
    .answer-btn {
      font-family: 'Ma Shan Zheng', cursive;
      font-size: 1.55rem;
      padding: 14px 24px;
      border-radius: 16px;
      border: 2.5px solid transparent;
      cursor: pointer;
      transition: all 0.22s cubic-bezier(.4,0,.2,1);
      position: relative;
      overflow: hidden;
      background: #f7f2ec;
      color: var(--text);
    }
    .answer-btn::after {
      content: '';
      position: absolute;
      inset: 0;
      background: rgba(255,255,255,0.35);
      opacity: 0;
      transition: opacity 0.2s;
    }
    .answer-btn:hover { transform: translateY(-3px); box-shadow: 0 6px 18px rgba(0,0,0,0.10); }
    .answer-btn:hover::after { opacity: 1; }
    .answer-btn.selected {
      background: var(--mint);
      color: white;
      border-color: var(--mint-dark);
      transform: scale(1.04);
      box-shadow: 0 6px 24px rgba(76,175,138,0.35);
    }
    .answer-btn:nth-child(2) { }
    .answer-btn:nth-child(2).selected {
      background: var(--lavender);
      border-color: var(--lavender-dark);
      box-shadow: 0 6px 24px rgba(156,119,212,0.35);
    }

    /* Result */
    #result {
      min-height: 2em;
      font-size: 1.15rem;
      color: var(--mint-dark);
      font-weight: 700;
      margin-bottom: 20px;
      transition: all 0.3s;
      animation: fadeIn 0.35s;
    }
    #result.error { color: var(--coral); }
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(6px); }
      to { opacity: 1; transform: translateY(0); }
    }

    /* Control buttons */
    .controls {
      display: flex;
      gap: 12px;
      justify-content: center;
      flex-wrap: wrap;
    }
    .ctrl-btn {
      font-family: 'Noto Sans SC', sans-serif;
      font-size: 1rem;
      font-weight: 700;
      padding: 10px 22px;
      border-radius: 12px;
      border: none;
      cursor: pointer;
      transition: all 0.2s;
      display: flex;
      align-items: center;
      gap: 6px;
    }
    .ctrl-btn:hover { transform: translateY(-2px); }
    .btn-speak {
      background: #fff3e0;
      color: #e65100;
      box-shadow: 0 2px 8px rgba(230,81,0,0.12);
    }
    .btn-speak:hover { background: #ffe0b2; }
    .btn-next {
      background: linear-gradient(135deg, var(--peach), var(--coral));
      color: white;
      box-shadow: 0 4px 14px rgba(255,112,67,0.30);
    }
    .btn-next:hover { box-shadow: 0 6px 20px rgba(255,112,67,0.45); }

    /* Speaking animation */
    .speaking-dots {
      display: inline-flex;
      gap: 4px;
      vertical-align: middle;
      margin-left: 6px;
    }
    .speaking-dots span {
      width: 6px; height: 6px;
      border-radius: 50%;
      background: var(--mint);
      animation: pulse 0.8s ease-in-out infinite;
    }
    .speaking-dots span:nth-child(2) { animation-delay: 0.2s; }
    .speaking-dots span:nth-child(3) { animation-delay: 0.4s; }
    @keyframes pulse {
      0%, 100% { transform: scaleY(1); }
      50% { transform: scaleY(2); }
    }

    footer {
      margin-top: 20px;
      font-size: 0.8rem;
      color: var(--soft);
    }
  </style>
</head>
<body>

  <!-- Floating decorations -->
  <div class="deco">🌸</div>
  <div class="deco">⭐</div>
  <div class="deco">🐼</div>
  <div class="deco">🌈</div>

  <div class="card">

    <!-- Progress dots -->
    <div class="progress-wrap" id="progress"></div>

    <!-- Question -->
    <div id="question-wrap">
      <span id="question"></span>
      <button class="speak-q-btn" onclick="speakQuestion()" title="读问题">🔊</button>
    </div>

    <!-- Answer buttons -->
    <div class="answers">
      <button id="btn1" class="answer-btn"></button>
      <button id="btn2" class="answer-btn"></button>
    </div>

    <!-- Result text -->
    <p id="result"></p>

    <!-- Controls -->
    <div class="controls">
      <button class="ctrl-btn btn-speak" onclick="speakSelected()">🔊 再读一遍</button>
      <button class="ctrl-btn btn-next" onclick="next()">下一句 →</button>
    </div>

  </div>

  <footer>点击答案 → 自动朗读 ✨</footer>

  <script>
    const data = [
      { q: "你饿了吗？",       a: ["我饿了",   "我不饿"]   },
      { q: "你想喝什么？",     a: ["我想喝水", "我想喝牛奶"] },
      { q: "你开心吗？",       a: ["我很开心", "我不开心"] },
      { q: "你叫什么名字？",   a: ["我叫小明", "我叫小红"] },
      { q: "你几岁了？",       a: ["我三岁了", "我五岁了"] },
      { q: "你喜欢吃什么？",   a: ["我喜欢吃苹果", "我喜欢吃饺子"] },
    ];

    let index = 0;
    let selected = "";
    let voices = [];

    const questionEl = document.getElementById("question");
    const resultEl   = document.getElementById("result");
    const btn1       = document.getElementById("btn1");
    const btn2       = document.getElementById("btn2");
    const progressEl = document.getElementById("progress");

    // ✅ FIX 1: Load voices properly (async on Chrome)
    function loadVoices() {
      voices = window.speechSynthesis.getVoices();
    }
    loadVoices();
    if (window.speechSynthesis.onvoiceschanged !== undefined) {
      window.speechSynthesis.onvoiceschanged = loadVoices;
    }

    // ✅ FIX 2: Pick the best Chinese voice available
    function getChineseVoice() {
      // Prefer zh-CN, fall back to any zh voice
      return voices.find(v => v.lang === "zh-CN")
          || voices.find(v => v.lang.startsWith("zh"))
          || null;
    }

    function buildProgress() {
      progressEl.innerHTML = "";
      data.forEach((_, i) => {
        const dot = document.createElement("div");
        dot.className = "progress-dot" + (i === index ? " active" : (i < index ? " done" : ""));
        progressEl.appendChild(dot);
      });
    }

    function updateQuestion() {
      const item = data[index];
      questionEl.textContent = item.q;
      btn1.textContent = item.a[0];
      btn2.textContent = item.a[1];
      btn1.classList.remove("selected");
      btn2.classList.remove("selected");
      resultEl.textContent = "";
      resultEl.className = "";
      selected = "";
      buildProgress();
    }

    function choose(text, btnEl) {
      selected = text;
      btn1.classList.remove("selected");
      btn2.classList.remove("selected");
      btnEl.classList.add("selected");
      resultEl.className = "";
      resultEl.innerHTML = `你说：${text}`;
      speakText(text);
    }

    function speakText(text) {
      if (!window.speechSynthesis) {
        showError("❌ 浏览器不支持语音，请用 Chrome 或 Edge");
        return;
      }

      // ✅ FIX 3: Cancel + short delay avoids Chrome bug where speech never starts
      speechSynthesis.cancel();
      setTimeout(() => {
        const msg = new SpeechSynthesisUtterance(text);
        msg.lang  = "zh-CN";
        msg.rate  = 0.85;
        msg.pitch = 1.1;

        // ✅ FIX 4: Assign best available Chinese voice
        const chineseVoice = getChineseVoice();
        if (chineseVoice) msg.voice = chineseVoice;

        msg.onerror = (e) => {
          // Ignore "interrupted" errors (caused by cancel())
          if (e.error === "interrupted") return;
          showError("⚠️ 朗读失败，请用 Chrome 或 Edge 浏览器");
        };

        msg.onstart = () => {
          if (text === selected) {
            resultEl.innerHTML = `你说：${text} <span class="speaking-dots"><span></span><span></span><span></span></span>`;
          }
        };

        msg.onend = () => {
          if (text === selected) resultEl.innerHTML = `你说：${text} ✅`;
        };

        speechSynthesis.speak(msg);
      }, 100); // 100ms delay fixes Chrome cancellation bug
    }

    function speakQuestion() {
      speakText(data[index].q);
    }

    function speakSelected() {
      if (!selected) {
        resultEl.className = "error";
        resultEl.textContent = "请先选择一个答案 👆";
        return;
      }
      speakText(selected);
    }

    function showError(msg) {
      resultEl.className = "error";
      resultEl.textContent = msg;
    }

    function next() {
      speechSynthesis.cancel();
      index = (index + 1) % data.length;
      updateQuestion();
    }

    function init() {
      btn1.addEventListener("click", () => choose(data[index].a[0], btn1));
      btn2.addEventListener("click", () => choose(data[index].a[1], btn2));
      updateQuestion();
    }

    window.onload = init;
  </script>
</body>
</html>
