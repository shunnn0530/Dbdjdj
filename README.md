<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>交往挑戰 - 50題回憶修正版</title>
    <style>
        :root { --main-pink: #ff85a2; --bg-pink: #fff0f3; --correct-green: #d1f7d1; --wrong-red: #ffdce0; }
        body { font-family: "Microsoft JhengHei", sans-serif; background: var(--bg-pink); display: flex; justify-content: center; padding: 20px; }
        .quiz-container { background: white; padding: 25px; border-radius: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.1); max-width: 650px; width: 100%; }
        h1 { color: var(--main-pink); text-align: center; font-size: 1.4em; border-bottom: 2px solid var(--bg-pink); padding-bottom: 10px; }
        .progress { text-align: center; color: #888; font-weight: bold; margin-bottom: 15px; }
        .question { font-size: 1.1em; font-weight: bold; margin-bottom: 20px; color: #333; min-height: 2.5em; }
        .options button { display: block; width: 100%; padding: 12px; margin: 8px 0; border: 2px solid #f0f0f0; border-radius: 12px; background: white; cursor: pointer; transition: 0.2s; text-align: left; }
        .options button:hover { border-color: var(--main-pink); background: #fff9fa; }
        .options button.wrong { background: var(--wrong-red); border-color: #ff4d6d; }
        .options button.correct { background: var(--correct-green); border-color: #2d6a4f; }
        #hint-box { display: none; background: #fffdf0; padding: 12px; border-left: 5px solid #ffd54f; margin: 15px 0; color: #7f6d00; font-size: 0.9em; border-radius: 8px; }
        #explain-box { display: none; background: #f0f7ff; padding: 15px; border-radius: 12px; border: 1px solid #cce5ff; margin-top: 15px; }
        .context-text { font-style: italic; color: #555; background: white; padding: 10px; border-radius: 8px; margin-top: 8px; white-space: pre-line; font-size: 0.9em; border-left: 3px solid #007bff; }
        .next-btn { display: none; width: 100%; padding: 15px; background: var(--main-pink); color: white; border: none; border-radius: 12px; cursor: pointer; font-weight: bold; margin-top: 15px; }
        .final-result { text-align: center; display: none; }
    </style>
</head>
<body>

<div class="quiz-container">
    <h1>💖 交往挑戰：50道回憶考驗 💖</h1>
    <div id="game-area">
        <div class="progress" id="progress">載入中...</div>
        <div class="question" id="q-text"></div>
        <div id="hint-box"></div>
        <div class="options" id="opt-container"></div>
        <div id="explain-box">
            <b id="ans-title"></b>
            <div class="context-text" id="context-content"></div>
        </div>
        <button class="next-btn" id="next-button" onclick="nextQuestion()">下一題 ✨</button>
    </div>

    <div class="final-result" id="final-area">
        <h2>🎉 挑戰完成！</h2>
        <p id="score-text"></p>
        <button class="next-btn" style="display:block" onclick="location.reload()">重新挑戰</button>
    </div>
</div>

<script>
    const quizData = [
        { q: "政誼沒接電話後說「我可以把『應該』拿掉」，是指什麼？", options: ["祈媛在洗澡", "祈媛在睡覺", "祈媛在生氣", "祈媛在打電動"], correct: 1, hint: "跟睡神有關...", context: "政誼：『看來我可以把應該拿掉，你剛剛應該在睡覺。』" },
        { q: "政誼打錯字說「我現在想孤兒」，當時他是在幹嘛？", options: ["在吃三媽臭臭鍋", "在育幼院當志工", "在樓下等學弟一個人站著", "在宿舍想媽媽"], correct: 2, hint: "形容那種被丟下的淒涼感...", context: "政誼：『我現在想（像）孤兒』\n因為他一個人在樓下等學弟等不到，覺得自己像孤兒一樣沒人理。" },
        { q: "關於「腳趾恐懼症」，政誼覺得這是哪裡人的特點？", options: ["宜蘭人", "南部人", "偏北部的大部分人", "機械系男生"], correct: 2, hint: "下雨天不愛露腳趾...", context: "政誼：『偏北部的大部分人都有腳趾恐懼症。』" },
        { q: "祈媛曾自爆她的五個戶頭加起來剩下多少錢？", options: ["2.4元", "24元", "240元", "2400元"], correct: 1, hint: "非常淒慘的數字...", context: "祈媛：『我有五個戶頭然後只有24塊。』" },
        { q: "政誼傳了一張全身濕透的照片，並稱之為什麼？", options: ["水男孩", "落湯雞", "濕背秀", "爆汗王"], correct: 2, hint: "Special 的諧音...", context: "政誼：『看，濕背秀。』" },
        { q: "祈媛要政誼寫「1000字檢討」是因為他做了什麼？", options: ["忘了吃晚餐", "掛她電話", "太晚回訊息", "遲到一小時"], correct: 1, hint: "發生在凌晨 00:11 的事...", context: "祈媛：『你掛我電話！！沒有1000字的檢討我是不會沒有難過的。』" },
        { q: "政誼被戲稱「學校 Uber Eat」的原因是？", options: ["他在兼職送外送", "幫祈媛買午餐", "在系學會忙著跑公文", "在校門口等餐點"], correct: 2, hint: "系學會的忙碌生活...", context: "祈媛：『學校 Uber Eat。』政誼：『對，我在跑公文。』" },
        { q: "政誼吃完中秋烤肉後，自稱是什麼形狀的男朋朋？", options: ["方塊狀", "星狀", "球狀", "柱狀"], correct: 2, hint: "肚子吃太飽了...", context: "政誼：『你得到一個球狀的男朋朋。』" },
        { q: "在機械系窩抽小卡，祈媛第幾次就抽到政誼了？", options: ["第1次", "第3次", "第5次", "最後一次"], correct: 0, hint: "絕對是命中註定...", context: "祈媛：『抽到你才可以回家。』結果第1抽就是政誼！" },
        { q: "身為宜蘭人的政誼，覺得什麼樣的雨根本不算淋雨？", options: ["暴雨", "雷陣雨", "飄雨", "酸雨"], correct: 2, hint: "宜蘭小孩的底氣...", context: "政誼：『這點飄雨對宜蘭小孩來說根本不算淋雨。』" },
        { q: "政誼抱怨系學會時，祈媛曾霸氣說要「幫你扁他」，那個「他」是誰？", options: ["路人", "學長", "系學會長", "教授"], correct: 2, hint: "護航男友的祈媛...", context: "政誼在抱怨會長，祈媛：『幫你扁他。』" },
        { q: "「地瓜狗」這個詞在對話中是用來形容什麼？", options: ["政誼的暱稱", "一張醜醜的地瓜球圖/貼圖", "祈媛家的狗", "一種零食"], correct: 1, hint: "看起來像地瓜又像狗...", context: "兩人討論地瓜球長得像狗，稱之為地瓜狗。" },
        { q: "祈媛忘了機車車牌，只記得開頭是什麼數字？", options: ["52", "13", "87", "94"], correct: 1, hint: "就在要去載政誼之前...", context: "祈媛：『我忘了車牌...好像是13什麼的。』" },
        { q: "政誼說腳「歪掉了」且舉不起來，是因為什麼活動？", options: ["打排球", "宿營練活動/練舞", "爬樓梯", "睡午覺"], correct: 1, hint: "系學會活動的職業傷害...", context: "政誼：『我的腳歪掉了...左手也拉傷舉不起來。』" },
        { q: "兩人在聊「誰吃誰」時，祈媛覺得哪種說法有人聽得下去？", options: ["學弟吃學姐", "學長吃學妹", "學妹吃學長", "老師吃學生"], correct: 2, hint: "祈媛覺得這個語音邏輯很瞎...", context: "祈媛：『學妹吃學長這說法有人聽得下去嗎？』" },
        { q: "政誼說「我愛你，木啊」，約定哪一天要整天在一起？", options: ["禮拜一", "禮拜三", "禮拜五", "禮拜六"], correct: 0, hint: "一週的開始...", context: "政誼：『禮拜一可以待在一起一整天。』" },
        { q: "政誼抽小卡的地方在哪裡？", options: ["排球場", "機械系窩", "元智大門", "圖書館"], correct: 1, hint: "机械系的據點...", context: "對話中明確提到在「機械系窩」。" },
        { q: "祈媛誇獎政誼「閃閃發光」是在什麼時候？", options: ["打球的時候", "認真對待活動的時候", "睡覺的時候", "吃烤肉的時候"], correct: 1, hint: "認真的男人最帥...", context: "祈媛：『你認真對待這個活動的時候閃閃發光。』" },
        { q: "凌晨 00:11 分時，政誼在電話中解釋他在幹嘛？", options: ["在睡覺", "在吃烤肉", "在寫作業", "在洗澡"], correct: 1, hint: "這也是掛電話的原因之一...", context: "政誼：『對不起我還在吃。』（那時在吃中秋烤肉）" },
        { q: "政誼說「你都不認識」，是指誰不認識誰？", options: ["他不認識祈媛的朋友", "祈媛不認識那些吃烤肉的人", "大家不認識政誼", "會長不認識祈媛"], correct: 1, hint: "掛電話的解釋...", context: "政誼：『你都不認識（在場的人），我沒有（掛電話）。』" },
        { q: "這份聊天記錄的標題如何描述政誼？", options: ["雙魚排球男", "天蠍籃球男", "雙子熱舞男", "處女羽球男"], correct: 0, hint: "文件檔名...", context: "標題：『元智機械大三系學會雙魚排球男』。" },
        { q: "祈媛在 09:21 分抱怨了什麼尷尬的事？", options: ["穿錯衣服", "沒點到名", "走錯教室", "認錯人"], correct: 1, hint: "早課的痛苦...", context: "祈媛：『ㄜ沒點到名好尷...』" },
        { q: "政誼對「下雨穿拖鞋」的見解是什麼？", options: ["很邋遢", "北部人比較不愛露腳趾", "很方便", "會感冒"], correct: 1, hint: "跟腳趾恐懼症相關...", context: "政誼討論雨天穿鞋習慣的獨到見解。" },
        { q: "「想你，想抱抱，回去要抱著你」是誰對誰說的？", options: ["祈媛對政誼", "政誼對祈媛", "會長對政誼", "學妹對政誼"], correct: 1, hint: "雙魚男的撒嬌...", context: "政誼在忙完活動後對祈媛說的甜蜜語錄。" },
        { q: "祈媛說「因為想見你就跑過去了」，是指她跑去哪？", options: ["宿舍", "球場", "系窩", "家裡"], correct: 2, hint: "去找忙碌的男友...", context: "祈媛分享自己衝動跑去系窩見面的心情。" },
        { q: "政誼在 00:04 分被標記了什麼系統訊息？", options: ["通話時間10分鐘", "您已取消通話", "通話結束", "對方通話中"], correct: 1, hint: "引發檢討書事件的開端...", context: "紀錄顯示：『00:04 祈媛 ☎ 您已取消通話』。" },
        { q: "檢討書風波最後，他們最後一通電話通了多久？", options: ["1分鐘", "4分35秒", "7分54秒", "10分鐘"], correct: 2, hint: "凌晨 00:31 分那一通...", context: "紀錄顯示：『00:31 通話時間 7:54』。" },
        { q: "聊天記錄存檔的日期是哪一天？", options: ["2024/12/31", "2025/12/31", "2025/09/18", "2025/01/01"], correct: 1, hint: "文件最上方資訊...", context: "儲存日期：2025/12/31。" },
        { q: "政誼是元智大學什麼系的？", options: ["電機系", "化工系", "機械系", "資管系"], correct: 2, hint: "元智機械...", context: "他是機械大三的學生。" },
        { q: "聊天紀錄中，祈媛發出「嗚嗚嗚嗚」慘叫是在幾點？", options: ["23:00", "00:11", "09:21", "12:39"], correct: 1, hint: "被掛電話的時候...", context: "00:11 祈媛：『嗚嗚嗚嗚嗚嗚嗚』。" },
        { q: "政誼說「我可以把應該拿掉」，這句話的語氣是？", options: ["生氣", "開玩笑/自信猜對", "難過", "疑惑"], correct: 1, hint: "猜對了女友在睡覺...", context: "政誼對祈媛作息的了解引發的自信發言。" },
        { q: "祈媛說「你有認真看我表演」，這是在哪個活動？", options: ["宿營", "球賽", "系周大會", "期末聚餐"], correct: 0, hint: "系學會最忙的活動...", context: "對話背景圍繞在宿營練習與表演。" },
        { q: "「我喜歡看你投入在這些事情上的樣子」是誰的台詞？", options: ["政誼", "祈媛", "學弟", "教授"], correct: 1, hint: "支持男友的女友...", context: "祈媛給予政誼忙系學會事務的正面鼓勵。" },
        { q: "聊天紀錄中頻率最高的「非文字」內容是什麼？", options: ["[相片]", "[影片]", "[貼圖]", "[語音]"], correct: 2, hint: "兩人的對話充滿表情包...", context: "對話中出現極大量的 [貼圖] 標註。" },
        { q: "政誼在 00:16 分與祈媛通話了多久？", options: ["1分鐘", "4分35秒", "7分54秒", "20秒"], correct: 1, hint: "檢討書風波中的第一通...", context: "00:16 ☎ 通話時間 4:35。" },
        { q: "政誼對於「飄雨」不穿雨具的習慣，跟他哪裡的成長背景有關？", options: ["基隆", "宜蘭", "台北", "花蓮"], correct: 1, hint: "雨水之都的孩子...", context: "政誼自稱『宜蘭小孩』。" },
        { q: "祈媛要求政誼寫檢討書，她說「沒有檢討書我是不會...」？", options: ["吃飯的", "睡覺的", "沒有難過的", "回訊息的"], correct: 2, hint: "一個有點可愛的反話邏輯...", context: "祈媛：『沒有1000字的檢討我是不會沒有難過的』。" },
        { q: "政誼說左手拉傷「舉不起來」，那右手呢？", options: ["也受傷了", "沒事", "沒提到", "斷掉了"], correct: 2, hint: "他只特別提了左手跟腳...", context: "對話只提到左手拉傷舉不起來。" },
        { q: "祈媛覺得「學妹吃學長」這說法瞎，那「學長吃學妹」呢？", options: ["也瞎", "沒提到", "很正常", "更瞎"], correct: 1, hint: "她只針對其中一種說法質疑...", context: "她只問學妹吃學長這說法有人聽得下去嗎。" },
        { q: "政誼提到「想孤兒」時，祈媛回了什麼？", options: ["可憐喔", "？", "哈哈", "抱抱"], correct: 1, hint: "因為他打錯字，她很困惑...", context: "祈媛回了一個問號『？』。" },
        { q: "這份挑戰的檔案存儲日期 2025/12/31 是星期幾？", options: ["三", "四", "五", "六"], correct: 0, hint: "查查看日曆...", context: "2025/12/31 是星期三。" },
        { q: "政誼是系學會第幾屆或幾年級？", options: ["大二", "大三", "大四", "研一"], correct: 1, hint: "元智機械...", context: "元智機械大三系學會。" },
        { q: "政誼在吃烤肉被抓包沒接電話時，他的第一句話是？", options: ["我沒掛", "對不起", "我還在吃", "哈哈"], correct: 1, hint: "先道歉再說...", context: "00:11 政誼：『對不起』。" },
        { q: "祈媛說「你有閃閃發光」，政誼當下的表現是？", options: ["敷衍", "很棒很認真", "在打球", "在偷懶"], correct: 1, hint: "認真對待活動的時候...", context: "這是在誇獎政誼對活動的投入程度。" },
        { q: "政誼說「我可以把應該拿掉」，「應該」原本是用來形容？", options: ["祈媛吃飯", "祈媛睡覺", "祈媛打球", "祈媛遲到"], correct: 1, hint: "猜她沒接電話的原因...", context: "原本猜：『你剛剛應該在睡覺』。" },
        { q: "兩人聊天紀錄開始的第一天日期是？", options: ["2024/09/18", "2025/09/18", "2025/12/31", "2025/01/01"], correct: 1, hint: "文件最開始的日期...", context: "2025/09/18（四）。" },
        { q: "這場挑戰的主角是誰？", options: ["政誼 & 祈媛", "正義 & 啟元", "政一 & 祈媛", "政誼 & 齊元"], correct: 0, hint: "名字別打錯...", context: "政誼與祈媛。" },
        { q: "聊天紀錄結束在 09:21，祈媛傳了什麼？", options: ["[貼圖]", "我醒了", "沒點到名好尷...", "早安"], correct: 2, hint: "最後一句話...", context: "09:21 祈媛：『ㄜ沒點到名好尷...』" },
        { q: "政誼說他「左手拉傷舉不起來」，但他還有體力對祈媛說？", options: ["想睡覺", "想你抱抱", "想吃烤肉", "想去宜蘭"], correct: 1, hint: "就算累也要求抱抱...", context: "政誼撒嬌說想你，回去要抱著你。" },
        { q: "最後一題：你覺得製作這個測驗的人是誰？", options: ["政誼", "祈媛", "這是一個AI助手", "地瓜狗"], correct: 2, hint: "當然是幫你寫程式的我啊...", context: "這是我幫你們製作的專屬挑戰！" }
    ];

    let currentQ = 0;
    let score = 0;
    let wrongCount = 0;

    function loadQuestion() {
        const data = quizData[currentQ];
        document.getElementById('progress').innerText = `第 ${currentQ + 1} 題 / 共 ${quizData.length} 題`;
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
        document.querySelectorAll('#opt-container button').forEach(b => b.disabled = true);
    }

    function showExplanation(isCorrect) {
        const data = quizData[currentQ];
        const expBox = document.getElementById('explain-box');
        const title = document.getElementById('ans-title');
        const content = document.getElementById('context-content');
        expBox.style.display = 'block';
        title.innerText = isCorrect ? "✅ 答對了！" : `❌ 答錯了，正解是「${data.options[data.correct]}」`;
        content.innerText = "【對話脈絡】\n" + data.context;
        document.getElementById('next-button').style.display = 'block';
    }

    function nextQuestion() {
        currentQ++;
        if (currentQ < quizData.length) loadQuestion();
        else showFinalResult();
    }

    function showFinalResult() {
        document.getElementById('game-area').style.display = 'none';
        const finalArea = document.getElementById('final-area');
        finalArea.style.display = 'block';
        let msg = score >= 45 ? "你們是靈魂伴侶！細節王是你 👑" : score >= 30 ? "很了解彼此耶，超甜蜜的 💕" : "看來需要重讀一遍對話紀錄喔 🏃‍♂️";
        document.getElementById('score-text').innerHTML = `得分：${score} / ${quizData.length}<br>${msg}`;
    }

    loadQuestion();
</script>

</body>
</html>
