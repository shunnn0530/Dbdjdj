<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>元智機械雙魚男 x 祈媛 - 交往挑戰</title>
    <style>
        body { font-family: sans-serif; background: #fff5f8; display: flex; justify-content: center; padding: 20px; }
        .quiz-container { background: white; padding: 30px; border-radius: 15px; box-shadow: 0 4px 10px rgba(0,0,0,0.1); max-width: 500px; width: 100%; }
        h1 { color: #ff4d94; text-align: center; font-size: 1.5em; }
        .question { margin-bottom: 20px; font-weight: bold; }
        .options button { display: block; width: 100%; padding: 10px; margin: 10px 0; border: 1px solid #ffb3d1; border-radius: 8px; background: white; cursor: pointer; transition: 0.3s; }
        .options button:hover { background: #ffe6f0; }
        .result { text-align: center; font-size: 1.2em; font-weight: bold; margin-top: 20px; display: none; }
    </style>
</head>
<body>

<div class="quiz-container" id="quiz">
    <h1>💖 交往講過的話挑戰 💖</h1>
    <div id="q-box">
        <p class="question" id="question-text"></p>
        <div class="options" id="options-box"></div>
    </div>
    <div class="result" id="result-box"></div>
</div>

<script>
    const questions = [
        { q: "當政誼沒接電話時傳訊說：『我可以把「應該」拿掉』，是指？", a: ["猜祈媛睡過頭了", "猜祈媛去洗澡", "覺得祈媛在打電動"], correct: 0 },
        { q: "政誼口中的「想孤兒」是指想吃？", a: ["炸雞", "有湯的東西", "滷肉飯"], correct: 1 },
        { q: "誰有「腳趾恐懼症」的獨到見解？", a: ["祈媛", "宜蘭小孩政誼", "系學會會長"], correct: 1 },
        { q: "祈媛說自己的五個戶頭加起來只有多少錢？", a: ["2400元", "240元", "24元"], correct: 2 },
        { q: "政誼練舞練到全身濕透稱之為？", a: ["濕背秀", "汗水慶典", "水男孩演出"], correct: 0 }
    ];

    let currentIdx = 0;
    let score = 0;

    function showQuestion() {
        if (currentIdx >= questions.length) {
            document.getElementById('q-box').style.display = 'none';
            const res = document.getElementById('result-box');
            res.style.display = 'block';
            res.innerText = `測驗結束！你的得分：${score}/${questions.length}\n${score == questions.length ? '你們絕對是真愛！' : '再多去吃幾次涮乃葉複習對話吧！'}`;
            return;
        }
        const item = questions[currentIdx];
        document.getElementById('question-text').innerText = (currentIdx + 1) + ". " + item.q;
        const optBox = document.getElementById('options-box');
        optBox.innerHTML = "";
        item.a.forEach((text, i) => {
            const btn = document.createElement('button');
            btn.innerText = text;
            btn.onclick = () => checkAnswer(i);
            optBox.appendChild(btn);
        });
    }

    function checkAnswer(idx) {
        if (idx === questions[currentIdx].correct) score++;
        currentIdx++;
        showQuestion();
    }

    showQuestion();
</script>

</body>
</html>
