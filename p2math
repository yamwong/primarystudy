<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>生物還是非生物？</title>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Load Tone.js for sound effects -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/tone/14.8.49/Tone.js"></script>
    <!-- Load Google Fonts for a cute rounded look -->
    <link href="https://fonts.googleapis.com/css2?family=M+PLUS+Rounded+1c:wght@400;700;900&display=swap" rel="stylesheet">
    <!-- 加入 Twemoji 將符號強制轉換為高品質圖片 (更換為 jsdelivr 來源以提高穩定性) -->
    <script src="https://unpkg.com/twemoji@14.0.2/dist/twemoji.min.js"></script>
    
    <style>
        body {
            font-family: 'M PLUS Rounded 1c', sans-serif;
            background-color: #f0fdf4; /* Light green background */
            background-image: radial-gradient(#bbf7d0 2px, transparent 2px);
            background-size: 30px 30px;
            overflow: hidden; /* Prevent scrolling */
            touch-action: manipulation; /* Better touch handling */
        }

        .game-container {
            max-width: 600px;
            margin: 0 auto;
            height: 100vh;
            display: flex;
            flex-direction: column;
        }

        /* Bubbly Buttons */
        .btn-bubbly {
            transition: transform 0.1s cubic-bezier(0.175, 0.885, 0.32, 1.275), box-shadow 0.1s;
            box-shadow: 0 8px 0 rgba(0,0,0,0.15);
        }
        .btn-bubbly:active {
            transform: translateY(8px);
            box-shadow: 0 0px 0 rgba(0,0,0,0.15);
        }

        /* Animations */
        @keyframes popIn {
            0% { transform: scale(0.5); opacity: 0; }
            80% { transform: scale(1.1); opacity: 1; }
            100% { transform: scale(1); opacity: 1; }
        }

        @keyframes bounceGently {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }

        @keyframes starSpin {
            0% { transform: scale(0) rotate(0deg); opacity: 1; }
            50% { transform: scale(1.5) rotate(180deg); opacity: 1; }
            100% { transform: scale(2) rotate(360deg); opacity: 0; }
        }

        .animate-pop {
            animation: popIn 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }

        .bounce-element {
            animation: bounceGently 2s infinite ease-in-out;
        }

        .star-particle {
            position: absolute;
            font-size: 3rem;
            pointer-events: none;
            z-index: 50;
            animation: starSpin 0.8s ease-out forwards;
        }

        /* Feedback Overlay overlay */
        .feedback-overlay {
            position: absolute;
            inset: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 40;
            pointer-events: none; /* Let clicks pass through if needed, but we manage via JS */
        }

        /* 確保 Twemoji 產生的圖片可以適應大尺寸並且不會被當成文字選取 */
        img.emoji {
            height: 1em;
            width: 1em;
            margin: 0 .05em 0 .1em;
            vertical-align: -0.1em;
            display: inline-block;
            pointer-events: none;
        }
    </style>
</head>
<body class="text-gray-800 selection:bg-green-200">

    <!-- Start Screen -->
    <div id="startScreen" class="fixed inset-0 bg-green-500 bg-opacity-95 flex flex-col justify-center items-center z-50 p-4 text-center">
        <h1 class="text-5xl md:text-6xl font-black text-white mb-6 drop-shadow-lg">🌟 生物分類小遊戲 🌟</h1>
        <p class="text-2xl text-white mb-8 font-bold bg-green-600 p-4 rounded-3xl shadow-inner">
            這是生物還是非生物呢？<br>
            來挑戰 20 題吧！
        </p>
        <button id="startBtn" class="btn-bubbly bg-yellow-400 text-yellow-900 text-4xl font-black py-6 px-12 rounded-full border-4 border-yellow-200 hover:bg-yellow-300">
            開始遊戲 ▶️
        </button>
    </div>

    <!-- Main Game UI -->
    <div class="game-container p-4">
        
        <!-- Header: Title and Score -->
        <header class="flex justify-between items-center mb-6 mt-4">
            <h2 class="text-2xl md:text-3xl font-black text-green-700 bg-white px-4 py-2 rounded-2xl shadow-md border-2 border-green-200" id="progressDisplay">
                第 1 題
            </h2>
            <div class="flex items-center bg-white px-5 py-2 rounded-2xl shadow-md border-2 border-yellow-200">
                <span class="text-3xl mr-2">⭐</span>
                <span class="text-2xl md:text-3xl font-black text-yellow-600" id="scoreDisplay">分數: 0</span>
            </div>
        </header>

        <!-- Game Stage: Showing the item -->
        <main class="flex-1 flex flex-col justify-center items-center relative">
            
            <!-- The Card holding the Emoji -->
            <div id="itemCard" class="bg-white rounded-[3rem] shadow-2xl border-8 border-white p-8 w-full max-w-sm flex flex-col items-center justify-center aspect-square animate-pop">
                <div id="itemEmoji" class="text-[8rem] md:text-[10rem] leading-none mb-4 bounce-element select-none">
                    🐶
                </div>
                <div id="itemName" class="text-4xl md:text-5xl font-black text-gray-700 text-center select-none">
                    小狗
                </div>
            </div>

            <!-- Feedback Message (Hidden by default) -->
            <div id="feedbackBox" class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 opacity-0 transition-opacity duration-300 z-30 pointer-events-none">
                <div id="feedbackText" class="text-5xl font-black text-white px-8 py-4 rounded-full shadow-2xl border-4 border-white">
                    答對了！
                </div>
            </div>
        </main>

        <!-- Controls: Living / Non-Living Buttons -->
        <footer class="mt-8 mb-8 flex justify-center gap-4 md:gap-8 w-full">
            <button id="btnLiving" class="btn-bubbly flex-1 max-w-[200px] bg-green-400 border-4 border-green-200 rounded-3xl py-6 flex flex-col items-center justify-center overflow-hidden relative group">
                <div class="absolute inset-0 bg-white opacity-0 group-hover:opacity-20 transition-opacity"></div>
                <span class="text-5xl mb-2">🌿</span>
                <span class="text-3xl md:text-4xl font-black text-green-900 drop-shadow-sm">生 物</span>
            </button>
            
            <button id="btnNonLiving" class="btn-bubbly flex-1 max-w-[200px] bg-blue-400 border-4 border-blue-200 rounded-3xl py-6 flex flex-col items-center justify-center overflow-hidden relative group">
                <div class="absolute inset-0 bg-white opacity-0 group-hover:opacity-20 transition-opacity"></div>
                <span class="text-5xl mb-2">🤖</span>
                <span class="text-3xl md:text-4xl font-black text-blue-900 drop-shadow-sm">非生物</span>
            </button>
        </footer>
    </div>

    <!-- Victory Screen -->
    <div id="victoryScreen" class="fixed inset-0 bg-yellow-400 bg-opacity-95 flex flex-col justify-center items-center z-50 p-4 text-center hidden">
        <h1 class="text-6xl md:text-7xl font-black text-white mb-6 drop-shadow-lg">🎉 太棒了！ 🎉</h1>
        <p class="text-3xl text-white mb-8 font-bold bg-yellow-500 p-6 rounded-3xl shadow-inner border-4 border-yellow-300">
            你完成了所有的題目！<br>
            你一共答對了 <span id="finalScore" class="text-5xl text-red-500 font-black">20</span> 題！<br>
            <span class="text-5xl mt-4 block">🏆</span>
        </p>
        <button id="restartBtn" class="btn-bubbly bg-white text-yellow-600 text-4xl font-black py-6 px-12 rounded-full border-4 border-yellow-200 hover:bg-gray-100">
            再玩一次 🔄
        </button>
    </div>

    <script>
        // Data for the game (擴充為 40 個不同的物品，涵蓋動植物與各種生活用品)
        const allGameItems = [
            // 生物 (Living Things)
            { name: '小狗', emoji: '🐶', type: 'living' },
            { name: '小貓', emoji: '🐱', type: 'living' },
            { name: '大樹', emoji: '🌳', type: 'living' },
            { name: '向日葵', emoji: '🌻', type: 'living' },
            { name: '大象', emoji: '🐘', type: 'living' },
            { name: '小魚', emoji: '🐟', type: 'living' },
            { name: '男孩', emoji: '👦', type: 'living' },
            { name: '女孩', emoji: '👧', type: 'living' },
            { name: '蝴蝶', emoji: '🦋', type: 'living' },
            { name: '小鳥', emoji: '🐦', type: 'living' },
            { name: '青蛙', emoji: '🐸', type: 'living' },
            { name: '蘋果樹', emoji: '🍎', type: 'living' }, 
            { name: '烏龜', emoji: '🐢', type: 'living' },
            { name: '螞蟻', emoji: '🐜', type: 'living' },
            { name: '蝸牛', emoji: '🐌', type: 'living' },
            { name: '長頸鹿', emoji: '🦒', type: 'living' },
            { name: '企鵝', emoji: '🐧', type: 'living' },
            { name: '蜜蜂', emoji: '🐝', type: 'living' },
            { name: '仙人掌', emoji: '🌵', type: 'living' },
            { name: '老虎', emoji: '🐅', type: 'living' },
            
            // 非生物 (Non-Living Things)
            { name: '汽車', emoji: '🚗', type: 'nonliving' },
            { name: '足球', emoji: '⚽', type: 'nonliving' },
            { name: '書本', emoji: '📚', type: 'nonliving' },
            { name: '椅子', emoji: '🪑', type: 'nonliving' },
            { name: '手機', emoji: '📱', type: 'nonliving' },
            { name: '氣球', emoji: '🎈', type: 'nonliving' },
            { name: '泰迪熊', emoji: '🧸', type: 'nonliving' },
            { name: '單車', emoji: '🚲', type: 'nonliving' },
            { name: '時鐘', emoji: '🕰️', type: 'nonliving' },
            { name: '雨傘', emoji: '☂️', type: 'nonliving' },
            { name: '衣服', emoji: '👕', type: 'nonliving' },
            { name: '飛機', emoji: '✈️', type: 'nonliving' },
            { name: '電視', emoji: '📺', type: 'nonliving' },
            { name: '石頭', emoji: '🪨', type: 'nonliving' },
            { name: '雲朵', emoji: '☁️', type: 'nonliving' },
            { name: '水滴', emoji: '💧', type: 'nonliving' },
            { name: '電腦', emoji: '💻', type: 'nonliving' },
            { name: '鉛筆', emoji: '✏️', type: 'nonliving' },
            { name: '剪刀', emoji: '✂️', type: 'nonliving' },
            { name: '水壺', emoji: '🫙', type: 'nonliving' }
        ];

        // Game State
        let currentScore = 0;
        let questionCount = 0; // 當前回答的題數
        let activeItem = null;
        let isProcessing = false; // Prevent multiple clicks
        const TOTAL_QUESTIONS = 20; // Items needed to win
        let currentDeck = []; // 用來存放洗牌後的題目

        // Synths for sound effects
        let correctSynth, wrongSynth;

        // DOM Elements
        const startScreen = document.getElementById('startScreen');
        const startBtn = document.getElementById('startBtn');
        const victoryScreen = document.getElementById('victoryScreen');
        const restartBtn = document.getElementById('restartBtn');
        const itemCard = document.getElementById('itemCard');
        const itemEmoji = document.getElementById('itemEmoji');
        const itemName = document.getElementById('itemName');
        const scoreDisplay = document.getElementById('scoreDisplay');
        const progressDisplay = document.getElementById('progressDisplay');
        const finalScoreDisplay = document.getElementById('finalScore');
        const btnLiving = document.getElementById('btnLiving');
        const btnNonLiving = document.getElementById('btnNonLiving');
        const feedbackBox = document.getElementById('feedbackBox');
        const feedbackText = document.getElementById('feedbackText');

        // 設定 Twemoji 的圖片來源
        const twemojiConfig = {
            base: 'https://cdnjs.cloudflare.com/ajax/libs/twemoji/14.0.2/',
            folder: 'svg',
            ext: '.svg'
        };

        // 安全地解析 Emoji 的函數，改成直接寫入 HTML，這是最穩定不會出錯的方法
        function setEmoji(element, textString) {
            if (typeof twemoji !== 'undefined') {
                element.innerHTML = twemoji.parse(textString, twemojiConfig);
            } else {
                element.textContent = textString;
            }
        }

        // Fisher-Yates Shuffle 洗牌演算法
        function shuffleArray(array) {
            let currentIndex = array.length, randomIndex;
            while (currentIndex != 0) {
                randomIndex = Math.floor(Math.random() * currentIndex);
                currentIndex--;
                [array[currentIndex], array[randomIndex]] = [array[randomIndex], array[currentIndex]];
            }
            return array;
        }

        function initAudio() {
            // Tone.js requires user interaction to start AudioContext
            Tone.start();
            
            // Cheerful synth for correct answers
            correctSynth = new Tone.Synth({
                oscillator: { type: 'sine' },
                envelope: { attack: 0.01, decay: 0.2, sustain: 0.2, release: 1 }
            }).toDestination();

            // Low boop synth for wrong answers
            wrongSynth = new Tone.MembraneSynth({
                pitchDecay: 0.05,
                octaves: 2,
                oscillator: { type: 'square' },
                envelope: { attack: 0.01, decay: 0.4, sustain: 0.01, release: 1.4 }
            }).toDestination();
        }

        function playCorrectSound() {
            if(correctSynth) {
                const now = Tone.now();
                correctSynth.triggerAttackRelease("C5", "8n", now);
                correctSynth.triggerAttackRelease("E5", "8n", now + 0.15);
                correctSynth.triggerAttackRelease("G5", "4n", now + 0.3);
            }
        }

        function playWrongSound() {
            if(wrongSynth) {
                wrongSynth.triggerAttackRelease("C2", "8n");
            }
        }

        function startGame() {
            initAudio();
            startScreen.classList.add('hidden');
            victoryScreen.classList.add('hidden');
            currentScore = 0;
            questionCount = 0;
            
            // 複製所有題目並洗牌，保證不重複
            currentDeck = shuffleArray([...allGameItems]);
            
            updateScoreDisplay();
            nextQuestion();
        }

        function getNextItem() {
            // 如果牌堆空了 (雖然設定上只抽20題，這裡加個保險)，重新洗牌
            if (currentDeck.length === 0) {
                currentDeck = shuffleArray([...allGameItems]);
            }
            // 從牌堆頂端抽出一張牌
            return currentDeck.pop();
        }

        function nextQuestion() {
            isProcessing = false;
            questionCount++;
            activeItem = getNextItem();
            
            progressDisplay.textContent = `第 ${questionCount} / ${TOTAL_QUESTIONS} 題`;
            
            // 停止卡片上所有正在進行的動態 (修正第二張卡片可能會被隱藏的問題)
            itemCard.getAnimations().forEach(anim => anim.cancel());
            itemCard.style.animation = 'none';
            itemCard.offsetHeight; /* trigger reflow */
            itemCard.style.animation = null; 
            
            itemCard.classList.remove('opacity-0');
            itemCard.style.transform = 'scale(1)';
            itemCard.classList.add('animate-pop');
            
            // 更新內容並強制轉換為圖片
            setEmoji(itemEmoji, activeItem.emoji);
            itemName.textContent = activeItem.name;
        }

        function updateScoreDisplay() {
            scoreDisplay.textContent = `分數: ${currentScore}`;
        }

        function createStars() {
            for(let i=0; i<5; i++) {
                const star = document.createElement('div');
                star.className = 'star-particle';
                star.style.left = `${40 + Math.random() * 20}%`;
                star.style.top = `${40 + Math.random() * 20}%`;
                // Randomize trajectory slightly
                star.style.transformOrigin = `${(Math.random()-0.5)*200}px ${(Math.random()-0.5)*200}px`;
                document.body.appendChild(star);
                
                // 將特效中的星星也轉為圖片
                setEmoji(star, '⭐');
                
                setTimeout(() => {
                    star.remove();
                }, 800);
            }
        }

        function showFeedback(isCorrect) {
            feedbackBox.classList.remove('opacity-0');
            
            if (isCorrect) {
                setEmoji(feedbackText, '✅ 答對了！');
                feedbackText.className = 'text-5xl font-black text-white px-8 py-4 rounded-full shadow-2xl border-4 border-white bg-green-500';
                playCorrectSound();
                createStars();
            } else {
                setEmoji(feedbackText, '❌ 答錯了！');
                feedbackText.className = 'text-5xl font-black text-white px-8 py-4 rounded-full shadow-2xl border-4 border-white bg-red-500';
                playWrongSound();
                // Shake animation for wrong
                itemCard.getAnimations().forEach(anim => anim.cancel());
                itemCard.style.animation = 'none';
                itemCard.animate([
                    { transform: 'translateX(0)' },
                    { transform: 'translateX(-10px)' },
                    { transform: 'translateX(10px)' },
                    { transform: 'translateX(-10px)' },
                    { transform: 'translateX(10px)' },
                    { transform: 'translateX(0)' }
                ], { duration: 400 });
            }

            // Hide feedback after delay
            setTimeout(() => {
                feedbackBox.classList.add('opacity-0');
                
                // 不管答對答錯，都滑出舊卡片，前往下一題
                itemCard.animate([
                    { transform: 'scale(1)', opacity: 1 },
                    { transform: 'scale(0.5) translateY(-200px)', opacity: 0 }
                ], { duration: 300, fill: 'forwards' }).onfinish = () => {
                    checkWinCondition();
                };
            }, 1000);
        }

        function checkWinCondition() {
            if (questionCount >= TOTAL_QUESTIONS) {
                // Play victory tune
                if(correctSynth) {
                    const now = Tone.now();
                    correctSynth.triggerAttackRelease("C5", "8n", now);
                    correctSynth.triggerAttackRelease("D5", "8n", now + 0.2);
                    correctSynth.triggerAttackRelease("E5", "8n", now + 0.4);
                    correctSynth.triggerAttackRelease("F5", "8n", now + 0.6);
                    correctSynth.triggerAttackRelease("G5", "2n", now + 0.8);
                }
                finalScoreDisplay.textContent = currentScore;
                victoryScreen.classList.remove('hidden');
            } else {
                nextQuestion();
            }
        }

        function handleAnswer(selectedType) {
            if (isProcessing || !activeItem) return;
            isProcessing = true;

            const isCorrect = (activeItem.type === selectedType);
            
            if (isCorrect) {
                currentScore++;
                updateScoreDisplay();
            }

            showFeedback(isCorrect);
        }

        startBtn.addEventListener('click', startGame);
        restartBtn.addEventListener('click', startGame);

        btnLiving.addEventListener('click', () => {
            // Visual click feedback
            handleAnswer('living');
        });

        btnNonLiving.addEventListener('click', () => {
            // Visual click feedback
            handleAnswer('nonliving');
        });

        // Add touch support specifically for better mobile feel
        btnLiving.addEventListener('touchstart', (e) => { e.preventDefault(); handleAnswer('living'); });
        btnNonLiving.addEventListener('touchstart', (e) => { e.preventDefault(); handleAnswer('nonliving'); });
        startBtn.addEventListener('touchstart', (e) => { e.preventDefault(); startGame(); });
        restartBtn.addEventListener('touchstart', (e) => { e.preventDefault(); startGame(); });

        // 在遊戲一開始載入時，把介面上所有的按鈕和標題圖案都轉換成圖片
        if (typeof twemoji !== 'undefined') {
            twemoji.parse(document.body, twemojiConfig);
        }

    </script>
</body>
</html>
