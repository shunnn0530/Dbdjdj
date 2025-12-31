<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>交往講過的話挑戰 - 進階版</title>
    <style>
        :root { --main-pink: #ff85a2; --bg-pink: #fff0f3; }
        body { font-family: "Microsoft JhengHei", sans-serif; background: var(--bg-pink); display: flex; justify-content: center; padding: 20px; line-height: 1.6; }
        .quiz-container { background: white; padding: 30px; border-radius: 20px; box-shadow: 0 10px 25px rgba(255,133,162,0.2); max-width: 600px; width: 100%; }
        h1 { color: var(--main-pink); text-align: center; border-bottom: 2px dashed var(--main-pink); padding-bottom: 10px; }
        .progress { text-align: right; color: #888; font-size: 0.9em; margin-bottom: 10px; }
        .question { font-size: 1.2em; font-weight: bold; margin-bottom: 20px; color: #444; }
        .options button { display: block; width: 100%; padding: 12px; margin: 8px 0; border: 2px solid #ffe0e6; border-radius: 12px; background: white; cursor: pointer; font-size: 1em; transition: all 0.2s; }
        .options button:hover { background: #fff9fa; border-color: var(--main-pink); }
        .options button.wrong { background: #ffccd5; border-color: #ff4d6d; }
        .options button.correct { background: #c1f2c1; border-color: #2d6a4f; }
        
        #hint-box { display: none; background: #fff4ce; padding: 10px; border-left: 5px solid #fbc02d; margin: 15px 0; border-radius: 4px; color: #856404; }
        #explain-box { display: none; background: #e7f3ff; padding: 15px; border-radius: 12px; border: 1px solid #b3d7ff; margin-top: 20px; }
        .context-text { font-style: italic; color: #555; background: white; padding: 10px; border-radius: 8px; margin-top: 10px; white-space: pre-line; font-size: 0.9em; }
        
        .next-btn { display: none; width: 100%; padding: 15px; background: var(--main-pink); color: white; border: none; border-radius: 12px; cursor: pointer; font-weight: bold; margin-top: 15px; }
        .next-btn:hover { background: #ff5c8a; }
        .final-result { text-align: center; display: none; }
        .final-result h2 { color: var(--main-pink); }
    </style>
</head>
<body>

<div class="quiz-container">
    <h1>💖 交往挑戰：回憶考驗 💖</h1>
    <div id="game-area">
        <div class="progress" id="progress">Question 1 / 15</div>
        <div class="question" id="q-text">載入中...</div>
        <div id="hint-box"></div>
        <div class="options" id="opt-container"></div>
        <div id="explain-box">
            <b id="ans-title">正確答案是：</b>
            <div class="context-text" id="context-content"></div>
        </div>
        <button class="next-btn" id="next-button" onclick="nextQuestion()">點擊繼續下一題 ✨</button>
    </div>

    <div class="final-result" id="final-area">
        <h2>測驗結束！</h2>
        <p id="score-text"></p>
        <button class="next-btn" style="display:block" onclick="location.reload()">再玩一次</button>
    </div>
</div>

<script>
    const quizData = [
        {
            q: "政誼沒接到電話後說「我可以把『應該』拿掉」，原本他是猜祈媛在幹嘛？",
            options: ["去洗澡", "在睡覺", "在忙系學會", "在打電動"],
            correct: 1,
            hint: "雙魚座的直覺，通常跟『昏睡』有關...",
            context: "政誼：『看來我可以把應該拿掉』\n政誼：『你剛剛應該在睡覺』\n（那時祈媛真的睡著了沒接到電話XD）"
        },
        {
            q: "政誼口中的「想孤兒」到底是什麼意思？",
            options: ["想一個人待著", "學弟沒有去找他", "想去育幼院服務", "想找媽媽"],
            correct: 1,
            hint: "我好冷，好難過，我是孤兒",
            context: "政誼：『我現在想（像）孤兒』""政誼：『我現在ㄒ孤兒』""政誼：『我在\\n兒』\        },
        {
            q: "關於「腳趾恐懼症」，政誼覺得是哪些人的特徵？",
            options: ["宜蘭人", "南部的老人", "偏北部的大部分人", "機械系的男生"],
            correct: 2,
            hint: "跟下雨天要不要穿拖鞋有關！",
            context: "政誼：『偏北部的大部分人都有腳趾恐懼症』\n（因為大家下雨都不愛穿拖鞋，怕腳趾露出來？）"
        },
        {
            q: "祈媛說自己的五個戶頭加起來剩多少錢？",
            options: ["24元", "240元", "2400元", "2.4元"],
            correct: 0,
            hint: "非常慘澹的數字，連買杯飲料都不夠...",
            context: "祈媛：『我有五個戶頭然後只有24塊』\n政誼：『哈哈哈哈太慘了吧』"
        },
        {
            q: "政誼說的「濕背秀」是在什麼情境下發生的？",
            options: ["洗澡給祈媛看", "練舞練到全身濕透", "游泳比賽", "下大雨沒撐傘"],
            correct: 1,
            hint: "Special 的諧音，充滿了汗水！",
            context: "政誼：『看，濕背秀』\n（附上一張練完活動，衣服全部濕掉的照片）"
        },
        {
            q: "為什麼祈媛要求政誼寫「1000字的檢討」？",
            options: ["因為他忘了紀念日", "因為他跟女生聊天", "因為他掛祈媛電話", "因為他太晚回家"],
            correct: 2,
            hint: "發生在凌晨 00:11 分的慘劇...",
            context: "祈媛：『你掛我電話！！！！！』\n祈媛：『沒有1000字的檢討我是不會沒有難過的』"
        },
        {
            q: "政誼被戲稱「學校 Uber Eat」是因為他在忙什麼？",
            options: ["真的在兼差送外送", "幫祈媛買午餐", "幫系學會跑公文", "在校園巡邏"],
            correct: 2,
            hint: "機械系大三系學會的忙碌日常...",
            context: "祈媛：『學校 Uber Eat』\n政誼：『對，我在跑公文...』"
        },
        {
            q: "政誼吃完烤肉後說自己是「球狀的男朋朋」，是因為？",
            options: ["他穿得很圓", "他吃太飽肚子很大", "他在玩排球", "他想縮成一團"],
            correct: 1,
            hint: "中秋節過後的遺憾...",
            context: "政誼：『你得到一個球狀的男朋朋』\n（因為中秋烤肉吃太撐了）"
        },
        {
            q: "祈媛去機械系窩抽小卡，第幾次抽到政誼？",
            options: ["抽了10次", "第1次就抽到", "抽到最後一個才是", "根本沒抽到"],
            correct: 1,
            hint: "這就是真愛的運氣！",
            context: "祈媛：『抽到你才可以回家』\n（結果第一張就是政誼，命中註定！）"
        },
        {
            q: "政誼說宜蘭人對「淋雨」的標準是？",
            options: ["內褲濕掉才算淋雨", "飄雨根本不算淋雨", "看到彩虹才算淋雨", "沒帶傘就算淋雨"],
            correct: 1,
            hint: "宜蘭小孩的傲氣！",
            context: "政誼：『這點飄雨對宜蘭小孩來說根本不算淋雨』"
        },
        {
            q: "祈媛曾說要「幫你扁他」，那個「他」是誰？",
            options: ["系學會長", "排球隊隊長", "路邊的狗", "前男友"],
            correct: 0,
            hint: "政誼當時被系上的事務弄到很不爽...",
            context: "政誼在抱怨系學會的事，祈媛：『幫你扁他（會長）』"
        },
        {
            q: "「地瓜狗」這個詞是用來形容什麼？",
            options: ["政誼養的狗", "一個醜醜的地瓜球貼圖/食物", "祈媛的綽號", "機械系的教授"],
            correct: 1,
            hint: "看起來有點像狗又有點像食物的奇怪東西...",
            context: "對話中提到某個長得像狗的地瓜球，被兩人笑很久。"
        },
        {
            q: "祈媛去載政誼時，忘了車牌號碼，只記得開頭是什麼？",
            options: ["94什麼的", "87什麼的", "13什麼的", "52什麼的"],
            correct: 2,
            hint: "就在聊到要借機車去載人的時候...",
            context: "祈媛：『我忘了車牌...好像是13什麼的』"
        },
        {
            q: "政誼說他的腳「歪掉了」、「舉不起來」是因為？",
            options: ["被球打到", "練舞/活動練到腿軟拉傷", "睡覺抽筋", "跳舞太用力破皮"],
            correct: 1,
            hint: "宿營活動期間的職業傷害...",
            context: "政誼：『我的腳歪掉了...左手也拉傷舉不起來』"
        },
        {
            q: "兩人在聊「誰吃誰」的邏輯時，祈媛覺得哪種說法很瞎？",
            options: ["大魚吃小魚", "學長吃學妹", "學妹吃學長", "雙魚吃排球"],
            correct: 2,
            hint: "祈媛覺得這個語音邏輯聽不下去...",
            context: "祈媛：『學妹吃學長這說法有人聽得下去嗎？』"
        }
    ];

    let currentQ = 0;
    let score = 0;
    let wrongCount = 0;

    function loadQuestion() {
        const data = quizData[currentQ];
        document.getElementById('progress').innerText = `Question ${currentQ + 1} / ${quizData.length}`;
        document.getElementById('q-text').innerText = data.q;
        document.getElementById('hint-box').style.display = 'none';
        document.getElementById('explain-box').style.display = 'none';
        document.getElementById('next-button').style.display = 'none';
        
        const container = document.getElementById('opt-container');
        container.innerHTML = '';
        wrongCount = 0;

        data.options.forEach((opt, idx) => {
            const btn = document.createElement('button');
            btn.innerText = opt;
            btn.onclick = () => checkAnswer(idx, btn);
            container.appendChild(btn);
        });
    }

    function checkAnswer(choice, btn) {
        const data = quizData[currentQ];
        
        if (choice === data.correct) {
            btn.classList.add('correct');
            if (wrongCount === 0) score++;
            disableAllButtons();
            showExplanation(true);
        } else {
            wrongCount++;
            btn.classList.add('wrong');
            btn.disabled = true;
            
            if (wrongCount === 1) {
                const hintBox = document.getElementById('hint-box');
                hintBox.innerText = "💡 提示： " + data.hint;
                hintBox.style.display = 'block';
            } else {
                disableAllButtons();
                showExplanation(false);
            }
        }
    }

    function disableAllButtons() {
        const btns = document.querySelectorAll('#opt-container button');
        btns.forEach(b => b.disabled = true);
    }

    function showExplanation(isCorrect) {
        const data = quizData[currentQ];
        const expBox = document.getElementById('explain-box');
        const title = document.getElementById('ans-title');
        const content = document.getElementById('context-content');
        
        expBox.style.display = 'block';
        title.innerText = isCorrect ? "✅ 答對了！當時的情況：" : `❌ 哎呀，正解是「${data.options[data.correct]}」：`;
        content.innerText = data.context;
        document.getElementById('next-button').style.display = 'block';
    }

    function nextQuestion() {
        currentQ++;
        if (currentQ < quizData.length) {
            loadQuestion();
        } else {
            showFinalResult();
        }
    }

    function showFinalResult() {
        document.getElementById('game-area').style.display = 'none';
        const finalArea = document.getElementById('final-area');
        finalArea.style.display = 'block';
        let comment = "";
        if (score >= 13) comment = "你們是彼此的靈魂寫手吧！閃瞎我了 😎";
        else if (score >= 8) comment = "很了解彼此喔！繼續保持甜蜜 💕";
        else comment = "看來聊天記錄要多翻幾次了，快去約會補課！🏃‍♂️";
        
        document.getElementById('score-text').innerHTML = `你的得分：${score} / ${quizData.length}<br><br>${comment}`;
    }

    loadQuestion();
</script>

</body>
</html>
