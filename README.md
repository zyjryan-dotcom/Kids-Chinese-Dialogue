<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>中文口语跟读练习</title>
  <link href="https://fonts.googleapis.com/css2?family=Ma+Shan+Zheng&family=Noto+Sans+SC:wght@400;700&display=swap" rel="stylesheet">
  <style>
    :root {
      --sky: #f0f7ff;
      --cloud: #ffffff;
      --primary: #4a90e2;
      --secondary: #67c23a;
      --text: #2c3e50;
      --soft: #95a5a6;
      --card-shadow: 0 12px 40px rgba(0,0,0,0.08);
    }

    * { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      background: var(--sky);
      font-family: 'Noto Sans SC', sans-serif;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 20px;
    }

    .container {
      max-width: 600px;
      width: 100%;
      margin-top: 40px;
    }

    header {
      text-align: center;
      margin-bottom: 30px;
    }

    h1 {
      font-family: 'Ma Shan Zheng', cursive;
      color: var(--primary);
      font-size: 2.5rem;
      margin-bottom: 10px;
    }

    /* 句子卡片样式 */
    .sentence-card {
      background: var(--cloud);
      border-radius: 20px;
      padding: 25px;
      margin-bottom: 20px;
      box-shadow: var(--card-shadow);
      transition: transform 0.2s;
      cursor: pointer;
      position: relative;
      border: 2px solid transparent;
      display: flex;
      flex-direction: column;
      gap: 15px;
    }

    .sentence-card:hover {
      transform: translateY(-5px);
      border-color: #d1e4fb;
    }

    .sentence-card:active {
      transform: scale(0.98);
    }

    /* 角色标签 */
    .role {
      font-size: 0.85rem;
      font-weight: bold;
      padding: 4px 12px;
      border-radius: 20px;
      width: fit-content;
    }
    .role-q { background: #e3f2fd; color: #1976d2; }
    .role-a { background: #f1f8e9; color: #388e3c; }

    /* 文字内容 */
    .content {
      font-family: 'Ma Shan Zheng', cursive;
      font-size: 1.8rem;
      color: var(--text);
      display: flex;
      align-items: center;
      justify-content: space-between;
    }

    .icon-play {
      font-size: 1.5rem;
      opacity: 0.3;
      transition: opacity 0.2s;
    }

    .sentence-card:hover .icon-play {
      opacity: 1;
    }

    /* 正在朗读的状态 */
    .reading {
      border-color: var(--primary) !important;
      background: #f0f7ff;
    }

    .reading .icon-play {
      color: var(--primary);
      opacity: 1;
      animation: pulse 1s infinite;
    }

    @keyframes pulse {
      0% { transform: scale(1); }
      50% { transform: scale(1.2); }
      100% { transform: scale(1); }
    }

    footer {
      text-align: center;
      margin-top: 40px;
      color: var(--soft);
      font-size: 0.9rem;
    }

    /* 装饰物 */
    .deco {
      position: fixed;
      font-size: 2rem;
      z-index: -1;
      opacity: 0.2;
    }
  </style>
</head>
<body>

  <div class="deco" style="top: 10%; left: 10%;">🍎</div>
  <div class="deco" style="top: 20%; right: 15%;">🦒</div>
  <div class="deco" style="bottom: 15%; left: 12%;">🎨</div>

  <div class="container">
    <header>
      <h1>跟我学中文</h1>
      <p>点击下方卡片，跟着老师读一读</p>
    </header>

    <div id="practice-list">
      <!-- 句子会通过 JS 动态生成 -->
    </div>

    <footer>
      ✨ 提示：建议使用 Chrome 或 Safari 浏览器以获得最佳语音体验
    </footer>
  </div>

  <script>
    // 学习内容数据
    const lessonData = [
      { text: "你饿了吗？", role: "问" },
      { text: "我饿了，我想吃苹果。", role: "答" },
      { text: "你想喝什么？", role: "问" },
      { text: "我想喝牛奶。", role: "答" },
      { text: "你叫什么名字？", role: "问" },
      { text: "我叫小明，很高兴认识你。", role: "答" },
      { text: "今天天气怎么样？", role: "问" },
      { text: "今天是晴天，阳光很好。", role: "答" }
    ];

    const listEl = document.getElementById('practice-list');
    let synth = window.speechSynthesis;
    let currentUtterance = null;

    // 初始化页面
    function init() {
      lessonData.forEach((item, index) => {
        const card = document.createElement('div');
        card.className = 'sentence-card';
        card.innerHTML = `
          <div class="role ${item.role === '问' ? 'role-q' : 'role-a'}">${item.role}</div>
          <div class="content">
            <span>${item.text}</span>
            <span class="icon-play">🔊</span>
          </div>
        `;
        
        card.onclick = () => speak(item.text, card);
        listEl.appendChild(card);
      });
    }

    // 朗读功能
    function speak(text, element) {
      // 如果正在读，先停止
      if (synth.speaking) {
        synth.cancel();
        document.querySelectorAll('.sentence-card').forEach(c => c.classList.remove('reading'));
      }

      const msg = new SpeechSynthesisUtterance(text);
      msg.lang = 'zh-CN';
      msg.rate = 0.8; // 语速稍慢，方便学生跟读
      msg.pitch = 1.1;

      // 自动选择最佳中文语音包
      const voices = synth.getVoices();
      const chineseVoice = voices.find(v => v.lang === 'zh-CN' || v.lang.includes('zh'));
      if (chineseVoice) msg.voice = chineseVoice;

      msg.onstart = () => {
        element.classList.add('reading');
      };

      msg.onend = () => {
        element.classList.remove('reading');
      };

      msg.onerror = () => {
        element.classList.remove('reading');
      };

      synth.speak(msg);
    }

    // 解决部分浏览器语音包异步加载的问题
    if (speechSynthesis.onvoiceschanged !== undefined) {
      speechSynthesis.onvoiceschanged = () => synth.getVoices();
    }

    init();
  </script>
</body>
</html>
